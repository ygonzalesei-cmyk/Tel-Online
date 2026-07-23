# INDEX des tickets — TEL ONLINE

> Writer : **/tickets**. Seed initial posé par l'orchestrateur (J0), ownership cédé à `/tickets`. **MAJ : vague 2 livrée (T-002/T-003/T-009/T-010 🟢).**
> **Règle anti-récidive** : un ticket ne se **clôt** (`FAIT`) que sur **commit précis + verdict QA** (jamais « fait » sans preuve).
> États : `À FAIRE` → `EN COURS` → `EN RECETTE` → `FAIT (VERIFIED prod)`. Priorité : **P0** (bloquant) → **P3**.
> 🔴 **VETO RELEASE actif** (`../ETAT.md`) : 18 SEC-### P0 ⇒ aucune clôture *FAIT* (prod) possible tant qu'un P0 reste ouvert. N'empêche pas l'intégration du **cadrage** dans `atelier` (docs).

## Sommaire (T-001 → T-010)
| Ticket | Titre | Owner (skill) | Prio | État | Preuve / déclencheur |
|---|---|---|---|---|---|
| **T-001** | Modèle de menace & analyse de risques | `/securite` | P0 | 🟢 **EN RECETTE** | livré — **PR #2** (intégré ; 42 SEC-###, 18 P0) |
| **T-002** | Architecture **coffre ZERO-KNOWLEDGE** | `/securite` (+ `/module-infra`) | P0 | 🟢 **EN RECETTE** | livré — **PR #8** (10/18 P0 couverts) |
| **T-003** | Cadrage **conformité** RGPD / DSP2 / KYC-AML + hébergement UE | `/securite` | P0 | 🟢 **EN RECETTE** | livré — **PR #13** |
| **T-004** | Définition **modules métier** + **stack** (→ validation patron) | `/module-infra` (+ `/securite`) | P0 | **À FAIRE** | **VALIDATION PATRON** (T-002/T-003 🟢 ✅ acquis) |
| **T-005** | **Garde-fous produit** → registre SEC + veto | `/securite` | P0 | 🟢 **EN RECETTE** | livré — **PR #2** (intégré ; 24 vetos) |
| **T-006** | **Charte UX** mobile-first + **WCAG** + **multilingue** | `/design` | P1 | 🟢 **EN RECETTE** | livré — **PR #4** (intégré) |
| **T-007** | **Registre technique** initial (canaux officiels, NOMS) | `/module-infra` | P1 | 🟢 **EN RECETTE** | livré — **PR #3** (intégré) |
| **T-008** | **Mémoire produit** : VISION + glossaire + backlog | `/memoire-produit` | P1 | 🟢 **EN RECETTE** | livré — **PR #5** (intégré) |
| **T-009** | **Isolation LI / rétention télco vs coffre ZK** *(routé Sécurité)* | `/module-infra` | P0 | 🟢 **EN RECETTE** | livré — **PR #9** |
| **T-010** | **SPECS anti-capture / anti-overlay** *(routé Sécurité)* | `/design` | P1 | 🟢 **EN RECETTE** | livré — **PR #10** |

> 🟢 **EN RECETTE** = livré (rapport agent 🟢 `READY_FOR_PUSH` + PR), en attente de recette/vérification. **≠ FAIT** : clôture *FAIT (VERIFIED prod)* gated (déploiement + 0 P0, R10). « intégré » = déjà mergé dans `atelier` (vague 1) ; vague 2 = **intégration Release dispatchée**.

## Priorisation S0–S2 — état à jour
Tous les tickets sont dans la **vague S0–S2** (cadrage sécurité/conformité, avant tout module métier).

- **Vague 1 — livrée & intégrée dans `atelier` 🟢** : **T-001, T-005** (Sécurité), **T-006** (Design), **T-007** (Infra), **T-008** (Mémoire).
- **Vague 2 — livrée 🟢, intégration Release dispatchée** : **T-002** (coffre ZK), **T-003** (conformité), **T-009** (isolation LI/ZK), **T-010** (SPECS sensibles).
- **Reste à lancer** : **T-004** (modules métier + stack) — **bloqué sur la seule validation patron** (T-002/T-003 🟢 acquis) ; puis création des skills `/module-<nom>`, puis 1er livrable **de code** → QA-Auto / QA-Recette / Release.

**Bilan** : **9/10 tickets livrés** (🟢 EN RECETTE), aucun EN COURS. **Aucune clôture *FAIT*** (anti-récidive : pas de VERIFIED prod ; VETO Release 18 P0 actif). Priorisation « sécurité/conformité d'abord » **respectée**.

### Graphe des déclencheurs (à jour)
```
Vague 1 (livrée+intégrée 🟢) :  T-001 ✅   T-005 ✅   T-006 ✅   T-007 ✅   T-008 ✅
Vague 2 (livrée 🟢, intégration dispatchée) :  T-002 ✅   T-003 ✅   T-009 ✅   T-010 ✅
                                     │
                        T-002 / T-003 🟢  (acquis)
                                     ▼
                         T-004  ⏳ attend **GO patron**  ─▶  création /module-<nom>  ─▶  (code)  ─▶  QA-Auto/QA-Recette/Release (0 P0 + GO)
```

## Fiches détaillées
[`T-001`](T-001.md) · [`T-002`](T-002.md) · [`T-003`](T-003.md) · [`T-004`](T-004.md) · [`T-005`](T-005.md) · [`T-006`](T-006.md) · [`T-007`](T-007.md) · [`T-008`](T-008.md) · [`T-009`](T-009.md) · [`T-010`](T-010.md).

## Bloqués patron
Voir `../ETAT.md` §5 : dépôt dédié (à confirmer) ; partenaires (opérateur eSIM, paiement/wallet, hébergeur UE) ; référent conformité RGPD/DSP2/KYC-AML ; emplacement des secrets (noms → `REGISTRE-TECH.md`) ; décisions Sécurité T-001 (**reco : 1 secret maître** — phrase + clé matérielle ; portefeuille EMI ; périmètre PCI-DSS / PVID-eIDAS) ; **Design Q1–Q8** (longueur phrase, facteurs de récupération, périmètre gratuit/premium, langues). **⇒ Débloquent notamment T-004 (GO patron).**
