# ETAT.md — Cockpit d'orchestration TEL ONLINE

> Tenu par **l'orchestrateur** (writer unique). Dernière mise à jour : **2026-07-23 (J0)**.
> Rappel : l'orchestrateur **produit des prompts** et **lit les rapports** ; il n'exécute rien.

## 0. Dispositif
- **Dispositif cloné à l'identique** de *Pilotage* : **9 agents transverses** opérationnels (skills créés).
- **Modules métier** : **NON clonés** (décision patron) → définis au **cadrage S0–S2** (Infra + Sécurité), créés après **validation patron**.
- **Stack technique** : laissée au **cadrage S0–S2** (Infra + Sécurité).
- Branches : **`atelier`** (intégration) créée ; **`main`** (PROD) réservée à **Release** ; travail sur `claude/<nom>`.

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
Bootstrap du dispositif terminé → **1re vague de prompts** = **cadrage sécurité/conformité S0–S2** (rails à fichiers disjoints, parallélisme sûr).

## 3. Tickets en vol
Voir `tickets/INDEX.md`. Actifs J0 : **T-001, T-005** (Sécurité), **T-006** (Design), **T-007** (Infra), **T-008** (Mémoire), + formalisation par **/tickets**.

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
