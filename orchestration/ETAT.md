# ETAT.md — Cockpit d'orchestration TEL ONLINE

> Tenu par **l'orchestrateur** (writer unique). Dernière mise à jour : **2026-07-23 (J0, atelier intégré + vague 2 dispatchée)**.
> Rappel : l'orchestrateur **produit des prompts** et **lit les rapports** ; il n'exécute rien.

## 🔴 VETO RELEASE ACTIF (R10)
Sécurité (T-001) : **18 SEC-### P0** ouverts ⇒ **aucun déploiement `atelier → main`** tant qu'un P0 reste ouvert.
Sans impact sur l'intégration de cadrage (docs). Détail : `agents/securite/REGISTRE.md`. `main` n'existe pas encore.

## 0. Dispositif
- **Cloné à l'identique** de *Pilotage* : 9 transverses. Parité : [`PARITE.md`](PARITE.md) (**9/9**).
- **`atelier` INTÉGRÉ** ✅ (socle + 5 livrables vague 1, via Release — PR #1→#6 mergées, commit `ba4aad4`).
- **Modules métier** & **stack** : cadrage S0–S2 (T-004), après validation patron.
- Branches : `atelier` (intégration) ; `claude/<nom>` (travail). **`main`** créée par Release au 1er déploiement.

## 1. Qui bosse où
| Session | Skill | Ticket | Feu |
|---|---|---|---|
| Orchestrateur | `/orchestrateur` | Pilotage, consolidation, dispatch vague 2 | 🟢 |
| Release | `/release` | Intégration vague 1 → `atelier` | 🟢 fait |
| **Sécurité** | `/securite` | **T-002** coffre zero-knowledge | 🟢 **livré** |
| **Module-Infra** | `/module-infra` | **T-009** isolation LI/rétention vs ZK + infra UE | 🟢 **livré** |
| **Design** | `/design` | **T-010** SPECS écrans sensibles / menace (PR #10) | 🟢 **livré** |
| **Sécurité** (suite) | `/securite` | **T-003** conformité RGPD/DSP2/KYC-AML | 🟢 **livré** |
| **Release** | `/release` | **Intégrer vague 2** dans `atelier` | ⏳ dispatché |
| Mémoire / Tickets | — | maintien VISION/backlog & tickets | 🟢 dispo |
| QA-Auto / QA-Recette | — | attente d'un livrable **de code** | ⏸️ gated |

## 2. Fil en cours (J0)
Vague 1 **intégrée** (Release 🟢). **Vague 2 : 4/4 🟢** — T-002 (coffre ZK), T-003 (conformité), T-009 (isolation LI/ZK), T-010 (SPECS↔menace) — **+ Mémoire & Tickets auto-synchronisés**. **Intégration Release de la vague 2 dispatchée** (6 branches-deltas, union des rapports). VETO maintenu (18 P0, aucun fermé). Ensuite **T-004** (modules + stack) : **attend la validation patron**.

## 3. Tickets
[`tickets/INDEX.md`](tickets/INDEX.md). **Faits & intégrés** : T-001, T-005, T-006, T-007, T-008 🟢.
**Actifs** : T-002 (Sécurité). **Nouveaux** (routés par Sécurité, à formaliser par `/tickets`) : **T-009** (Infra), **T-010** (Design).

## 4. Prochaines étapes / déclencheurs
| Prompt en attente | Déclencheur |
|---|---|
| **T-003** Conformité RGPD/DSP2/KYC-AML (Sécurité) | après **T-002 🟢** (même writer, 1 mission à la fois) |
| **T-004** Modules métier + stack (Infra) | **T-002/T-003 🟢 + validation patron** |
| Création des skills `/module-<nom>` | après **T-004** validé patron |
| **QA-Auto / QA-Recette / Release (deploy)** | 1er livrable **de code** → gardien R10 + **0 P0** + GO patron |

## 5. Bloqués patron (décisions — R7, sans rien inventer)
1. **Dépôt** `ygonzalesei-cmyk/Tel-Online` dédié (PR sur ce dépôt) → *à confirmer*.
2. **Partenaires** (Infra+Sécurité) : opérateur **eSIM**, prestataire **paiement/wallet**, **hébergeur UE** — connus ou à sourcer ?
3. **Conformité** : référent **RGPD/DSP2/KYC-AML** ou Sécurité seule au départ ?
4. **Secrets** : emplacement (noms → `REGISTRE-TECH.md`) ?
5. **Sécurité (T-001)** : (a) **2 secrets** (phrase + mdp maître) *ou* **1 kit hardware** ? (b) wallet = **EMI** ? (c) périmètre **PCI-DSS** / **PVID/eIDAS** ?
6. **Design (T-006)** : arbitrer **Q1–Q8** (longueur de la phrase, facteurs de récupération, périmètre gratuit/premium, langues, contraintes KYC/RGPD).
