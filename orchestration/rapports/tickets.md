# Rapports — /tickets

> Format : `../\_CONTRAT-RAPPORT.md`. Le plus récent en haut. Feu 🟢 `READY_FOR_PUSH` / 🔴 `BLOCKED_*` (R6).
> Writer de `../tickets/` (INDEX + fiches T-###). **Clôture = commit précis + verdict QA** (anti-récidive).

## [2026-07-23] T-001→T-008 — Formalisation des fiches + priorisation S0–S2
- **Feu** : 🟢 READY_FOR_PUSH
- **Périmètre touché** : `orchestration/tickets/` (`INDEX.md` + `T-001`→`T-008`) + ce rapport `orchestration/rapports/tickets.md`. **Writer unique respecté** ; aucun fichier hors périmètre modifié (ni code, ni rapports d'autres agents, ni `ETAT.md`).
- **Fait** :
  - **Vérifié** les 8 fiches seedées : toutes **structurellement complètes et conformes** (owner-skill, priorité P0/P1, état, vague S0–S2, déclencheur, contexte, périmètre *writer unique*, critères DoD, clôture anti-récidive). **Aucune incohérence** avec `../ETAT.md` (déclencheurs, états, owners) ni avec le skill `/tickets`.
  - **Affiné** : ajout sur chaque fiche d'une ligne de **traçabilité amont/aval** (dépendances explicites, chemin critique, rails parallèles) — sans modifier le contenu produit (R2/R3).
  - **INDEX** : ajout d'une section **« Priorisation S0–S2 — confirmée »** (ordre, chemin critique, split *lançables J0* / *en attente de déclencheur*) + **graphe des déclencheurs**. Table seedée conservée à l'identique.
  - **Priorisation confirmée** : la séquence seedée respecte « sécurité/conformité S0–S2 d'abord » (skill `/tickets`) et les feux de `../ETAT.md` → **aucune re-priorisation nécessaire**.
- **Preuve** : commit de formalisation sur branche `claude/tickets-4f4x54` (SHA : _complété après push_) ; PR draft → `atelier` : _#à compléter_. (R1 : pas de livraison annoncée sans preuve.)
- **Tests (R8)** : livrable **documentaire** (pas de code) → **automatisables** : aucun ; **semi-auto** : contrôle croisé de cohérence `ETAT.md` ↔ `INDEX.md` ↔ fiches (owners/priorités/états/déclencheurs) — **OK** ; **humains obligatoires** : validation éditoriale par le patron/orchestrateur ; **preuve manquante** : néant.
- **Sécurité** : aucun secret introduit (fiches en **noms** uniquement). Aucun impact zero-knowledge. Les fiches **rappellent** les garde-fous (T-005 veto) et l'archi ZK (T-002) mais `/tickets` **n'ouvre pas** de SEC-### (hors périmètre → `/securite`).
- **Décisions nécessaires (R7)** : aucune n'a bloqué la mission. Rappel des **bloqués patron** (`../ETAT.md` §5) qui conditionnent T-003/T-004/T-007 : référent **conformité** RGPD/DSP2/KYC-AML ; **partenaires** (opérateur eSIM, prestataire paiement/wallet, hébergeur UE) ; **emplacement des secrets** (noms → `REGISTRE-TECH.md`) ; dépôt dédié (ce dépôt semble déjà dédié — à confirmer).
- **Reste à faire / prompt de reprise** : maintenance continue du backlog — (1) tout nouveau besoin = nouveau `T-###` (routé ici) ; (2) mettre à jour les **états** au fil des rapports d'agents ; (3) **dès `T-001` 🟢**, basculer T-002 & T-003 de `À FAIRE` → lançables dans l'INDEX, et préparer le déclenchement de T-004 (sur GO patron).
- **Routage** : décisions **produit** → `/memoire-produit` ; **sécurité** → `/securite` ; intégration de cette PR → **orchestrateur** (base `atelier`).

> **Note de traçabilité (transparence)** : au démarrage, le dépôt distant était encore vide (dispositif non propagé) ; une première ébauche hors-sujet avait été produite sur cette branche. Après `git fetch`, le vrai dispositif (skill `/tickets`, `ETAT.md`, seed réel) a été récupéré : la branche a été **ré-alignée sur `origin/atelier`** et repart du **seed réel** de l'orchestrateur. Aucune donnée d'un autre agent n'a été écrasée (R4).
