# INDEX des tickets — TEL ONLINE

> Writer : **/tickets**. Seed initial posé par l'orchestrateur (J0), ownership cédé à `/tickets`. **MAJ : après consolidation vague 1 + re-séquençage `ETAT.md`.**
> **Règle anti-récidive** : un ticket ne se **clôt** (`FAIT`) que sur **commit précis + verdict QA** (jamais « fait » sans preuve).
> États : `À FAIRE` → `EN COURS` → `EN RECETTE` → `FAIT (VERIFIED prod)`. Priorité : **P0** (bloquant) → **P3**.
> 🔴 **VETO RELEASE actif** (`../ETAT.md`) : 18 SEC-### P0 ⇒ aucune clôture *FAIT* (prod) possible tant qu'un P0 reste ouvert. N'empêche pas l'intégration du **cadrage** dans `atelier` (docs).

## Sommaire (T-001 → T-010)
| Ticket | Titre | Owner (skill) | Prio | État | Déclencheur / preuve |
|---|---|---|---|---|---|
| **T-001** | Modèle de menace & analyse de risques TEL ONLINE | `/securite` | P0 | 🟢 **EN RECETTE** | livré — **PR #2** (42 SEC-###, 18 P0) |
| **T-002** | Architecture **coffre ZERO-KNOWLEDGE** (E2E, dérivation de clé, récupération sans déchiffrement serveur) | `/securite` (+ `/module-infra`) | P0 | **À FAIRE (lançable)** | **T-001 🟢 + `atelier` intégré → réalisé** |
| **T-003** | Cadrage **conformité** RGPD / DSP2 / KYC-AML + **hébergement UE** | `/securite` | P0 | À FAIRE | **après T-002 🟢** *(re-séquencé)* |
| **T-004** | Définition des **modules métier** + **stack technique** (→ validation patron) | `/module-infra` (+ `/securite`) | P0 | À FAIRE | **T-002/T-003 🟢 + patron** |
| **T-005** | **Garde-fous produit** (pas d'OTP bancaire, pas de clone eSIM, pas de clair) → registre SEC + veto | `/securite` | P0 | 🟢 **EN RECETTE** | livré — **PR #2** (24 vetos) |
| **T-006** | **Charte UX** mobile-first + **WCAG** + **multilingue** (SPECS de base) | `/design` | P1 | 🟢 **EN RECETTE** | livré — **PR #4** |
| **T-007** | **Registre technique** initial : intégrations via **canaux officiels** (NOMS) | `/module-infra` | P1 | 🟢 **EN RECETTE** | livré — **PR #3** |
| **T-008** | **Mémoire produit** : VISION + glossaire + **backlog** (MVP→V1→V2, freemium) | `/memoire-produit` | P1 | 🟢 **EN RECETTE** | livré — **PR #5** |
| **T-009** | **Isolation LI / rétention télco vs coffre ZK** *(routé par Sécurité, T-001)* | `/module-infra` | P0 | À FAIRE (lançable) | `atelier` intégré → réalisé |
| **T-010** | **SPECS anti-capture / anti-overlay** des écrans sensibles *(routé par Sécurité, T-001)* | `/design` | P1 | À FAIRE (lançable) | `atelier` intégré → réalisé |

> 🟢 **EN RECETTE** = livré et intégré dans `atelier` (rapport agent 🟢 `READY_FOR_PUSH`), en attente de recette/vérification. **≠ FAIT** : la clôture *FAIT (VERIFIED prod)* reste gated (déploiement + 0 P0, R10).

## Priorisation S0–S2 — confirmée & mise à jour par /tickets
Tous les tickets restent dans la **vague S0–S2** (cadrage sécurité/conformité, avant tout module métier). Séquence à jour (cohérente avec `../ETAT.md` §4) :

- **Vague 1 — livrée 🟢** (cadrage intégré dans `atelier`) : **T-001, T-005** (Sécurité), **T-006** (Design), **T-007** (Infra), **T-008** (Mémoire). *(EN RECETTE ; clôture FAIT gated.)*
- **Vague 2 — lançable maintenant** (déclencheur « `atelier` intégré » réalisé) :
  1. **T-002** coffre zero-knowledge (`/securite`) → puis **T-003** conformité (même writer, **1 mission à la fois**) → **T-004** modules + stack (**+ GO patron**).
  2. **T-009** isolation LI/rétention vs ZK (`/module-infra`) et **T-010** SPECS anti-capture (`/design`) — **parallélisables** (rails disjoints).
- **Chemin critique** : `T-002 → T-003 → T-004 (+patron)` gouverne le démarrage des modules.

**Pas de re-priorisation** : l'ordre respecte « sécurité/conformité d'abord » (skill `/tickets`) et le VETO Release (R10). T-009 est **P0** (tension directe avec la promesse ZK) ; T-010 est **P1** (SPECS design, dérivées de menaces P0).

### Graphe des déclencheurs (à jour)
```
Vague 1 (livrée 🟢) :  T-001 ✅   T-005 ✅   T-006 ✅   T-007 ✅   T-008 ✅
                          │
        T-001 🟢  +  atelier intégré 🟢  (Release)
                          ▼
Vague 2 (lançable) :  T-002 ──▶ T-003 ──▶ T-004  (+ GO patron) ──▶ modules /module-<nom>
                      T-009   (P0, infra — LI/rétention vs ZK)   ┐
                      T-010   (P1, design — anti-capture/overlay)┘ parallèles, rails disjoints
```

## Fiches détaillées
[`T-001`](T-001.md) · [`T-002`](T-002.md) · [`T-003`](T-003.md) · [`T-004`](T-004.md) · [`T-005`](T-005.md) · [`T-006`](T-006.md) · [`T-007`](T-007.md) · [`T-008`](T-008.md) · [`T-009`](T-009.md) · [`T-010`](T-010.md).

## Bloqués patron
Voir `../ETAT.md` §5 : dépôt dédié (à confirmer) ; partenaires (opérateur eSIM, paiement/wallet, hébergeur UE) ; référent conformité RGPD/DSP2/KYC-AML ; emplacement des secrets (noms → `REGISTRE-TECH.md`) ; décisions Sécurité T-001 (2 secrets vs 1 kit hardware-backed ; portefeuille EMI ; périmètre PCI-DSS / PVID-eIDAS).
