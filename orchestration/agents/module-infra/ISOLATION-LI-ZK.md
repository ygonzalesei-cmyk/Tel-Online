# ISOLATION-LI-ZK.md — Cloisonnement « télécom régulé » vs « coffre zero-knowledge » + socle infra UE (T-009)

> **Writer unique : `/module-infra`.** Spécification d'**architecture d'infrastructure** (on spécifie, on ne code pas).
> **Séparé de `REGISTRE-TECH.md`** (qui liste les NOMS d'accès) : ce document spécifie l'**isolation** et le **socle infra**, sans aucune valeur ni secret.
> **Entrées lues (lecture seule)** : `../securite/MODELE-MENACE.md` (frontières F1–F7, hypothèses H01–H14, SEC-###), `../securite/REGISTRE.md` (SEC-### + garde-fous VETO-V##), `../REGISTRE-TECH.md`. **Je ne co-écris pas les fichiers de la Sécurité** — je les référence par ID.
> **Origine (routage)** : Sécurité (T-001), Annexe D.3 : *« Isolation LI/rétention télco vs coffre ZK : frontière à prouver → Infra »* et *« Client web/PWA : modélisation dédiée → Infra »*. Angles morts P0 associés : D.1 « LI/rétention vs ZK » et « port-out / renvoi d'appel ».

---

## 0. Principe directeur

TEL ONLINE fait cohabiter, dans un même backend **opéré par l'opérateur télécom lui-même**, deux mondes **antagonistes** :

- **Domaine ZK** (coffre) — le serveur ne doit **RIEN** pouvoir lire (H01, H02).
- **Domaine régulé** — le serveur **DOIT** légalement traiter en clair : KYC/AML, paiement (DSP2), **et** l'**interception légale (LI) + rétention** de métadonnées télécom.

**Règle d'or** : cloisonnement **fort et deny-by-default**. Aucune clé, aucun identifiant réutilisable, aucune corrélation ne franchit la frontière **F6**. **Le plan LI/rétention n'atteint jamais le coffre.** Le **numéro de téléphone n'est jamais un facteur de confiance**. Tout en **UE**, sous **moindre privilège**.

> Ce document **ne réduit pas** la surface cryptographique du coffre (périmètre Sécurité) : il garantit que **l'infrastructure** ne crée aucun chemin qui contournerait le zero-knowledge par la « porte régulée » (H10) ou par la « porte télécom » (LI/rétention, port-out).

---

## 1. Zones d'isolation (segmentation logique, réseau & tenants)

| Zone | Contient | Traite en clair ? | Ne contient JAMAIS | SEC-### adressés |
|---|---|---|---|---|
| **Z-ZK** — Coffre zero-knowledge | Blobs AEAD opaques, métadonnées chiffrées, verificateur PAKE, versions signées | **Non** (ciphertext uniquement) | Clé de déchiffrement utilisateur, seed, mot de passe maître, KYC, PAN | SEC-011, SEC-012, SEC-013, SEC-022 |
| **Z-REG** — KYC / AML / Paiement | Identité vérifiée, documents KYC, ledger portefeuille, tokens PAN, décisions AML | **Oui** (obligation légale) | Toute clé du coffre, tout secret client ZK | SEC-008, SEC-009, SEC-010, SEC-032, SEC-033 |
| **Z-LI** — Interception légale & rétention télécom | Métadonnées de trafic à rétention ciblée, capacité LI voix/SMS/data (infra mandatée) | **Oui** (mandat légal) | **Aucun chemin vers Z-ZK**, aucune clé de coffre | SEC-016 (insider), SEC-023 |
| **Z-ADMIN** — Plan de contrôle | Consoles, orchestration, break-glass, CI/CD, secrets d'exploitation | n/a | Capacité « se connecter en tant que » utilisateur, capacité de déchiffrer le coffre | SEC-014, SEC-015, SEC-017 |
| **Z-EDGE** — API / Edge / Client web-PWA | Terminaison TLS, WAF, service du client web/PWA, passerelle API | Transitoire | Logique métier régulée, clés | SEC-021, SEC-036 |
| **Z-OBS** — Observabilité | Logs WORM caviardés, métriques sans PII, audit append-only | Non (redigé) | Jetons, seed, KDF, PAN, KYC, payloads webhook | SEC-023, SEC-024 |

**Contrôles transverses de segmentation** : micro-segmentation **deny-by-default**, **allowlist d'egress** par zone, **anti-SSRF** (validation d'URL, blocage des endpoints métadonnées cloud), **KMS/HSM isolé** de tout service exposé (**SEC-036**). Aucune latéralisation Z-EDGE → Z-ADMIN/KMS.

---

## 2. Frontière F6 — Coffre ZK ⟷ Régulé (la frontière interne critique)

Reprend **F6** et **H10** du modèle de menace. Exigences d'infrastructure :

1. **Bases et réseaux distincts** par domaine ; **aucun flux de clé** entre Z-ZK et Z-REG/Z-LI (H10, VETO-V20).
2. **Pseudonymes séparés** de part et d'autre : l'identifiant utilisateur du coffre **≠** l'identifiant KYC/paiement (aucune réutilisation d'ID comme canal de corrélation ou de déchiffrement).
3. **Aucune capacité de déchiffrement opérateur** du coffre (VETO-V3, VETO-V5, **SEC-001**) : la récupération est 100 % côté client (seed) ; pas d'escrow, pas de fallback support lisible.
4. **Autorisation objet-par-objet** (ownership/ABAC, deny-by-default) sur toute ressource régulée pour empêcher l'IDOR/BOLA inter-tenant (**SEC-010**, VETO-V17).
5. **Intégrité du coffre** garantie hors serveur : AEAD + versionnement signé client (**SEC-011/012/013**, VETO-V8/V9) — l'infra fournit un **stockage versionné immuable** mais **ne détient pas** les clés.

