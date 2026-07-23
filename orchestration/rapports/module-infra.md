# Rapports — /module-infra

> Format : `../_CONTRAT-RAPPORT.md`. Le plus récent en haut. Feu 🟢 `READY_FOR_PUSH` / 🔴 `BLOCKED_*` (R6).
> Périmètre infra (déploiement, serveur, sauvegardes, auth/JWT, robustesse). Writer désigné de `../agents/REGISTRE-TECH.md`.

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
