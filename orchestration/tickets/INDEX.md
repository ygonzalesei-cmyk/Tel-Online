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

## Priorisation S0–S2 — confirmée par /tickets (J0)
Les 8 fiches appartiennent à la **vague S0–S2** (cadrage sécurité/conformité — priorité absolue avant tout module métier, cf. skill `/tickets`). Vérification faite, la séquence seedée est **cohérente avec `../ETAT.md`** et n'appelle pas de re-priorisation. Ordre confirmé :

1. **P0 — chemin critique sécurité** : **T-001** (modèle de menace) → **T-002** (coffre ZK) & **T-003** (conformité) → **T-004** (modules + stack, *sur GO patron*).
2. **P0 — parallèle, couplé T-001** : **T-005** (garde-fous à veto — alimente le gate Release R10).
3. **P1 — parallélisables, rails disjoints (lançables J0)** : **T-006** (UX/WCAG/i18n), **T-007** (registre technique), **T-008** (mémoire produit).

- **Lançables maintenant (J0)** : T-001, T-005, T-006, T-007, T-008.
- **En attente de déclencheur** : T-002 & T-003 (après **T-001 🟢**) ; T-004 (après **T-001/002/003 🟢 + patron**).

### Graphe des déclencheurs
```
T-001 ─┬─▶ T-002 ─┐
       └─▶ T-003 ─┴─▶ T-004  (+ GO patron) ─▶ modules /module-<nom>
T-005   (P0, couplé T-001 — veto Sécurité / gate Release R10)
T-006   T-007   T-008   (P1, parallèles — rails disjoints, lançables J0)
```

## Fiches détaillées
Fiches seedées au bootstrap (critères d'acceptation posés), maintenance cédée à **/tickets** :
[`T-001`](T-001.md) · [`T-002`](T-002.md) · [`T-003`](T-003.md) · [`T-004`](T-004.md) · [`T-005`](T-005.md) · [`T-006`](T-006.md) · [`T-007`](T-007.md) · [`T-008`](T-008.md).

## Bloqués patron
Voir `../ETAT.md` §5 (dépôt, partenaires, conformité, accès/secrets).
