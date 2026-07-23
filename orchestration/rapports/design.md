# Rapport — Pôle Design · Mission T-006

| | |
|---|---|
| **Mission** | T-006 — Charte UX + SPECS des écrans sensibles de TEL ONLINE |
| **Branche de dev** | `claude/design-cmnaxa` |
| **Cible PR** | `atelier` (jamais `main`) |
| **Date** | 2026-07-23 |
| **Statut global** | 🟢 **Livrables produits et committés localement** · 🔴 **Livraison distante (push + PR) bloquée par une permission dépôt** |

---

## 🟢 Livrables produits (périmètre `orchestration/agents/design/`)

| Fichier | Contenu | Taille |
|---|---|---|
| `CHARTE-UX.md` | **Charte UX socle v0.2** : principes grand public, mobile-first, WCAG 2.2 AA, i18n dès le départ, offline-first, perf, sécurité UX, tokens à contrastes **calculés**, verrouillage du coffre, glossaire. | ~320 l. |
| `SPECS-onboarding-coffre.md` | Onboarding + création du coffre (valeur → protection locale → coffre prêt), permissions différées, verrouillage, offline. | ~596 l. |
| `SPECS-phrase-recuperation.md` | Phrase de récupération : affichage sécurisé, sauvegarde papier, vérification non-cognitive (3.3.8), report avec rappel du risque. | ~477 l. |
| `SPECS-recuperation-zero-perte.md` | Configuration des facteurs (phrase + SIM numérique/SMS + sauvegarde chiffrée + contacts de confiance) **et** récupération guidée sans cul-de-sac. | ~581 l. |
| `SPECS-esim-secours.md` | Carte SIM numérique de secours : compatibilité, activation guidée, états réseau, quota, usage en récupération. | ~534 l. |
| `SPECS-abonnement-freemium.md` | Offre gratuite / premium : comparaison transparente (sans dark patterns), passage premium, résiliation aussi simple que la souscription. | ~572 l. |
| `../rapports/design.md` | Le présent rapport. | — |

**Périmètre respecté** : écriture uniquement sous `orchestration/agents/design/` (+ ce rapport). **Aucune modification de code** (R12) ; les maquettes sont des **wireframes textuels**, les i18n des **tables clé→texte**. Lecture seule sur le code (au demeurant, le dépôt était vide).

---

## 🟢 Méthode & qualité

- **Charte rédigée en premier** (document socle), avec une **palette de tokens dont chaque ratio de contraste a été calculé** (WCAG AA/AAA).
- **Orchestration multi-agents** : pour chaque SPEC, pipeline **rédaction → audit adversarial → révision** ; la **charte** a elle-même été auditée. 16 agents, 0 erreur.
- **15 corrections d'audit appliquées à la charte** (passage v0.1 → **v0.2**), dont :
  - 🔴→✅ **Verrouillage/déverrouillage du coffre** (§10.1) + **8ᵉ état d'écran « Verrouillé »** (§13).
  - 🔴→✅ **Divulgation privée des secrets** au lecteur d'écran (casque, lecture mot-à-mot, pas de région *live* sur un secret) (§5.3).
  - 🟠→✅ **Palette sombre** complétée et **recalculée** ; correction du piège lien vs bouton (`#6AA6FF` ne peut pas porter de texte blanc) (§11.2).
  - 🟠→✅ **Capture d'écran réaliste par plateforme** (Android bloque, iOS ne peut que détecter) + masquage de l'aperçu multitâche + presse-papiers inter-appareils (§10).
  - 🟠→✅ **Champ d'affichage vs de saisie** d'un secret et conformité **WCAG 3.3.8** (collage autorisé pour la saisie) (§10, §5.4).
  - 🟠→✅ **i18n** : 6 catégories de pluriel ICU (arabe), isolation **bidi**, système de chiffres par locale, expansion par plage de longueur (§6).
  - 🟠→✅ **Citations WCAG corrigées** (2.4.13 pour l'apparence du focus) et critères ajoutés (2.5.7, 3.2.6, 1.4.13, 1.3.5, 2.4.1).
  - 🟠→✅ **Tous les renvois de section** rectifiés (bug de numérotation) + harmonisation terminologique.
- **Vérification finale des SPECS** : aucun bloc de code applicatif, aucun jargon à l'écran, structure complète (16 sections), points de sécurité critiques présents, alignement terminologique avec la charte.

---

## 🔴 Blocage de livraison distante (action requise)

La mission demande **commit + PR draft vers `atelier`**. Le contenu est prêt et committé **localement** sur `claude/design-cmnaxa`, mais **la publication distante est impossible dans cette session** :

- `git push` (git direct via le proxy git local) → **HTTP 403** (authentifié, mais **interdit par politique**), aussi bien vers `atelier` que vers `claude/design-cmnaxa`.
- Écriture via l'**API GitHub (MCP)** (`create_or_update_file`) → **403 « Resource not accessible by integration »** (l'App GitHub n'a **pas** la permission `contents:write`).
- `list_branches` renvoie `[]` : le dépôt distant est **vide** (aucune branche `main`/`atelier`).

Conformément aux règles de l'environnement (« ne pas réessayer un refus de politique 403/407, le signaler »), **je n'ai pas contourné** ce refus et **je n'ai pas réessayé en boucle**. R7 (ne pas rester bloqué) est respecté en **produisant l'intégralité des livrables** et en les **committant localement**.

**Pour débloquer la livraison** — au choix de l'équipe :
1. Accorder au **GitHub App / à la session** un accès **écriture** (`contents:write` + autorisation de push) sur `ygonzalesei-cmyk/Tel-Online` (réglages Claude / installation de l'App).
2. Créer la branche d'intégration **`atelier`** côté dépôt (le dépôt est actuellement non initialisé).

Dès que l'accès en écriture est ouvert, la séquence de livraison est : push `claude/design-cmnaxa` → création `atelier` (base) → **PR draft `claude/design-cmnaxa` → `atelier`** (jamais `main`).

---

## ⚠️ Notes & décisions

- **`/design`** : c'est une **commande CLI intégrée**, non chargeable par l'outil `Skill` (le système demande à l'utilisateur de la lancer). J'ai donc appliqué les bonnes pratiques UX/accessibilité directement (R7 : pas de blocage).
- **Amorçage `atelier`** : une branche `atelier` locale a été préparée à partir d'un commit racine, en vue de servir de base à la PR ; sa publication attend l'accès en écriture.
- **Hypothèses produit** `HYP-1`…`HYP-5` (charte §1) : formulées par le Design pour la cohérence des maquettes, **à confirmer par le pôle Produit**.

## Questions ouvertes (renvoi charte §17)
`Q1` hypothèses produit · `Q2` longueur/vocabulaire de la phrase · `Q3` facteurs de récupération & priorité · `Q4`/`Q5` périmètre gratuit vs premium & quota SIM de secours · `Q6` langues + système de chiffres · `Q7` contraintes réglementaires (KYC/RGPD) · `Q8` périmètre « valeur/argent ».

## Prochaines étapes proposées
1. Débloquer l'accès en écriture → pousser la branche et **ouvrir la PR draft vers `atelier`**.
2. Revue en atelier de la charte v0.2 et des 5 SPECS ; trancher `Q1`…`Q8`.
3. Décliner les maquettes visuelles à partir des tokens validés.

---

*Rapport généré par le pôle Design (session DESIGN). Livrables : `orchestration/agents/design/CHARTE-UX.md` + `SPECS-*.md`.*
