# Rapports — /release

> Format : `../\_CONTRAT-RAPPORT.md`. Le plus récent en haut. Feu 🟢 `READY_FOR_PUSH` / 🔴 `BLOCKED_*` (R6).
> **/release** est le SEUL à `git push` et à déployer `atelier → main`, après gardien R10 + GO patron.

## [2026-07-23] Intégration Vague 1 — socle + 5 livrables dans `atelier`
- **Feu** : 🟢 READY_FOR_PUSH *(intégration DOCS dans `atelier` — PAS de déploiement `main`)*
- **Périmètre touché** : branche `atelier` **uniquement** (writer unique = Release, R2). `main` **non touché** (VETO actif).
- **Fait** :
  - **Socle** intégré en **fast-forward** depuis `claude/orchestrateur-noxdrw` (`atelier` en était l'ancêtre) → CLAUDE.md, `.claude/skills/*`, `.claude/settings.json`, règles communes, ETAT, PARITE, MODELE-PROMPT-AGENT, _CONTRAT-RAPPORT, CONFIG-WEB, seeds & évaluations.
  - **5 livrables** intégrés un par un via `git merge --no-ff -X theirs` (la version de l'agent prime sur le seed template, chaque agent n'ayant touché QUE ses fichiers) :
    - **Sécurité** (`claude/tel-online-threat-model-q2cm4s`) → `securite/MODELE-MENACE.md`, `securite/REGISTRE.md` (18 SEC-### P0), `rapports/securite.md`.
    - **Infra** (`claude/module-infra-bm5d9p`) → `agents/REGISTRE-TECH.md`, `rapports/module-infra.md`.
    - **Design** (`claude/design-cmnaxa`) → `agents/design/CHARTE-UX.md` + 5 SPECS, `rapports/design.md`.
    - **Mémoire-Produit** (`claude/memoire-produit-consolidation-fofrlx`) → `memoire-produit/{VISION,BACKLOG,FAIT}.md`, `rapports/memoire-produit.md`.
    - **Tickets** (`claude/tickets-4f4x54`) → `tickets/INDEX.md`, `tickets/T-001..T-008.md`, `rapports/tickets.md`.
- **Preuve** :
  - **SHA final `atelier`** (avant ce rapport) : `1a086fc070c524efd3d29ca4f22c5bc53960d777`.
  - **6 PR fermées automatiquement** au push (têtes devenues ancêtres d'`atelier`, `closed_at = merged_at = 2026-07-23T19:24:01Z`). Mapping exact **PR ↔ branche ↔ SHA de tête** (les libellés `[PR #n]` de mes messages de merge étaient nominaux ; référence faisant foi = le SHA) :
    - **PR #1** `claude/orchestrateur-noxdrw` `4b34278` (socle)
    - **PR #2** `claude/design-cmnaxa` `db815eb` (Design)
    - **PR #3** `claude/memoire-produit-consolidation-fofrlx` `3c05a0d` (Mémoire)
    - **PR #4** `claude/tel-online-threat-model-q2cm4s` `bf82c4d` (Sécurité)
    - **PR #5** `claude/module-infra-bm5d9p` `6291039` (Infra)
    - **PR #6** `claude/tickets-4f4x54` `5cca309` (Tickets)
  - Bilan vs amorce `5094a1e` : **50 fichiers A + 1 M** (`.gitignore`).
  - **Intégrité socle** : empreintes `git ls-tree` de CLAUDE.md / `.claude/*` / ETAT / _REGLES-COMMUNES / PARITE / MODELE-PROMPT-AGENT / _CONTRAT-RAPPORT / CONFIG-WEB / rapport orchestrateur / evaluations → **identiques avant/après** (aucun fichier socle écrasé).
  - **Priorité agent vérifiée** : les 10 fichiers en recouvrement seed↔agent (REGISTRE, rapports, VISION/BACKLOG/FAIT, INDEX, T-###) == version de la branche agent.
- **Tests (R8)** : intégration documentaire (aucun build/code métier). Contrôles automatiques ci-dessus = preuve suffisante. Recette non requise (pas de prod).
- **Sécurité** : scan « secrets en clair » sur tout l'arbre → **aucun** (NOMS uniquement, cf. REGISTRE-TECH). Coffre zero-knowledge respecté. **18 SEC-### P0 ouverts** (registre Sécurité) → **VETO Release `atelier → main` maintenu** : aucun déploiement prod.
- **Décisions nécessaires (R7)** : néant pour cette intégration. Le passage `atelier → main` reste conditionné à la levée des P0 + 4 feux verts + GO patron (R10).
- **Reste à faire / prompt de reprise** : aucun blocage. Étapes ultérieures (hors périmètre de cette mission) = traitement des SEC-### P0 puis cadrage vague 2.
- **Routage** : Orchestrateur (mise à jour `ETAT.md`) ; Sécurité (P0) ; Tickets (suite S0–S2).
