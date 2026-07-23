# ETAT.md — Cockpit d'orchestration TEL ONLINE

> Tenu par **l'orchestrateur** (writer unique). Dernière mise à jour : **2026-07-23 (J0, après consolidation vague 1)**.
> Rappel : l'orchestrateur **produit des prompts** et **lit les rapports** ; il n'exécute rien.

## 🔴 VETO RELEASE ACTIF (R10)
Sécurité (T-001) a ouvert **18 SEC-### P0** ⇒ **aucun déploiement `atelier → main` possible** tant qu'un P0 reste ouvert.
N'impacte PAS l'intégration de cadrage dans `atelier` (docs, pas de prod). Détail : `agents/securite/REGISTRE.md`.

## 0. Dispositif
- **Dispositif cloné à l'identique** de *Pilotage* : **9 agents transverses**. Parité : [`PARITE.md`](PARITE.md) (**9/9**).
- **Modules métier** & **stack** : définis au **cadrage S0–S2** (Infra + Sécurité), après **validation patron**.
- **Gabarit de dispatch** : [`MODELE-PROMPT-AGENT.md`](MODELE-PROMPT-AGENT.md).
- Branches : `atelier` (intégration) ; `claude/<nom>` (travail). **`main`** créée par **Release** au 1er déploiement.

## 1. Qui bosse où
| Session | Skill | Branche | Mission | Feu |
|---|---|---|---|---|
| Orchestrateur | `/orchestrateur` | `claude/orchestrateur-noxdrw` | Pilotage, consolidation vague 1, dispatch intégration | 🟢 |
| Sécurité | `/securite` | `claude/tel-online-threat-model-q2cm4s` | **T-001+T-005** menace + garde-fous | 🟢 livré |
| Design | `/design` | `claude/design-cmnaxa` | **T-006** charte + SPECS (PR #2) | 🟢 livré |
| Module-Infra | `/module-infra` | `claude/module-infra-bm5d9p` | **T-007** registre technique | 🟢 livré |
| Mémoire-Produit | `/memoire-produit` | `claude/memoire-produit-consolidation-fofrlx` | **T-008** VISION/BACKLOG/FAIT | 🟢 livré |
| Tickets | `/tickets` | `claude/tickets-4f4x54` | Fiches T-001→T-008 + priorisation | 🟢 livré |
| Release | `/release` | (à lancer) | **Intégrer vague 1 dans `atelier`** | ⏳ dispatché |
| QA-Auto / QA-Recette | — | — | En attente d'un livrable **de code** | ⏸️ gated |

## 2. Fil en cours (J0)
Vague 1 **livrée et consolidée : 5/5 🟢**. Les branches agents sont basées sur `atelier` **nu** → **intégration requise** :
`atelier` doit recevoir le **socle** (depuis `claude/orchestrateur-noxdrw`) **+ les 5 livrables** (règle : la version de l'agent prime sur le seed pour SES fichiers). Mission confiée à **Release**.

## 3. Tickets
Fiches : [`tickets/INDEX.md`](tickets/INDEX.md) (T-001→T-008). Faits (cadrage) : T-001, T-005, T-006, T-007, T-008 = 🟢 (à intégrer).

## 4. Prochaines étapes / déclencheurs (prompts NON affichés tant que le déclencheur n'est pas réalisé)
| Prompt en attente | Déclencheur |
|---|---|
| **Vague 2** — T-002 (coffre zero-knowledge, Sécurité) | **`atelier` intégré** (Release 🟢) → base propre pour brancher |
| **T-003** Conformité RGPD/DSP2 (Sécurité) | après **T-002 🟢** (même writer, 1 mission à la fois) |
| **T-009** Isolation LI/rétention télco vs ZK (Infra) *(routé par Sécurité)* | `atelier` intégré |
| **T-010** SPECS anti-capture/anti-overlay (Design) *(routé par Sécurité)* | `atelier` intégré |
| **T-004** Modules métier + stack (Infra) | T-002/T-003 🟢 **+ validation patron** |
| **QA-Auto / QA-Recette / Release (deploy)** | 1er livrable **de code** → puis gardien R10 + **0 P0** + GO patron |

## 5. Bloqués patron (décisions — R7, sans rien inventer)
1. **Dépôt** : `ygonzalesei-cmyk/Tel-Online` = dédié (PR #1 dessus) → *à confirmer*.
2. **Partenaires** (remontés par Infra + Sécurité) : opérateur **eSIM**, prestataire **paiement/wallet**, **hébergeur UE** — connus ou à sourcer ?
3. **Conformité** : référent **RGPD/DSP2/KYC-AML**, ou Sécurité seule au départ ?
4. **Secrets** : emplacement (noms → `REGISTRE-TECH.md`) ?
5. **Décisions Sécurité (T-001)** : (a) **2 secrets** (phrase + mot de passe maître) *ou* **1 kit hardware-backed** ? (b) portefeuille = **monnaie électronique** (EMI) ? (c) périmètre **PCI-DSS** / certif **PVID/eIDAS** ?
