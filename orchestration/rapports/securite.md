# Rapports — /securite

> Format : `../_CONTRAT-RAPPORT.md`. Le plus récent en haut. Feu 🟢 `READY_FOR_PUSH` / 🔴 `BLOCKED_*` (R6).
> Registre des vulnérabilités : `../agents/securite/REGISTRE.md` (SEC-###, P0–P3). **Veto Release si P0 ouvert**.

## [2026-07-23] T-002 — Architecture du coffre ZERO-KNOWLEDGE
- **Feu** : 🟢 `READY_FOR_PUSH`
- **Périmètre touché** : `orchestration/agents/securite/ARCHI-ZERO-KNOWLEDGE.md` (nouveau) + ce rapport. **Writer unique respecté** ; `MODELE-MENACE.md`/`REGISTRE.md` **non modifiés** (SEC-### référencés par numéro). Basé sur `origin/atelier` (vague 1 intégrée).
- **Fait** : Spécification d'architecture cryptographique — invariant ZK (serveur ne lit jamais, récupération sans déchiffrement serveur) ; **hiérarchie de clés à enveloppes** (phrase→Argon2id→RootKey→HKDF→KEK→DEK par item, enveloppes `E_*` par facteur/appareil) ; **E2E/AEAD** (XChaCha20-Poly1305 / AES-256-GCM, AAD, padding métadonnées) ; **dérivation client** Argon2id à **plancher codé en dur** + PAKE OPAQUE ; **récupération « zéro perte » 4 facteurs** (phrase, sauvegarde chiffrée, contacts de confiance/Shamir opérateur < t, carte SIM numérique de secours, + codes hors-ligne) **sans escrow** ; **enrôlement multi-appareils** sans secret en clair + **sync chiffrée** (CRDT/LWW, Merkle signé anti-rollback) ; **stockage serveur aveugle** ; **rotation/révocation** (ré-encapsulation KEK, crypto-shredding, fenêtre anti-rançongiciel) ; **mapping des 18 SEC-### P0**.
- **Preuve** : commit sur `claude/tel-online-threat-model-q2cm4s` (`ARCHI-ZERO-KNOWLEDGE.md`) ; **PR draft vers `atelier`**.
- **Tests (R8)** : **humains obligatoires** (revue d'architecture) ; *automatisables* ensuite côté modules : rejet d'un ciphertext altéré (AEAD), refus d'un downgrade KDF, refus d'une version servie ≤ (anti-rollback), preuve d'architecture « aucune clé serveur ».
- **Sécurité** : couvre **par conception 10/18 P0** (SEC-001,002,003,004,011,012,013,016,018 + 015 confidentialité coffre) ; **atténue** SEC-014 (dépend de l'endpoint → Infra) ; **route** les P0 hors périmètre coffre (eSIM 005/006/007 → T-004 ; paiement/IDOR 008/009/010 → T-003 ; plan de contrôle 017 → Infra). **Aucun P0 fermé** (audit non destructif) ⇒ **VETO Release toujours actif (R10)**.
- **Décisions nécessaires (R7)** : (1) **modèle de secret** — reco **Option B : 1 secret maître (phrase) + clé matérielle biométrique** (pas de 2ᵉ secret mémorisé) + Q2 longueur phrase 12/24 mots (reco ≥128 bits) ; (2) seuil Shamir social (reco 2-de-3 ou 3-de-5, opérateur 0 part) ; (3) fenêtre d'annulation 7–30 j / cooldown 24–72 h ; (4) carte SIM secours incluse ou premium.
- **Reste à faire / prompt de reprise** : implémentation conforme par les modules (fermeture des P0 → levée du VETO) ; articuler avec **T-004** (eSIM) qui a T-002 en prérequis.
- **Routage** : `/module-infra` (OPAQUE, HSM/KMS cloisonné, hébergement UE, attestation, signature de code) · `/tickets` (T-003 paiement/KYC-AML) · `/design` (écrans sensibles) · patron (décisions ci-dessus).

## [2026-07-23] T-001 — Modèle de menace & analyse de risques TEL ONLINE
- **Feu** : 🟢 `READY_FOR_PUSH`
- **Périmètre touché** : `orchestration/agents/securite/MODELE-MENACE.md` (nouveau) + `orchestration/agents/securite/REGISTRE.md` (mis à jour) + ce rapport. **Writer unique respecté** (aucune écriture hors `orchestration/agents/securite/` et `rapports/securite.md`).
- **Fait** : Modèle de menace complet — **12 actifs**, **32 surfaces**, **100 menaces STRIDE** (38 P0 / 54 P1 / 8 P2), **10 scénarios** (perte / vol / appareil compromis / brèche serveur / SIM-swap / phishing seed / insider / fraude paiement / transfert appareil / backup OS), **14 hypothèses zero-knowledge** (+ conséquence si violée), **51 exigences dérivées** (E2E/AEAD, dérivation Argon2id, **récupération sans déchiffrement serveur**, auth, eSIM, paiement, sauvegarde, journalisation). **42 SEC-### ouverts** (18 P0, 21 P1, 3 P2). Réconcilié avec `VISION.md`, `_REGLES-COMMUNES.md`, skill `/securite`.
- **Preuve** : commit sur `claude/tel-online-threat-model-q2cm4s` (fichiers `MODELE-MENACE.md` + `REGISTRE.md`) ; **PR draft vers `atelier`** (jamais `main`). Diff = 3 fichiers `securite` uniquement.
- **Tests (R8)** : **humains obligatoires** — la validation d'un modèle de menace est une revue (pas de build/test auto). *Automatisable* ensuite : tests de conformité des exigences (ex. rejet d'un ciphertext altéré, refus d'un downgrade KDF) une fois le code écrit par les modules.
- **Sécurité** : **18 SEC-### P0 ouverts ⇒ VETO Release actif (R10)** jusqu'à résolution. Risque produit dominant : éditeur = opérateur = RSP ⇒ le transfert eSIM **est** la primitive d'un SIM-swap (SMS sur le réseau maison). Zero-knowledge **tenable** mais conditionné au socle en vetos (AEAD, KDF plancher client, aucune récupération lisible serveur, découplage du numéro, SCA à liaison dynamique, cloisonnement ZK/régulé).
- **Décisions nécessaires (R7)** : (1) deux secrets (phrase + mot de passe maître) **ou** un seul kit hardware-backed ? (2) portefeuille = monnaie électronique (EMI/safeguarding, lien **T-003**) ? (3) isolation LI/rétention télco vs coffre ZK (routage **Infra**) ; (4) périmètre **PCI-DSS** / certification **PVID/eIDAS** ; (5) modélisation dédiée du **client web/PWA** (confirmé en périmètre par `VISION.md`).
- **Reste à faire / prompt de reprise** : traiter les **18 P0** avant prod ; intégrer/statuer les **17 angles morts** (`REGISTRE.md` Annexe D), notamment autofill, **port-out téléphonique**, **client web/PWA**, wallet EMI, LI/rétention.
- **Routage** : `/module-infra` (isolation LI, hébergement UE, segmentation, KMS/HSM, client web) · `/tickets` (T-003 KYC/AML) · `/design` (UX des flux sensibles : anti-capture, anti-overlay) · patron (décisions nécessaires ci-dessus).

## [2026-07-23] T-005 — Garde-fous produit → registre SEC + veto
- **Feu** : 🟢 `READY_FOR_PUSH`
- **Périmètre touché** : `orchestration/agents/securite/REGISTRE.md` (section « Garde-fous » + Annexe B). **Writer unique respecté**.
- **Fait** : **24 garde-fous à veto** inscrits, dont les **4 imposés (DoD T-005)** formalisés en règles de veto claires (violation ⇒ **SEC-### P0** + blocage Release) : ❌ OTP 3DS stocké → **SCA/DSP2/passkeys** *(VETO-V1)* · ❌ clone eSIM → **transfert opérateur SM-DP+** *(VETO-V2)* · ❌ mot de passe en clair → **coffre chiffré zero-knowledge** *(VETO-V3)* · ⚠️ flux financier ⇒ **KYC/AML** (lien T-003) *(VETO-V4)*. 20 vetos additionnels (récupération sans backdoor, anti-rollback signé, audit WORM, token binding, anti-MITM, intégrité OTA/CI-CD, anti-IDOR…).
- **Preuve** : commit sur `claude/tel-online-threat-model-q2cm4s` (`REGISTRE.md` §Garde-fous + Annexe B) ; **PR draft vers `atelier`**.
- **Tests (R8)** : **humains obligatoires** (revue de conformité) ; chaque veto est ensuite *automatisable* en contrôle CI côté modules (ex. scan « aucun OTP 3DS stocké », « allowBackup=false », « pas de PAN en logs »).
- **Sécurité** : les 4 garde-fous sont **opposables** aux autres agents (règles de veto). Toute violation ouvre un **P0** ⇒ VETO Release (R10).
- **Décisions nécessaires (R7)** : cadrage **KYC/AML** à finaliser avec la conformité (lien **T-003**).
- **Reste à faire / prompt de reprise** : diffuser les 24 vetos comme règles opposables ; brancher chaque veto sur un contrôle CI côté modules dès que le code existe.
- **Routage** : `/tickets` (T-003 KYC/AML) · `/module-infra` & `/module-*` (implémentation des contrôles) · `/release` (respect du VETO tant qu'un P0 est ouvert).

> **Note publication.** La publication a été momentanément **bloquée** (droit `contents: write` non accordé à l'intégration → `403` sur push et API), puis **débloquée** après octroi de l'accès en écriture : branche poussée + PR draft vers `atelier` ouverte. Branche utilisée : `claude/tel-online-threat-model-q2cm4s` (imposée par le harness ; la convention skill `claude/securite-*` est notée mais non applicable ici).

---
_(J0 — Missions dispatchées **T-001** modèle de menace & risques + **T-005** garde-fous : livrées et rapportées ci-dessus.)_