---

## 3. Cloisonnement LI / rétention télécom **HORS** du coffre ZK

**Contexte réglementaire** (post-CJEU *La Quadrature/Tele2*, cf. `REGISTRE.md` Annexe C & angle mort D.1 P0) : l'opérateur est soumis à une **rétention ciblée des métadonnées de trafic** et à une **capacité d'interception légale (LI)** voix/SMS/data. Ces infrastructures **mandatées** sont des **surfaces d'attaque** et des **vecteurs insider**.

**Spécification d'isolation :**
1. **Zone Z-LI dédiée**, réseau et tenant **séparés** de Z-ZK et Z-REG ; accès strictement **mandaté**, tracé en **WORM** (VETO-V20).
2. **Preuve de non-atteignabilité du coffre par la LI** : le coffre ne contient que du **ciphertext chiffré côté client** ; **aucune clé serveur** n'existe (H01/H02) → une réquisition LI sur l'infra réseau **ne peut** rendre le clair du coffre. La frontière est **documentée et opposable** (transparence).
3. **Z-LI incluse dans le modèle insider** : un opérateur/administrateur de Z-LI n'a **aucun** chemin (réseau, clé, identifiant) vers Z-ZK (**SEC-016**).
4. **Journalisation caviardée** : aucun secret/PII de Z-ZK ne transite ni n'est journalisé dans Z-LI/Z-OBS (**SEC-023**, VETO-V7).
5. **Matrice de notifiabilité de violation** (RGPD Art. 33/34) : le ciphertext ZK correctement chiffré est **non notifiable** ; les stores Z-REG/Z-LI **le sont** — matrice à tenir par catégorie.

> **Portée à confirmer patron** : le périmètre exact des obligations LI/rétention (FR/UE) relève de la **conformité** (T-003) ; l'infra pose ici la **frontière architecturale**, pas le détail juridique.

---

## 4. Segmentation réseau & modèle multi-tenant

