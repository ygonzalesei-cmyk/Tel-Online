# TEL ONLINE — Dispositif d'orchestration

Ce dépôt héberge le **dispositif d'orchestration** du projet **TEL ONLINE** (jumeau du dispositif *Pilotage*),
et non (encore) le code produit : la **stack technique et les modules métier sont cadrés en S0–S2** par
Infra + Sécurité, puis créés après validation du patron.

## TEL ONLINE, en une phrase
Application **« téléphone virtuel / coffre-fort numérique »**, multi-plateforme (iOS, Android, desktop, web/PWA),
**un seul compte**, promesse **« ZÉRO PERTE »** : en cas de perte/vol, l'utilisateur récupère instantanément,
depuis n'importe quel appareil, toute sa vie numérique **+ une ligne mobile (eSIM de secours)** — le tout
**chiffré en zero-knowledge** (le serveur ne peut jamais lire les données).

## Comment ça marche (le dispositif)
- **1 équipe de 15 agents** : 9 **transverses** (clonés à l'identique de *Pilotage*) + **modules métier** définis au cadrage.
- Chaque session (agent) = **un skill** (`/<nom>`) + **sa branche `claude/<nom>`** + **PR *draft* vers `atelier`**.
- **L'Orchestrateur** pilote : il **produit des prompts** copiables pour le patron et **lit les rapports 🟢/🔴**.
  Il ne code pas, ne pousse pas, n'exécute ni tests ni merge ni déploiement.
- **Release** est le **seul** à `git push` et à déployer **`atelier → main`** (= PROD), après feu vert du
  **gardien** (QA-Auto + QA-Recette + Sécurité + Mémoire) et **GO patron**.

## Points d'entrée
| Fichier | Rôle |
|---|---|
| [`CLAUDE.md`](CLAUDE.md) | Règle de rôle lue par toute session (tableau session → skill) |
| [`.claude/settings.json`](.claude/settings.json) | Hook `UserPromptSubmit` (role-lock) |
| [`.claude/skills/`](.claude/skills/) | Les 9 skills transverses |
| [`orchestration/ETAT.md`](orchestration/ETAT.md) | Cockpit : qui bosse où, fil en cours, déclencheurs |
| [`orchestration/tickets/INDEX.md`](orchestration/tickets/INDEX.md) | Index des tickets T-### |
| [`orchestration/rapports/`](orchestration/rapports/) | Rapports 🟢/🔴 par agent |
| [`orchestration/agents/_REGLES-COMMUNES.md`](orchestration/agents/_REGLES-COMMUNES.md) | Règles R1–R13 |

## Branches
- **`atelier`** — intégration commune (cible des PR).
- **`main`** — PROD (**seul Release** y déploie depuis `atelier`).
- **`claude/<nom>`** — branche de travail d'une session.

## Sécurité (non négociable)
Coffre **zero-knowledge** ; récupération « zéro perte » **sans déchiffrement serveur** ; intégrations
**uniquement via canaux officiels** (OAuth, API bancaires officielles, eSIM opérateur) ; **hébergement UE**.
Garde-fous : ❌ pas d'interception/stockage des **OTP bancaires (3D Secure)** ; ❌ pas de « clone » d'eSIM ;
❌ pas de mots de passe en clair ; ⚠️ tout flux financier ⇒ **KYC/AML** à cadrer. **Aucun secret en clair** dans le dépôt.
