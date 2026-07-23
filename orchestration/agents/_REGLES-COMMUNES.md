# Règles communes R1–R13 (copiées de *Pilotage* — priment en cas de conflit)

> Ces règles s'appliquent à **toutes les sessions**. En cas de contradiction avec un skill, **R1–R13 priment**.
> Garde-fous : ce fichier + `CLAUDE.md` (racine) + hook `UserPromptSubmit` (`.claude/settings.json`).

- **R1 — Langue & style** : français, direct/factuel. **Jamais de livraison annoncée sans preuve** (build/test/commit).
- **R2 — Rôle exact** : chaque agent déclare ce qu'il **POSSÈDE** / **PEUT modifier** / **NE DOIT JAMAIS modifier** / **QUAND router**.
- **R3 — Périmètre strict** : dossiers autorisés/interdits ; **UN SEUL writer** par module/fichier/migration/déploiement ;
  hors périmètre → ticket ou prompt à l'owner. Fichier partagé (ex. `App.jsx`) : chacun ne touche QUE ses blocs, **relit avant d'éditer**.
- **R4 — Anti-conflit** : relire son skill ; `git fetch` ; vérifier commits/fichiers récents ; relire avant édition ;
  **ne jamais écraser** ; **jamais `git reset --hard`** sans GO explicite.
- **R5 — Workflow** : **1 ticket = 1 mission** ; rapport dans `rapports/<agent>.md` ; suivi global tenu par l'orchestrateur.
- **R6 — Rapport obligatoire** : à la fin de CHAQUE mission, feu **🟢 VERT** (`READY_FOR_PUSH`) ou **🔴 ROUGE**
  (`BLOCKED_*` / `PARTIAL_BLOCKED` + cause + prompt de reprise). **Pas de rapport = mission non terminée.**
- **R7 — Questions humaines** : une question qui bloque **N'ARRÊTE PAS** la mission — va au bout du faisable,
  PUIS note la question dans le rapport (« Décisions nécessaires »).
- **R8 — Tests** : classer en **automatisables / semi-auto / humains obligatoires / preuve manquante**.
- **R9 — Git (flux web)** : chaque session = **SA branche `claude/<nom>`** + **PR (draft) vers `atelier`** ;
  **JAMAIS** de push direct `atelier`, **JAMAIS** `main`. **Seul Release** fait `atelier → main`.
  `READY_FOR_PUSH` ≠ « en prod » (clôture après **VERIFIED prod**).
- **R10 — Gardien 🟢/🔴 avant Release** : **QA-Auto** (build+tests) + **QA-Recette** (conforme ticket + non-régression)
  + **Sécurité** (aucun P0) + **Mémoire** (à jour). **TOUT VERT + GO patron → Release déploie.**
  **UN SEUL ROUGE → STOP**, boucle de correction jusqu'au vert.
- **R11 — Registre technique** : avant de croire qu'un accès/outil « n'est pas là », lire `REGISTRE-TECH.md`.
  **Aucun secret en clair** (noms de variables uniquement). Mettre à jour le registre quand on ajoute une intégration.
- **R12 — Design & UX** : toute modif d'interface **SUIT la charte + les SPECS** de `design` (qui spécifie, ne code pas).
  Problème UX hors spec → signaler dans le rapport (routé vers `design`).
- **R13 — (Re)charge ton skill à CHAQUE message** avant d'agir, puis respecte ses limites (anti-dérive de rôle ;
  3 garde-fous : `_REGLES-COMMUNES.md`, `CLAUDE.md`, hook `UserPromptSubmit`).

---

## Rôle-type « module métier » (conservé pour chaque `/module-<nom>`)
Périmètre **exclusif** ; **un seul writer** ; **commit sans push** ; **rapport obligatoire 🟢/🔴** ; **PR vers `atelier`** (jamais `main`).

## Garde-fous produit TEL ONLINE (la Sécurité pose un veto si violés)
- ❌ intercepter/stocker les **SMS OTP bancaires (3D Secure)** → à la place : SCA/DSP2, passkeys, récupération rapide de la ligne.
- ❌ « **cloner** » une eSIM (impossible) → transfert / re-téléchargement supporté par l'opérateur.
- ❌ centraliser les **mots de passe en clair** → uniquement dans le **coffre chiffré zero-knowledge**.
- ⚠️ tout flux financier ⇒ **KYC/AML** à cadrer avec la conformité.
