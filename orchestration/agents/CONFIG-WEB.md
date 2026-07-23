# CONFIG-WEB.md — Flux de travail « web » (Claude Code on the web)

> Rappel du flux Git imposé (R9). `atelier` est une **branche**, jamais un destinataire de prompt.

## Principe
- **Une session = un skill = une branche** `claude/<nom>`.
- Chaque session **commit sans push direct** sur les branches d'intégration et ouvre une **PR *draft* vers `atelier`**.
- **JAMAIS** de push direct sur `atelier`, **JAMAIS** sur `main`.
- **Seul `/release`** fusionne `atelier → main` (PROD), après le **gardien** (R10) + **GO patron**.

## Branches
| Branche | Rôle | Qui écrit |
|---|---|---|
| `claude/<nom>` | Travail d'une session | la session correspondante |
| `atelier` | Intégration commune (cible des PR) | **/release** (merge des PR validées) |
| `main` | **PROD** | **/release** uniquement (`atelier → main`) |

## Cycle d'un ticket
1. Session ouvre/complète `claude/<nom>` → commit → **PR draft `atelier`**.
2. **Rapport 🟢/🔴** dans `orchestration/rapports/<agent>.md` (R6).
3. Gardien : **QA-Auto** + **QA-Recette** + **Sécurité (0 P0)** + **Mémoire à jour** (R10).
4. **GO patron** → **/release** merge + déploie `atelier → main`.
5. **QA-Recette** valide **en prod** → **/tickets** clôt (commit précis + verdict QA).

## Rappel « lançable tout de suite » (orchestrateur)
Un prompt qui dépend d'un prérequis n'est **pas** affiché : il est noté dans `ETAT.md` (déclencheur) et produit **quand** le déclencheur est réalisé.
