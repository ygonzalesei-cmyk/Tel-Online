# INDEX des tickets — TEL ONLINE

> Writer : **/tickets**. Seed initial posé par l'orchestrateur au bootstrap (J0), ownership cédé à `/tickets`.
> **Règle anti-récidive** : un ticket ne se **clôt** que sur **commit précis + verdict QA** (jamais « fait » sans preuve).
> États : `À FAIRE` → `EN COURS` → `EN RECETTE` → `FAIT (VERIFIED prod)`. Priorité : **P0** (bloquant) → **P3**.

## Vague S0–S2 — Cadrage sécurité / conformité (priorité absolue)
| Ticket | Titre | Owner (skill) | Prio | État | Déclencheur |
|---|---|---|---|---|---|
| **T-001** | Modèle de menace & analyse de risques TEL ONLINE | `/securite` | P0 | EN COURS | — (lançable) |
| **T-002** | Architecture **coffre ZERO-KNOWLEDGE** (E2E, dérivation de clé, récupération sans déchiffrement serveur) | `/securite` (+ `/module-infra`) | P0 | À FAIRE | après **T-001** 🟢 |
| **T-003** | Cadrage **conformité** RGPD / DSP2 / KYC-AML + **hébergement UE** | `/securite` | P0 | À FAIRE | après **T-001** 🟢 |
| **T-004** | Définition des **modules métier** TEL ONLINE + **stack technique** (→ validation patron) | `/module-infra` (+ `/securite`) | P0 | À FAIRE | après **T-001/002/003** 🟢 + **patron** |
| **T-005** | **Garde-fous produit** (pas d'OTP bancaire stocké, pas de clone eSIM, pas de clair) → registre SEC + veto | `/securite` | P0 | EN COURS | — (lançable, couplé T-001) |
| **T-006** | **Charte UX** mobile-first + **WCAG** + **multilingue** (SPECS de base) | `/design` | P1 | EN COURS | — (lançable) |
| **T-007** | **Registre technique** initial : intégrations via **canaux officiels** (NOMS uniquement) | `/module-infra` | P1 | EN COURS | — (lançable) |
| **T-008** | **Mémoire produit** : VISION + glossaire + **backlog** initial (MVP→V1→V2, freemium) | `/memoire-produit` | P1 | EN COURS | — (lançable) |

## Fiches détaillées
Fiches seedées au bootstrap (critères d'acceptation posés), maintenance cédée à **/tickets** :
[`T-001`](T-001.md) · [`T-002`](T-002.md) · [`T-003`](T-003.md) · [`T-004`](T-004.md) · [`T-005`](T-005.md) · [`T-006`](T-006.md) · [`T-007`](T-007.md) · [`T-008`](T-008.md).

## Bloqués patron
Voir `../ETAT.md` §5 (dépôt, partenaires, conformité, accès/secrets).
