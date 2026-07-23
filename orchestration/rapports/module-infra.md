# Rapports — /module-infra

> Format : `../_CONTRAT-RAPPORT.md`. Le plus récent en haut. Feu 🟢 `READY_FOR_PUSH` / 🔴 `BLOCKED_*` (R6).
> Périmètre infra (déploiement, serveur, sauvegardes, auth/JWT, robustesse). Writer désigné de `../agents/REGISTRE-TECH.md`.

## [2026-07-23] T-009 — Isolation « télécom régulé » vs coffre zero-knowledge + socle infra UE
- **Feu** : 🟢 `READY_FOR_PUSH`
- **Périmètre touché** : `orchestration/agents/module-infra/ISOLATION-LI-ZK.md` (writer unique `/module-infra`, **fichier créé**, **séparé** de `REGISTRE-TECH.md`) + le présent rapport. **Lecture seule** de `securite/MODELE-MENACE.md`, `securite/REGISTRE.md`, `REGISTRE-TECH.md` — **fichiers Sécurité non modifiés** (SEC-### / VETO-V## référencés par ID).
- **Fait** : spécification d'isolation **routée par la Sécurité** (T-001, Annexe D.3) —
  - **Zones** Z-ZK / Z-REG / Z-LI / Z-ADMIN / Z-EDGE / Z-OBS + **frontière F6 étanche** (aucune clé ni corrélation ne la franchit) ;
  - **Cloisonnement LI/rétention télécom HORS coffre ZK** + preuve de non-atteignabilité du vault ; Z-LI incluse dans le modèle insider ;
  - **Segmentation réseau/tenants** (anti-SSRF, **KMS isolé**), plan admin **JIT / dual-control / WORM** ;
  - **KMS/HSM pour clés d'INFRA uniquement** (jamais les clés utilisateur, dérivées côté client ; aucune capacité opérateur) ;
  - **Découplage numéro ⟷ identité du coffre** (anti SIM-swap) + **port-out / renvoi d'appel** ; eSIM = transfert (pas clone) ;
  - **Client web/PWA** (CSP/SRI/intégrité de code) ; **écritures atomiques**, **rate-limit par identité**, **sauvegardes UE cloisonnées** ;
  - **Mapping SEC-### P0/P1** (SEC-001, 005–007, 010–018, 021, 023, 036–038) + angles morts D.1 (LI, port-out, web/PWA).
- **Preuve** (R1) : branche `claude/module-infra-bm5d9p` · commit spec `7fad422` · **PR draft #9** → `atelier` : https://github.com/ygonzalesei-cmyk/Tel-Online/pull/9 · scan anti-secret **négatif**.
- **Tests (R8)** : *automatisables* → lint Markdown + scan « aucun secret » (**négatif**) ; *semi-auto* → revue croisée `/securite` (cohérence SEC-### / VETO) ; *humains obligatoires* → **validation patron** (périmètre LI/rétention, hébergeur, tenancy) ; *preuve manquante* → aucune (doc).
- **Sécurité** : **renforce le zero-knowledge** (aucune capacité de déchiffrement serveur/opérateur ; F6 étanche ; **LI isolée du vault**). Contribue à la mitigation de **18 SEC-###** (dont 13 P0) **sans les clore** (clôture = `/securite`, levée du VETO R10). **Aucun SEC-### nouveau** ouvert.
- **Décisions nécessaires (R7)** — *n'ont pas arrêté la mission* : périmètre exact **LI/rétention** (conformité, T-003) ; **hébergeur UE & tenancy** (ETAT §5) ; **UX web/PWA** (`/design`) ; **portefeuille = EMI** (T-003).
- **Reste à faire / prompt de reprise** : affiner la frontière LI une fois le cadrage **T-003** posé ; **co-spéc web/PWA** avec `/design` ; compléter au **T-004** (modules + stack).
- **Routage** : `/securite` (cohérence modèle de menace / VETO) · `/design` (UX flux sensibles web/PWA) · `/tickets` (suivi T-009) · `/release` (merge après gardien R10 + GO patron).

## [2026-07-23] T-007 — Registre technique initial (canaux officiels, NOMS uniquement)
- **Feu** : 🟢 `READY_FOR_PUSH`
- **Périmètre touché** : `orchestration/agents/REGISTRE-TECH.md` (writer unique `/module-infra`) + le présent rapport. **Un seul writer respecté** ; aucune écriture hors périmètre (lecture seule de `VISION.md` et `securite/REGISTRE.md`).
- **Fait** : registre rempli — intégrations cibles via **canaux officiels**, **NOMS** de variables uniquement :
  - Comptes **OAuth/OIDC** (Google, Apple ; autres à cadrer) ;
  - **eSIM de secours** — opérateur partenaire par **TRANSFERT** (**GSMA SGP.22/SGP.32**), **jamais clone** ; `EID/ICCID/activation code` = données **runtime**, pas des secrets ;
  - **Paiements SCA/DSP2** — PSP (3DS2 délégué, **aucun OTP bancaire stocké**), Apple Pay / Google Pay, open banking **eIDAS** (option) ;
  - **Auth/JWT** (JWKS) + **KMS** au repos, garde-fou **zero-knowledge** explicité (*aucune clé serveur ne déchiffre les données utilisateur*) ;
  - **Hébergement & données UE** (cloud, coffre secrets, BDD, stockage objet, e-mail/SMS).
  Chaque accès : **emplacement** (Coffre UE / Config UE) + **statut**. Règle de mise à jour **R11** rappelée.
- **Preuve** (R1) : branche `claude/module-infra-bm5d9p` · commit registre `638f26b` · **PR draft #5** → `atelier` : https://github.com/ygonzalesei-cmyk/Tel-Online/pull/5 · scan anti-secret **négatif** (0 valeur, 0 motif de clé).
- **Tests (R8)** :
  - *automatisables* : lint Markdown + scan « aucun secret en clair » (fait → **négatif**) ;
  - *semi-auto* : revue croisée avec `/securite` (garde-fous, moindre privilège) ;
  - *humains obligatoires* : **validation patron** des fournisseurs et emplacements (ETAT §5) ;
  - *preuve manquante* : aucune (livrable documentaire, pas de build).
- **Sécurité** : impact **zero-knowledge = neutre** (aucune clé de déchiffrement des données utilisateur introduite ; dérivation **côté client**). Garde-fous respectés (pas d'OTP bancaire, pas de clone eSIM, pas de clair). **Aucun SEC-### ouvert** par cette mission.
- **Décisions nécessaires (R7)** — *n'ont pas arrêté la mission* : opérateur **eSIM** partenaire ? prestataire **paiement/wallet** ? **hébergeur UE** ? emplacement des clés (page « Connexions » vs variables d'env) ? (cf. `ETAT.md` §5). Fournisseurs = **candidats** « à confirmer patron ».
- **Reste à faire / prompt de reprise** : compléter les **noms définitifs** après **T-004** (modules métier + stack) et après le choix patron des partenaires ; réconcilier le registre si `VISION.md` / `securite/REGISTRE.md` évoluent.
- **Routage** : `/securite` (co-cadrage garde-fous, T-002 coffre ZK / T-003 conformité) · `/tickets` (suivi T-007) · `/release` (merge de la PR après gardien R10 + GO patron).
