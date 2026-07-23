# PARITE.md — Tableau de parité (clonage à l'identique du dispositif *Pilotage*)

> Procédure de parité imposée (Étape 0) : (a) inventaire → (b) copie conforme → (c) tableau OUI/NON →
> (d) tout réglage non copiable à l'identique est **signalé au patron** (jamais inventé). Date : **2026-07-23**.

## (a) Inventaire des agents transverses (9)
`orchestrateur`, `release`, `qa-auto`, `qa-recette`, `securite`, `tickets`, `memoire-produit`, `design`, `module-infra`.

## (b)+(c) Tableau de parité — agent → identique OUI/NON
| # | Agent (skill) | Rôle identique | Règles R1–R13 | Rituels (rapport 🟢/🔴, flux web, gardien) | **Parité** | Réglage non copiable ? |
|---|---|---|---|---|---|---|
| 1 | `/orchestrateur` | OUI | OUI | OUI (produit prompts, lit rapports, PM, éval. quotidienne) | **✅ OUI** | — |
| 2 | `/release` | OUI | OUI | OUI (seul push + `atelier→main`, gardien R10) | **✅ OUI** | — |
| 3 | `/qa-auto` | OUI | OUI | OUI (tests lecture seule, verdict 🟢/🔴) | **✅ OUI** | — |
| 4 | `/qa-recette` | OUI | OUI | OUI (recette vs demande + non-régression, en prod) | **✅ OUI** | — |
| 5 | `/securite` | OUI | OUI | OUI (SEC-### P0–P3, **veto** si P0) | **✅ OUI** | — |
| 6 | `/tickets` | OUI | OUI | OUI (T-###, anti-récidive) | **✅ OUI** | — |
| 7 | `/memoire-produit` | OUI | OUI | OUI (VISION/BACKLOG/FAIT, décisions) | **✅ OUI** | — |
| 8 | `/design` | OUI | OUI | OUI (charte + SPECS, lecture seule code) | **✅ OUI** | — |
| 9 | `/module-infra` | OUI | OUI | OUI (déploiement/auth-JWT/robustesse, rôle-type module) | **✅ OUI** | — |

**9 / 9 transverses clonés à l'identique.** Aucun réglage transverse non copiable (point (d) : rien à signaler).

## Modules métier — NON clonés (décision patron actée)
| Élément | Statut | Raison |
|---|---|---|
| Modules métier BTP de *Pilotage* | **NON clonés** | Décision patron : non pertinents pour TEL ONLINE |
| Modules métier TEL ONLINE (`/module-<nom>`) | **À définir au cadrage S0–S2** (Infra + Sécurité) puis **validation patron** | Architecture réelle TEL ONLINE (candidats : coffre/chiffrement, comptes/OAuth, eSIM, sync, paiements, conformité) |
| **Stack technique** | **À définir au cadrage S0–S2** (Infra + Sécurité) | Décision patron : laissée au cadrage |

> Le **rôle-type module** est conservé pour tout futur `/module-<nom>` : périmètre exclusif, **un seul writer**,
> commit sans push, rapport 🟢/🔴 obligatoire, **PR vers `atelier`** (jamais `main`).
