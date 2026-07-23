# TEL ONLINE — Architecture du coffre ZERO-KNOWLEDGE

| | |
|---|---|
| **Réf.** | T-002 (architecture du coffre zero-knowledge) — amont : T-001 🟢 · aval : prérequis T-004 |
| **Agent** | `securite` (writer unique de `orchestration/agents/securite/`) |
| **Version** | 1.0 |
| **Date** | 2026-07-23 |
| **Base** | `origin/atelier` (vague 1 intégrée) |
| **Statut** | Spécification d'architecture — **audit non destructif**, aucun code métier, aucun secret en clair |
| **Entrées** | [`MODELE-MENACE.md`](./MODELE-MENACE.md) · [`REGISTRE.md`](./REGISTRE.md) (SEC-###) · `../memoire-produit/VISION.md` · design `SPECS-onboarding-coffre` / `SPECS-phrase-recuperation` / `SPECS-recuperation-zero-perte` · `../REGISTRE-TECH.md` |

> **Périmètre & non-modification.** Ce document **crée** la spécification d'architecture. Il **ne modifie
> pas** `MODELE-MENACE.md` ni `REGISTRE.md` : les vulnérabilités sont **référencées par numéro** (SEC-###).
> `/module-infra` **contribue** (implémentation serveur/HSM, hébergement UE) via prompt/route mais **ne
> co-écrit pas** ce fichier (writer unique = `/securite`).

---

## 0. Invariant fondateur (non négociable)

> **Le serveur ne peut JAMAIS lire les données du coffre, et la récupération « zéro perte » se fait SANS
> aucun déchiffrement côté serveur.** Toute clé capable d'ouvrir le coffre est dérivée/détenue **côté
> client** ; le serveur ne détient que du **chiffré authentifié opaque** et des **enveloppes de clés
> qu'il ne peut pas ouvrir**. Aucun escrow, aucune backdoor, aucune capacité de recouvrement opérateur.

Cet invariant est la traduction architecturale des hypothèses **H01–H14** du modèle de menace et des
garde-fous **VETO-V3 / VETO-V5** du registre. Il gouverne toutes les décisions ci-dessous.

### Ce que le serveur voit / ne voit jamais
| Le serveur détient (opaque) | Le serveur ne détient JAMAIS |
|---|---|
| Blobs AEAD (items du coffre, sauvegardes) | Le clair (mots de passe, secrets, notes, contacts, photos) |
| UUID d'items, numéro de **version signé**, racine de **Merkle** signée | La **phrase de récupération** / le secret maître |
| Paramètres KDF (algo, coût, **sel**) — publics mais **authentifiés** | La **Root Key**, la **KEK**, les **DEK** |
| **Enveloppes de clé** (KEK wrappée par chaque facteur / appareil) | Une clé permettant d'ouvrir une enveloppe |
| Vérificateur **PAKE (OPAQUE)**, clés publiques d'appareils/gardiens | Un dérivé rejouable du secret maître |
| Métadonnées minimales **rembourrées** (tailles par buckets, timestamps) | Titres, domaines, structure interne en clair |

---

## 1. Modèle de confiance

- **Client (app iOS/Android/desktop/web-PWA)** = zone de confiance où le clair existe **transitoirement**
  en mémoire. C'est le **seul** endroit de déchiffrement. Le maillon réel (hypothèse **H06**) : intégrité
  de l'endpoint (OS + TEE + code livré).
- **Serveur/back-office** = **honnête-mais-curieux, voire hostile ou sous contrainte légale**. On suppose
  qu'il peut être dumpé, rejouer/substituer des blobs, servir une version antérieure, ou être piloté par un
  insider. L'architecture doit rester sûre **malgré** un serveur malveillant (au plus : déni de service).
- **Frontière interne critique (F6 du modèle)** : le **domaine ZK (coffre)** et le **domaine régulé**
  (KYC/AML/paiement, qui doit voir de l'identité en clair) sont **strictement cloisonnés** — aucun partage
  de clé, identifiants pseudonymisés distincts. L'identité régulée ne sert **jamais** de canal de
  corrélation ou de déchiffrement du coffre.
- **Hébergement UE**, moindre privilège, journalisation d'audit **hors** du blob E2E (cf. §10, §11).

---

## 2. Hiérarchie de clés (cœur de l'architecture)

Schéma à **enveloppes** (envelope encryption). Une seule clé symétrique protège réellement un item (sa
**DEK**), et une clé de coffre (**KEK**) protège les DEK. La KEK est **wrappée en parallèle** par chaque
moyen d'ouverture (secret maître, chaque facteur de récupération, chaque appareil) — c'est ce qui permet la
**récupération multi-facteurs sans jamais exposer la KEK au serveur**.

```
  Phrase de récupération (RS)                 [secret maître, généré client, ≥128 bits, BIP39]
        │  Argon2id (sel unique, params plancher) — §3
        ▼
  Root Key (RK, 256 bits)
        │  HKDF-SHA-256 (séparation de domaine)
        ├──► K_auth       → vérificateur PAKE/OPAQUE (auth serveur, jamais rejouable)
        ├──► K_sign        → graine Ed25519 (signatures de version/intention — anti-rollback)
        ├──► K_meta        → chiffrement des métadonnées
        └──► K_wrap(RK)    ─────────────┐
                                        ▼
                             KEK (256 bits, CSPRNG)          ← clé de coffre
                              │  wrappe (AEAD) chaque…
                              ├──► DEK_item_1 (256 bits) ─► AEAD(item_1)
                              ├──► DEK_item_2 ─► AEAD(item_2)
                              └──► …

  ENVELOPPES DE KEK (stockées serveur, opaques) — une par moyen d'ouverture :
     E_phrase   = Wrap(KEK, K_wrap(RK))            ← ouverture par la phrase
     E_appareil = Wrap(KEK, clé publique d'appareil / clé Secure Enclave)   ← §5 déverrouillage quotidien
     E_codes    = Wrap(KEK, clé dérivée des codes de secours hors-ligne)    ← facteur 2 (§4)
     E_social   = Shamir t-de-n de (KEK ou RK'), parts chiffrées par gardien← facteur 3 (§4)
     E_hw       = Wrap(KEK, secret dérivé d'une clé matérielle FIDO2/PRF)   ← facteur 4 (§4)
```

**Propriétés clés :**
- Le serveur stocke `E_*` mais **ne peut ouvrir aucune** (il ne détient aucune clé d'enveloppe).
- **Changement de secret / ajout-retrait de facteur = ré-encapsulation de la KEK** (`E_*` régénérées),
  **sans re-chiffrer les items** (les DEK/KEK ne changent pas) → opération peu coûteuse, adaptée mobile.
- La compromission d'une **DEK** n'expose **qu'un seul item** ; la compromission d'un **facteur** n'ouvre
  la KEK que si l'attaquant possède **ce** facteur (pour le social : **≥ t** parts, jamais détenues par
  l'opérateur).

---

## 3. Chiffrement E2E / AEAD

- **Algorithme.** AEAD moderne : **XChaCha20-Poly1305** (nonce aléatoire ≥192 bits — préféré mobile/web)
  ou **AES-256-GCM** (nonce 96 bits **jamais réutilisé**, compteur monotone vérifié). Modes non
  authentifiés (CBC/CTR/ECB sans MAC) **interdits** (garde-fou VETO-V8).
- **AAD (données additionnelles authentifiées).** Chaque item lie en AAD : `user_pseudonyme`, `item_id`
  stable, **numéro de version monotone**, **compteur global du coffre**. ⇒ anti-substitution, anti-confusion
  inter-comptes, anti-rejeu, anti-réordonnancement (couvre SEC-011).
- **Rejet strict.** Tout ciphertext dont le tag Poly1305/GCM ne vérifie pas est **rejeté** (échec fermé,
  aucun clair partiel). Vérification d'intégrité **avant** tout usage (pas de trust-on-first-use du blob).
- **Métadonnées.** Titres, URL/domaines, usernames, tags, secrets TOTP **chiffrés** (clé `K_meta`). Le
  serveur ne voit qu'un blob + UUID + version. **Padding par buckets** (multiples de 4 KiB) pour brider
  l'inférence par taille (couvre SEC-022 / hypothèse H08).
- **Format d'enregistrement (indicatif).**
  `{ item_id(uuid), version(u64), alg_id, nonce, aad_ref, ciphertext, tag }`, l'ensemble couvert par la
  racine de Merkle signée du coffre (cf. §7).

---

## 4. Dérivation de clé côté client & décision « 2 secrets vs 1 kit »

### 4.1 Fonction de dérivation (KDF)
- **Argon2id** (RFC 9106), **paramètres plancher imposés et codés en dur côté client** :
  `m ≥ 256 MiB` (mobile contraint : `m ≥ 64 MiB` avec `t` compensatoire), `t ≥ 3`, `p ≥ 1`, sortie 256 bits.
  Repli autorisé si Argon2 indisponible : `scrypt (N ≥ 2^17, r=8, p=1)` ou `PBKDF2-HMAC-SHA256 ≥ 600 000`.
  MD5/SHA1 brut et PBKDF2 < 100k **interdits**.
- **Sel** CSPRNG ≥128 bits, **unique par utilisateur**, jamais dérivé d'une valeur prédictible (email/uuid).
- **Anti-downgrade (SEC-013 / VETO-V3).** Les paramètres KDF (algo, `m`, `t`, `p`, sel, version) sont
  **authentifiés** (couverts par l'AAD / signés client) et le client **refuse tout paramètre sous le
  plancher**, **même proposé par le serveur**. Borne haute pour éviter un DoS algorithmique.
- **Authentification au serveur = PAKE (OPAQUE).** Le secret maître n'est **jamais** envoyé, ni sous forme
  de hash rejouable/bruteforçable (hypothèse H05). Le serveur détient un **vérificateur** OPAQUE, pas un
  dérivé du secret.

### 4.2 Déverrouillage quotidien (phrase + biométrie)
Re-saisir la phrase à chaque ouverture est inacceptable (UX grand public, `SPECS-onboarding-coffre`). Donc :
- À l'onboarding, la KEK est aussi wrappée dans une **enveloppe d'appareil** `E_appareil`, protégée par une
  **clé non exportable en Secure Enclave / StrongBox / TEE**, **débloquée par la biométrie liée
  matériellement à la clé** (`setUserAuthenticationRequired` + `invalidatedByBiometricEnrollment`) et/ou un
  **code local**. Pas de biométrie « booléenne » applicative (couvre **SEC-018 / VETO-V13**).
- L'ouverture quotidienne = déblocage de `E_appareil` (biométrie/code) → KEK en mémoire protégée →
  **zeroization** au verrouillage / passage arrière-plan. La **phrase** ne sert qu'à
  l'**enrôlement** et à la **récupération**.

### 4.3 Décision T-001 « deux secrets (phrase + mot de passe maître) vs un kit unique » — **RECOMMANDATION**
**Recommandation : UN seul secret maître = la phrase de récupération**, protégée localement par une **clé
matérielle biométrique** (Secure Enclave/StrongBox) + **code local**, et **reconstituable** via les 4
facteurs de récupération (§5). **Pas de second secret mémorisé indépendant** (mot de passe maître distinct).

Justification :
1. **Aligné produit/design.** `VISION.md` et les SPECS actent la **phrase = secret maître** (HYP-2) et une
   récupération « zéro perte » **multi-facteurs** ; un second secret mémorisé n'y figure pas.
2. **Moins de risque.** Deux secrets à haute entropie indépendants = modèle *wallet crypto* mal adapté au
   grand public : dégrade l'UX, pousse à **photographier/stocker** la phrase (annule FLAG_SECURE, nourrit
   le phishing **SEC-004**) et augmente le **DoS de verrouillage définitif** (SEC-037/038). Déjà signalé en
   sur-ingénierie au **MODELE-MENACE §13**.
3. **Sécurité équivalente.** Le couple *phrase (haute entropie) + clé matérielle biométrique locale* atteint
   le même niveau de garantie ZK, avec une **surface de récupération redondante** (4 facteurs) qui supprime
   le point unique de défaillance.

> **Décision nécessaire (R7) — patron / Produit :** confirmer **Option B (1 secret + clé matérielle)** et
> trancher **Q2 (design)** : longueur de la phrase **12 vs 24 mots** (le compteur design est déjà paramétré
> ICU). Reco sécurité : **≥ 128 bits d'entropie** (24 mots si la liste BIP39 est utilisée, ou 12 mots d'une
> liste ≥ 2048 avec checksum si l'entropie/mot le permet). **Non bloquant** pour cette architecture.

---

## 5. Récupération « zéro perte » SANS déchiffrement serveur (4 facteurs)

Principe : **chaque facteur est une enveloppe indépendante de la KEK** (ou une part d'un partage à seuil).
Le serveur héberge les enveloppes mais **ne peut en ouvrir aucune** ; il n'y a **aucun escrow lisible**
(couvre **SEC-001 / VETO-V5**). Aligné sur `SPECS-recuperation-zero-perte` (Volet A configuration, Volet B
récupération, **anti-énumération** structurante).

| # | Facteur (design) | Mécanisme cryptographique | Le serveur peut-il ouvrir ? |
|---|---|---|---|
| 1 | **Phrase de récupération** (socle, hors ligne) | `RK = Argon2id(phrase)` → ouvre `E_phrase` | ❌ (ne connaît pas la phrase) |
| 2 | **Sauvegarde chiffrée** | Copie du ciphertext + enveloppes ; **s'ouvre avec un autre facteur** (pas de faux sentiment de sécurité) | ❌ |
| 3 | **Contacts de confiance** (récupération sociale) | **Shamir t-de-n** de `RK'`/KEK ; chaque part **chiffrée pour la clé publique du gardien** ; reconstruction **client** | ❌ (opérateur détient **< t** parts, idéalement 0) |
| 4 | **Carte SIM numérique de secours** (canal indépendant) | Canal de **preuve de possession/step-up** indépendant du numéro principal ; **ne porte pas** la clé, il **autorise** l'usage d'un autre facteur (anti-SIM-swap) | ❌ |
| + | **Codes de secours hors-ligne** | Codes CSPRNG haute entropie générés au setup ; `K = KDF(codes)` ouvre `E_codes` ; imprimés/hors-ligne | ❌ |

**Règles transverses (dérivées du modèle & des SPECS) :**
- **Aucune API n'accepte la phrase** ; le support ne la demande **jamais** ; toute saisie en ligne = attaque
  (couvre **SEC-004 / VETO-V15**). Saisie phrase = **écran natif durci** (FLAG_SECURE, anti-overlay,
  auto-complétion **locale sur liste fermée** autorisée, presse-papiers éphémère — cf. SPEC B3).
- **Anti-énumération** : `B1/B2` ne révèlent ni l'existence d'un compte, ni les facteurs configurés, ni le
  nom d'un contact ; la détermination réelle n'a lieu qu'à la **première preuve de possession** (SPEC §9 ;
  couvre SEC-041).
- **Récupération sociale** : les gardiens **aident** (approuvent) mais **n'accèdent jamais seuls** ; parts
  authentifiées avant combinaison ; re-partage possible (révocation d'un gardien, §7).
- **Anti-rançongiciel / step-up** : toute récupération/rotation impose **re-auth phishing-resistant
  (passkey)** + **cooldown 24–72 h** + **notifications hors-bande signées multi-canal** + **gel des
  opérations sensibles** (paiement, transfert eSIM) pendant la fenêtre, avec **annulation** par le
  détenteur légitime (couvre **SEC-002, SEC-003**).

---

## 6. Enrôlement multi-appareils & synchronisation chiffrée

### 6.1 Ajout d'un appareil (aucun secret en clair ne transite)
1. Le nouvel appareil génère une **paire de clés d'appareil** (Secure Enclave/StrongBox, non exportable).
2. Il s'authentifie (**OPAQUE** + **step-up** : approbation depuis un appareil déjà enrôlé **ou** un facteur
   de récupération). Le **numéro/SMS n'est jamais un facteur de confiance** (VETO-V11, anti-SEC-005).
3. La KEK est fournie au nouvel appareil **de deux façons possibles, toutes deux ZK** :
   - **Re-wrap client→client** : un appareil déjà déverrouillé wrappe la KEK vers la **clé publique** du
     nouvel appareil (`E_appareil_new`), relayé **chiffré** par le serveur (qui ne voit rien) ; ou
   - **Dérivation locale** : le nouvel appareil ouvre `E_phrase` avec la phrase (récupération).
4. Le nouvel appareil crée **sa propre** `E_appareil` (biométrie locale). Aucune clé longue durée en clair
   ne transite ; le serveur ne relaie que du chiffré.

### 6.2 Résolution de conflits (sync chiffrée)
- **Granularité item.** Chaque item porte un **id stable** + **version monotone** + horloge logique
  (**Lamport/vector clock**). Le serveur ne voit que des **diffs opaques** ; **toute fusion se fait côté
  client sur le clair**.
- **Stratégie.** **CRDT** (ou **LWW** — *last-writer-wins* — par champ avec horloge logique) pour les items
  indépendants ; **tombstones** pour les suppressions (anti-résurrection). Conflit **sémantique** (même
  champ édité en divergence) → **surfacé à l'utilisateur** (design), jamais résolu silencieusement en
  perdant des données.
- **Intégrité d'ensemble & anti-rollback (SEC-012 / VETO-V9).** L'état global du coffre = **arbre de
  Merkle** dont la racine porte un **numéro de version strictement croissant signé** (`K_sign`, Ed25519).
  Le client mémorise la **dernière version vue** (keystore matériel) et **refuse toute version servie ≤**
  sans preuve d'intention explicite (anti-rollback, anti-troncature, anti-poison-sync).
- **Offline-first.** Édition hors ligne possible ; synchro **mise en file** ; réconciliation au retour du
  réseau (aligné SPECS « hors ligne »).

---

## 7. Rotation & révocation de clés

| Opération | Mécanisme | Coût | SEC couverts |
|---|---|---|---|
| **Rotation du secret maître / phrase** | Nouvelle `RK'` → **ré-encapsulation** `E_phrase` (KEK inchangée) ; ancienne enveloppe conservée pendant une **fenêtre d'annulation 7–30 j** puis purgée | Faible (pas de re-chiffrement d'items) | SEC-003 |
| **Rotation de la KEK** (compromission suspectée) | Nouvelle KEK → **re-wrap** de toutes les DEK + **régénération de toutes les `E_*`** ; option de rotation des DEK | Moyen | SEC-016, SEC-020 |
| **Révocation d'appareil** | Suppression de `E_appareil` + révocation session/clé d'appareil ; **rotation KEK** si compromission plausible | Faible→Moyen | SEC-019, SEC-020 |
| **Révocation d'un gardien** (social) | **Re-partage Shamir** (nouvelles parts) ; anciennes parts invalidées | Faible | SEC-001 |
| **Révocation d'un facteur** | Suppression de l'enveloppe du facteur (`E_codes` / `E_hw` …) | Faible | — |
| **Effacement RGPD (droit à l'oubli)** | **Crypto-shredding** : destruction des clés → ciphertext définitivement illisible (concilie immuabilité WORM et Art. 17) | Faible | cf. angle mort RGPD (REGISTRE Annexe D) |

**Garde-fous de rotation :** toute rotation destructrice exige **step-up phishing-resistant + confirmation
hors-bande + inscription dans le journal d'audit append-only inviolable (WORM, hors blob E2E)** — VETO-V9 /
VETO-V20. La fenêtre d'annulation neutralise le **rançongiciel de compte** (SEC-003).

---

## 8. Modèle de stockage serveur aveugle

- **Le serveur ne détient aucune clé de déchiffrement.** Il stocke : blobs AEAD, UUID, versions signées,
  racine Merkle signée, **enveloppes `E_*`** (opaques), paramètres KDF **authentifiés**, vérificateur
  OPAQUE, clés **publiques** d'appareils/gardiens, métadonnées rembourrées.
- **Insider / brèche (SEC-016 / VETO-V20).** Un dump total de la base **ne permet aucun déchiffrement du
  coffre** (aucune clé présente). Les **stores régulés non-ZK** (KYC/AML/paiement) — hors coffre — sont
  protégés séparément : **KMS/HSM cloisonné hors du périmètre DB**, tokenisation PAN, moindre privilège,
  accès JIT audité (renvoi `/module-infra`).
- **Anti-substitution/rejeu par le serveur (SEC-011/012).** L'AAD + la signature de version + la racine
  Merkle empêchent un serveur malveillant de mélanger, rejouer ou revenir en arrière sans détection client.
- **Journal d'audit** append-only **WORM**, chaîné par hachage, **hors du blob E2E**, horodatage serveur de
  confiance (jamais l'horloge client) — couvre SEC-024, VETO-V20. **Aucun secret/PII dans les logs** (VETO-V7).
- **Hébergement UE**, minimisation, rétention par catégorie (crypto-shredding pour l'effaçable, rétention
  légale distincte pour AML/télco).

---

## 9. Couverture des SEC-### P0 (mapping)

| SEC-### (P0) | Traitement par l'architecture ZK | Statut |
|---|---|---|
| **SEC-001** récupération cassant le ZK (escrow/fallback lisible) | Récupération 100 % client, aucun escrow ; social **opérateur < t** parts (§5) | ✅ Couvert |
| **SEC-002** altération / re-clé du blob de récupération | Enveloppes **AEAD + signées** client, ancrées au secret ; step-up avant usage (§5, §7) | ✅ Couvert |
| **SEC-003** rotation malveillante (rançongiciel) | Step-up + cooldown + **fenêtre d'annulation** + WORM (§7) | ✅ Couvert |
| **SEC-004** phishing de la phrase | **Aucune API/serveur/support n'accepte la phrase** ; écran natif durci (§5) | ✅ Couvert (archi) |
| **SEC-011** coffre sans AEAD | **AEAD obligatoire** + AAD + rejet strict (§3) | ✅ Couvert |
| **SEC-012** rollback / troncature / poison-sync | Version monotone **signée** + **Merkle** + refus ≤ (§6.2) | ✅ Couvert |
| **SEC-013** downgrade des paramètres KDF | Paramètres **authentifiés** + **plancher codé en dur** (§4.1) | ✅ Couvert |
| **SEC-016** insider DB + KMS | **Serveur sans aucune clé** ; KMS régulé cloisonné (§8) | ✅ Couvert (coffre) |
| **SEC-018** biométrie non liée à la clé | Clé en **Secure Enclave/StrongBox**, biométrie **liée matériellement** (§4.2) | ✅ Couvert |
| **SEC-015** impersonation admin (lecture coffre) | Le serveur/admin **ne peut pas lire** le coffre (ZK) ; minting de session = plan de contrôle → renvoi | ✅ Coffre / ↪ renvoi |
| **SEC-014** client malveillant (OTA/CI-CD) | Hors périmètre crypto pur (dépend de l'endpoint, H06/H07) : **atténué** par signature de code + builds reproductibles + attestation + config non pilotant KDF/pinning (VETO-V21) | ⚠️ Atténué ↪ `/module-infra` |
| **SEC-005/006/007** eSIM = SIM-swap | Hors coffre. L'archi **décloue le numéro** (aucun facteur SMS, VETO-V11) ; le reste relève de l'**architecture eSIM** | ↪ Renvoi **T-004** |
| **SEC-008/009/010** paiement, SCA, IDOR | Hors coffre (domaine régulé) ; **cloisonnement F6** garanti (§1, §8) | ↪ Renvoi paiement (T-003) |
| **SEC-017** confusion d'algorithme jeton | Plan de contrôle / auth serveur | ↪ Renvoi `/module-infra` |

> **Synthèse :** l'architecture **couvre par conception 10 des 18 P0** (001, 002, 003, 004, 011, 012, 013,
> 016, 018, + 015 pour la confidentialité du coffre), **atténue** SEC-014 (dépendance endpoint), et **route
> explicitement** les P0 hors périmètre coffre (eSIM T-004, paiement/IDOR T-003, plan de contrôle Infra).
> **Aucun P0 n'est fermé ici** (audit non destructif, pas de code) : le VETO Release (R10) reste actif ; ces
> constats se résoudront à l'implémentation conforme, vérifiée par QA.

---

## 10. Contraintes de conformité & pérennité

- **RGPD** : minimisation, **hébergement UE**, **crypto-shredding** pour le droit à l'effacement (concilie
  WORM/immuabilité), DPIA (biométrie liveness — hors coffre). Le ZK correct est un facteur atténuant Art. 34.
- **Agilité cryptographique / post-quantique.** L'`alg_id` versionné dans l'AAD permet la migration ; prévoir
  une **trajectoire d'hybridation KEM post-quantique** pour l'encapsulation des clés (moisson-différée sur
  des secrets à durée de vie décennale) — cf. angle mort PQC du registre.
- **Canaux officiels uniquement** (VISION) ; **moindre privilège** ; **séparation des rôles** côté serveur.

---

## 11. Décisions nécessaires (R7) & renvois

**Décisions patron / Produit (non bloquantes) :**
1. **Modèle de secret** : confirmer **Option B — 1 secret maître (phrase) + clé matérielle biométrique**
   (reco §4.3), et **Q2** longueur de phrase (12 vs 24 mots ; reco ≥ 128 bits).
2. **Paramètres de récupération sociale** : seuil **t-de-n** par défaut (reco **2-de-3** ou **3-de-5**),
   opérateur détenant **0** part.
3. **Fenêtre d'annulation** de rotation (reco **7–30 j**) et **cooldown** de récupération (reco **24–72 h**).
4. **Carte SIM numérique de secours** incluse ou premium (Q4 design) — impacte la disponibilité d'un facteur.

**Renvois (routage) :**
- `/module-infra` : implémentation OPAQUE, HSM/KMS cloisonné, hébergement UE, attestation, signature de code
  (SEC-014, SEC-016 régulé, SEC-017) ; **contribue** à cette archi via prompt, ne co-écrit pas ce fichier.
- **T-004** (eSIM) : SEC-005/006/007, carte SIM numérique de secours comme facteur.
- **T-003** (paiement/KYC-AML) : SEC-008/009/010, cloisonnement domaine régulé.
- `/design` : cohérence des écrans sensibles (anti-capture, anti-overlay, anti-énumération).

---

## 12. Références

- Modèle de menace : [`MODELE-MENACE.md`](./MODELE-MENACE.md) (§3 actifs, §5 hypothèses ZK, §6 surfaces,
  §7 STRIDE, §9 exigences).
- Registre : [`REGISTRE.md`](./REGISTRE.md) (SEC-001…042, garde-fous VETO-V1…V24, échelle P0–P3).
- Vision : `../memoire-produit/VISION.md`.
- Design : `../design/SPECS-onboarding-coffre.md`, `../design/SPECS-phrase-recuperation.md`,
  `../design/SPECS-recuperation-zero-perte.md`.
- Registre technique (noms d'accès/outils, **aucun secret en clair**) : `../REGISTRE-TECH.md`.

*Fin de la spécification d'architecture zero-knowledge (T-002). Le suivi des risques reste dans
[`REGISTRE.md`](./REGISTRE.md) ; VETO Release actif tant qu'un P0 est ouvert (R10).*
