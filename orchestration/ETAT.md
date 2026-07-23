# ETAT.md — Cockpit d'orchestration TEL ONLINE

> Tenu par **l'orchestrateur** (writer unique). Dernière mise à jour : **2026-07-23 (J0)**.
> Rappel : l'orchestrateur **produit des prompts** et **lit les rapports** ; il n'exécute rien.

## 0. Dispositif
- **Dispositif cloné à l'identique** de *Pilotage* : **9 agents transverses** opérationnels (skills créés). Voir le **tableau de parité** : [`PARITE.md`](PARITE.md) (**9/9 identiques**).
- **Modules métier** : **NON clonés** (décision patron) → définis au **cadrage S0–S2** (Infra + Sécurité), créés après **validation patron**.
- **Stack technique** : laissée au **cadrage S0–S2** (Infra + Sécurité).
- **Gabarit de dispatch** : [`MODELE-PROMPT-AGENT.md`](MODELE-PROMPT-AGENT.md).
- Branches : **`atelier`** (intégration, base des PR) ; **`claude/<nom>`** pour le travail. **`main` (PROD) n'existe pas encore** : elle sera **créée par Release** au 1er déploiement (`atelier → main`, S3+) — conforme au « seul Release fait `atelier → main` ».

## 1. Qui bosse où
| Session | Skill | Branche | Mission en cours | Feu |
|---|---|---|---|---|
| Orchestrateur | `/orchestrateur` | `claude/orchestrateur-noxdrw` | Bootstrap dispositif + 1re vague S0–S2 | 🟢 en place |
| Sécurité | `/securite` | `claude/securite-*` | **T-001** menace + **T-005** garde-fous | ⏳ dispatché |
| Module-Infra | `/module-infra` | `claude/module-infra-*` | **T-007** registre technique (canaux officiels) | ⏳ dispatché |
| Design | `/design` | `claude/design-*` | **T-006** charte UX + WCAG + multilingue | ⏳ dispatché |
| Mémoire-Produit | `/memoire-produit` | `claude/memoire-produit-*` | **T-008** VISION + glossaire + backlog | ⏳ dispatché |
| Tickets | `/tickets` | `claude/tickets-*` | Formaliser fiches **T-001→T-008** + priorisation | ⏳ dispatché |
| QA-Auto | `/qa-auto` | — | *(en attente d'un premier livrable testable)* | ⏸️ gated |
| QA-Recette | `/qa-recette` | — | *(en attente d'un déploiement)* | ⏸️ gated |
| Release | `/release` | — | *(en attente 4 feux verts + GO patron)* | ⏸️ gated |

## 2. Fil en cours (J0)
Dispositif **poussé sur le dépôt distant** (branches `atelier` + `claude/orchestrateur-noxdrw`) → **PR draft #1** ouverte (`claude/orchestrateur-noxdrw → atelier`).
Déclencheur « dispositif sur le remote » **réalisé** → la **1re vague S0–S2** est **lançable** (rails à fichiers disjoints, parallélisme sûr). CI : aucun pipeline à ce stade (ressort `/qa-auto` au 1er livrable).
**Checkpoint `go`** : **vague 2 = 0 prompt lançable** pour l'instant — T-002/T-003 attendent **T-001 🟢** (Sécurité), T-004 attend cadrage + validation patron. En attente des rapports de la vague 1.

## 3. Tickets en vol
Voir [`tickets/INDEX.md`](tickets/INDEX.md). **Fiches `T-001`→`T-008` seedées** (critères d'acceptation posés), maintenance cédée à **/tickets**.
Actifs J0 : **T-001, T-005** (Sécurité), **T-006** (Design), **T-007** (Infra), **T-008** (Mémoire), + formalisation/priorisation par **/tickets**.

## 4. Prochaines étapes / déclencheurs (prompts NON affichés tant que le déclencheur n'est pas réalisé)
| Prompt en attente | Déclencheur (condition) |
|---|---|
| **T-002** Architecture coffre **zero-knowledge** (Sécurité + Infra) | Rapport **T-001** 🟢 (modèle de menace posé) |
| **T-003** Conformité **RGPD/DSP2/KYC-AML** + hébergement UE (Sécurité) | Rapport **T-001** 🟢 |
| **T-004** Définition **modules métier + stack** (Infra + Sécurité) | **T-001/T-002/T-003** 🟢 **+ validation patron** |
| **QA-Auto** (build/lint/unit/smoke) | Premier **livrable testable** poussé en PR `atelier` |
| **QA-Recette** (conforme + non-régression) | **Déploiement** effectué par Release |
| **Release** (`atelier → main`) | **QA-Auto 🟢 + QA-Recette 🟢 + Sécurité (0 P0) + Mémoire à jour + GO patron** (R10) |

## 5. Bloqués patron (décisions nécessaires — R7, sans rien inventer)
1. **Dépôt** : nouveau dépôt dédié (recommandé) ou sous-dossier ? *(ce dépôt `Tel-Online` semble déjà dédié — à confirmer.)*
2. **Partenaires** : opérateur **eSIM**, prestataire **paiement/wallet**, **hébergeur UE** — connus ou à sourcer ?
3. **Conformité** : référent **RGPD/DSP2/KYC-AML**, ou la Sécurité cadre-t-elle seule au départ ?
4. **Accès/secrets** : où poser les clés (page « Connexions » équivalente / variables d'env) ? → à consigner en **noms** dans `REGISTRE-TECH.md`.