- **Micro-segmentation** par zone (§1), **deny-by-default** inter-zone, **allowlist d'egress** explicite ; **protection SSRF** et **isolation du KMS** vis-à-vis de tout service exposé (**SEC-036**).
- **Plan d'administration (Z-ADMIN) isolé** : accès **JIT**, **MFA phishing-resistant**, **dual-control** sur actions sensibles, **suppression de toute capacité « se connecter en tant que »** utilisateur, journaux **WORM externalisés** (**SEC-015**, VETO-V20).
- **Autorisation de service** : algorithme de jeton **figé côté serveur**, rôles résolus depuis l'état serveur (jamais depuis les claims client) pour bloquer la forge de tenant/rôle (**SEC-017**, VETO-V22).
- **Tenancy** : isolation logique par tenant **et** par domaine ; identifiants **non énumérables** (UUID) ; pas de latéralisation inter-tenant.

---

## 5. KMS / HSM — clés d'**INFRA** uniquement (jamais les clés utilisateur)

**Le KMS/HSM protège les clés d'infrastructure**, pas le coffre :

| Usage KMS/HSM (clés d'INFRA) | Actif lié | Contrôle |
|---|---|---|
| Signature de code / artefacts / OTA | A12 | HSM, séparation des rôles (**SEC-014**, VETO-V21) |
| Clés RSP eSIM (SM-DP+) | A04, A12 | HSM, accréditation SAS-SM visée |
| Certificats TLS / pinning first-party | A12 | Rotation gouvernée (**SEC-021**, VETO-V12) |
| **Enveloppe au repos** des stores **régulés** (Z-REG/Z-LI) | A07, A09 | Enveloppe + contrôle d'accès **distinct de la DB** |
| Clés de signature d'audit WORM / reçus | A12 | HSM, append-only (VETO-V20) |

**Règles d'isolation (SEC-016, VETO-V7) :** KMS/HSM **hors du périmètre DB et backups** ; **quorum** + **séparation des rôles** ; accès **JIT audité** ; un snapshot DB ou un backup volé **seul** ne déchiffre **rien**.

> ⚠️ **Garde-fou ZK (non négociable)** : le KMS/HSM **ne détient jamais** de clé capable de **déchiffrer le coffre utilisateur**. Ces clés (KEK/DEK) sont **dérivées côté client** (seed + biométrie matérielle — H01/H03, VETO-V3) et **n'atteignent jamais le serveur**. **Aucune capacité de déchiffrement opérateur** (VETO-V5).
>
> Les **NOMS** des variables/références (ex. `KMS_KEY_REF`, `VAULT_*`) et leurs emplacements vivent dans **`../REGISTRE-TECH.md`** — **jamais de valeur** ici (R11).

---

## 6. Découplage du numéro de téléphone ⟷ identité du coffre (anti SIM-swap)

Reprend **H11**, **SEC-005/006/007** et **VETO-V11/V2**.

1. **Le MSISDN / numéro n'est JAMAIS** un facteur d'authentification, de récupération, de SCA ni de 2FA (**VETO-V11**). **Aucun repli SMS/voix.**
2. **Identité du coffre = secret client** (seed + biométrie matérielle) — **sans aucun lien** avec la ligne mobile : voler le numéro **ne donne pas** le coffre, et compromettre le coffre **ne donne pas** la ligne.
3. **eSIM = transfert opérateur** (SM-DP+, **profil unique, pas de clone** — **VETO-V2**) sous **step-up phishing-resistant + cooldown + gel financier post-transfert + preuve d'intention signée** (**SEC-005/006/007**, VETO-V19).
4. **Élargissement aux autres chemins télécom SIM-swap** (angle mort D.1 P0) : **verrou de port-out** (PIN + gel + notification hors-bande + cooldown) et **monitoring du renvoi d'appel / messagerie vocale** avec notification hors-bande. L'infra traite **port-out** et **call-forwarding** comme des chemins de SIM-swap **au même titre** que le transfert eSIM.
5. **Jetons liés à l'appareil** (DPoP/attestation, **VETO-V10**) pour que le vol du numéro ne porte pas une session.

---

## 7. Client web / PWA — surface de première classe

**Confirmé en périmètre** (VISION multi-plateforme ; `REGISTRE.md` note VISION & angle mort D.1 P1). Le **crypto livré en JS** par le serveur est une surface **supply-chain/XSS** **plus grave** que l'OTA mobile (un serveur compromis pousse du JS capturant le clair au déverrouillage, **sans** signature de store).

**Contrôles d'infrastructure :**
- **CSP stricte** + **SRI** (intégrité des sous-ressources) ; **en-têtes de sécurité** (HSTS, COOP/COEP, X-Frame-Options).
- **Intégrité du code client web** : bundles **hashés et publiés** (transparence) ; **domaine dédié** servant le client crypto, isolé du contenu applicatif ; **isolation d'origine** stricte, **pas de bridge** vers du contenu tiers.
- **Même invariant ZK** : la **dérivation de clé s'exécute côté navigateur** ; le serveur ne voit que du **chiffré** (H01).
- **Sessions liées** (token binding / DPoP, **VETO-V10**) ; **PKCE** pour OAuth (VETO-V24) ; **WebView tierce isolée** sans pont natif (VETO-V23).
- **SEC-### liés** : SEC-014 (livraison de client malveillant), SEC-021 (MITM), SEC-028 (WebView).

> **Co-spécification** : l'**UX des flux sensibles** web/PWA relève de `/design` (routage Annexe D.3) ; l'infra fournit le socle de sécurité de livraison.

---

## 8. Robustesse infra : écritures atomiques, rate-limit, sauvegardes

- **Écritures atomiques** + **versionnement monotone signé client** + **anti-rollback / anti-troncature** (**SEC-012**, VETO-V9) ; **idempotence** des ordres sensibles (clé d'idempotence, anti-rejeu — **SEC-008/032**, VETO-V18).
- **Rate-limit par identité légitime** (et non par ressource) pour éviter le **DoS ciblé** des parcours critiques — lockout de récupération, épuisement SM-DP+, gel AML induit, quota de sauvegarde (**SEC-037/038**). **Déverrouillage du coffre 100 % offline** (aucun couplage déverrouillage ↔ serveur ; **SEC-037**).
- **Sauvegardes** : **chiffrées, versionnées, signées**, en **région UE**, **cloisonnées hors du périmètre KMS** (**SEC-016**) ; **exclusion des backups OS** pour le coffre/seed/paramètres KDF (**VETO-V16**) ; **tests de restauration** non destructifs ; **santé/version** exposées.
- **Rétention & effacement** : concilier **WORM immuable** (VETO-V9/V20) et **droit à l'effacement** (RGPD Art. 17) via **pseudonymisation + crypto-shredding** ; **durées par catégorie** distinguant données à **rétention légale** (télécom/AML) et **effaçables** (angle mort D.1 P1).

---

## 9. Hébergement UE & moindre privilège (transverse)

- **Toutes les zones en région UE** (RGPD, résidence des données). **DPA** avec chaque sous-processeur (PSP, KYC, cloud, SM-DP+, push) ; **transferts hors-UE encadrés** (Schrems II / SCC) — **minimiser les payloads push** vers Apple/Google (US).
- **Moindre privilège** partout : **JIT**, **séparation des tâches**, **break-glass à double contrôle** tracé, **audit WORM** (VETO-V20).
- **Aucun secret en clair** dans le dépôt : **NOMS uniquement**, valeurs en **coffre de secrets UE** (R11, cf. `REGISTRE-TECH.md`).

---

## 10. Mapping des constats Sécurité couverts (SEC-### — writer : `/securite`)

> Référencés par **ID** (aucune modification des fichiers Sécurité). « Traité infra » = l'architecture d'isolation **contribue** à la mitigation ; la résolution/clôture reste du ressort de `/securite` (levée du VETO R10).

| SEC-### | Gravité | Sujet (abrégé) | Contribution infra (ce document) | § |
|---|---|---|---|---|
| SEC-001 | P0 | Récupération cassant le ZK (escrow/fallback) | Aucune clé/chemin serveur de déchiffrement ; F6 étanche | §2, §5 |
| SEC-005 | P0 | Transfert eSIM = SIM-swap | Découplage numéro ⟷ coffre ; step-up/cooldown/gel | §6 |
| SEC-006 | P0 | Transfert sans preuve d'intention signée | Signature d'intention matérielle exigée avant exécution | §6 |
| SEC-007 | P0 | Altération requête/code d'activation eSIM | Intégrité signée + pinning SM-DP+ (KMS/HSM) | §5, §6 |
| SEC-010 | P0 | IDOR/BOLA (PAN/CVV, KYC, eSIM, wallet) | Autorisation objet-par-objet deny-by-default, tenants | §2, §4 |
| SEC-011 | P0 | Coffre sans AEAD | Stockage n'accepte que blobs AEAD versionnés | §2 |
| SEC-012 | P0 | Rollback/troncature du coffre | Écritures atomiques + versions immuables signées | §2, §8 |
| SEC-013 | P0 | Downgrade KDF servi par serveur | Paramètres KDF non dictés par l'infra (côté client) | §2, §5 |
| SEC-014 | P0 | Livraison client malveillant (OTA/CI-CD/web) | CI/CD durci, signature, intégrité web (CSP/SRI) | §5, §7 |
| SEC-015 | P0 | ATO console admin | Z-ADMIN isolée, JIT, dual-control, WORM | §4 |
| SEC-016 | P0 | Insider DB+KMS co-localisés | **KMS/HSM hors périmètre DB/backups**, quorum | §3, §5, §8 |
| SEC-017 | P0 | Escalade via jeton (alg/role forgé) | Algo figé serveur, rôles résolus serveur | §4 |
| SEC-018 | P0 | Biométrie non liée au matériel | (client/OS) — infra n'introduit aucun contournement | §6 |
| SEC-021 | P1 | MITM (backend / SM-DP+) | Pinning first-party + SM-DP+, TLS strict | §5, §7 |
| SEC-023 | P1 | Logs fuyant jetons/KYC/PAN/métadonnées | Z-OBS caviardée, aucun clair Z-ZK journalisé | §1, §3 |
| SEC-036 | P1 | SSRF / absence de segmentation → KMS | Micro-segmentation, anti-SSRF, KMS isolé | §1, §4 |
| SEC-037 | P1 | DoS disponibilité coffre (couplage serveur) | Déverrouillage offline, compteur local | §8 |
| SEC-038 | P1 | DoS parcours critiques | Rate-limit par identité, alternatives hors-bande | §8 |

> **Angles morts P0/P1 (Annexe D.1) adressés** : *LI/rétention vs ZK* (§3), *port-out / renvoi d'appel* (§6), *client web/PWA* (§7), *rétention vs effacement WORM* (§8).

---

## 11. Décisions nécessaires (R7 — n'ont pas bloqué la mission)

- **Périmètre LI/rétention** (FR/UE) : obligations exactes et durées → **conformité / T-003** ; l'infra a posé la frontière, le juridique la borne.
- **Hébergeur UE & modèle de tenancy** : dépend du choix patron (cf. `ETAT.md` §5) ; le présent découpage en zones est **indépendant du fournisseur**.
- **Web/PWA** : co-spécification de l'**UX des flux sensibles** avec `/design`.
- **Portefeuille = monnaie électronique (EMI)** : impacte le cloisonnement du **ledger** (comptes ségrégués) → lien **T-003**.
- **Double secret vs kit unique** (récupération) : arbitrage produit/Sécurité — sans impact sur le cloisonnement posé ici.

---

## 12. Traçabilité & périmètre

- **Writer unique** : `/module-infra`. **Fichier créé** : `orchestration/agents/module-infra/ISOLATION-LI-ZK.md`. **Séparé** de `REGISTRE-TECH.md` (NOMS d'accès).
- **Lu (lecture seule, non modifié)** : `../securite/MODELE-MENACE.md`, `../securite/REGISTRE.md`, `../REGISTRE-TECH.md`, `memoire-produit/VISION.md`.
- **Hors périmètre de ce document** : détail cryptographique du coffre et clôture des SEC-### (**/securite**) ; UX (**/design**) ; agrément EMI & cadrage juridique (**T-003 / conformité**) ; définition des modules métier & stack (**T-004**).
- **Canaux officiels uniquement · hébergement UE · aucun secret en clair (NOMS) · jamais `main`.**
