# Rapport — Agent MÉMOIRE-PRODUIT (`/memoire-produit`)

> Format imposé par `_CONTRAT-RAPPORT.md` (R6). Journal **anti-chronologique** : l'entrée la plus récente est en **haut**.
> Périmètre (writer unique) : `orchestration/agents/memoire-produit/` (VISION.md, BACKLOG.md, FAIT.md) + ce rapport.

## [2026-07-23] T-008 — Alignement de la mémoire produit sur la vague 1 intégrée
- **Feu** : 🟢 READY_FOR_PUSH
- **Périmètre touché** : `orchestration/agents/memoire-produit/VISION.md`, `.../BACKLOG.md`, `orchestration/rapports/memoire-produit.md` (writer unique respecté). `FAIT.md` inchangé (toujours **vide** — rien de VERIFIED prod).
- **Fait** : réconciliation de la mémoire produit avec le cadrage intégré (Sécurité T-001/T-005, Design T-006, Registre technique T-007) — **sans rien inventer**, chaque ajout **sourcé** :
  - **VISION** : définition produit à **3 piliers** (eSIM de secours ; coffre zero-knowledge ; paiement/wallet freemium) ; clés **dérivées côté client** (phrase de récupération + biométrie) ; **garde-fous produit actés** (T-005, à veto) ; glossaire enrichi (eSIM/GSMA RSP, coffre ZK, phrase de récupération, mot de passe maître, SCA/DSP2, PSP/wallet/EMI, KYC/AML, hébergement UE, SEC-###/veto, canaux officiels) ; **correction D-01/D-02** à la formulation **officielle** (modules définis au cadrage Infra/Sécurité — *pas de clone « BTP » de « Pilotage »* ; stack laissée au cadrage Infra/Sécurité S0–S2 ; matérialisation au **T-004**).
  - **BACKLOG** : freemium rattaché à `/design` (`SPECS-abonnement-freemium`) + PSP SCA/DSP2 ; **15 questions ouvertes routées** vers leurs owners (T-004 ; /securite T-002 & T-003 ; /design ; patron `ETAT.md` §5).
- **Preuve** : commit de cette passe sur `claude/memoire-produit-consolidation-fofrlx` (base `atelier` à jour) + **PR draft `#12` vers `atelier`** (jamais `main`). Diff = mémoire produit uniquement.
- **Tests (R8)** : sans objet (documentation) → **preuve = revue** ; pas de build/test applicable. Cohérence des renvois VISION↔BACKLOG vérifiée.
- **Sécurité** : la mémoire **reflète** l'état `/securite` (**42 SEC-###, 18 P0 ⇒ VETO Release actif**) ; aucun secret ni PII ; **ne modifie pas** le registre SEC (possédé par `/securite`).
- **Décisions nécessaires (R7)** : aucune bloquante côté mémoire (mission menée au bout). Rappel des décisions **patron** en attente (`ETAT.md` §5) : dépôt dédié, partenaires eSIM/paiement/hébergeur UE, « **2 secrets** (phrase + mot de passe maître) **vs 1 kit hardware-backed** », statut **EMI** du portefeuille, référent conformité RGPD/DSP2/KYC-AML.
- **Reste à faire / prompt de reprise** : re-consolider la mémoire après **T-002/T-003/T-004** (nouvelles décisions actées) et à chaque nouvelle **SPEC `/design`** ; convertir en `FAIT.md` uniquement ce qui devient **VERIFIED prod**.
- **Routage** : `/securite` (T-002, T-003) · `/module-infra` (T-004) · `/design` (freemium, récupération) · **patron** (`ETAT.md` §5).

## [2026-07-23] T-008 — Consolidation initiale VISION + glossaire + BACKLOG + FAIT
- **Feu** : 🟢 READY_FOR_PUSH — **livré et intégré** (PR #3 **mergée** dans `atelier`).
- **Périmètre touché** : `orchestration/agents/memoire-produit/` (VISION.md, BACKLOG.md, FAIT.md) + rapport.
- **Fait** : **VISION** (promesse « zéro perte », zero-knowledge + tension récupération traitée honnêtement, garde-fous, glossaire, **décisions actées datées** D-01/D-02) ; **BACKLOG** (roadmap MVP S0–S2 → V1 (~S3–S12) → V2 (S13+), **freemium** gratuit 1 appareil ; ~4,99/9,99/famille ; 15 questions ouvertes) ; **FAIT.md vide** (aucun élément VERIFIED prod).
- **Preuve** : PR **#3** → `atelier`, **mergée** le 2026-07-23 (merged_by patron) ; 4 fichiers, +316. Aucun workflow CI configuré ; aucun commentaire de revue.
- **Tests (R8)** : sans objet (documentation).
- **Sécurité** : promesse zero-knowledge posée ; tension récupération signalée (depuis **confirmée** par SEC-001..004).
- **Décisions nécessaires (R7)** : 15 questions consignées (voir BACKLOG).
- **Reste à faire / prompt de reprise** : — (mission livrée ; suivi = entrée du haut).
- **Routage** : `/tickets` pour tout besoin nouveau.
- **Note environnement** : au 1er passage, dépôt **vide** + session **sans droit d'écriture** (403 git proxy & API) ; **débloqué** après initialisation d'`atelier` par l'orchestrateur, puis publication menée à bien.
