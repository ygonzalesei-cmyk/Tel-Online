# Rapports — Pôle Design (journal anti-chronologique, R6)

> Format imposé : `_CONTRAT-RAPPORT.md`. Entrée la plus récente en haut.

## [2026-07-23] T-010 — Alignement SPECS ↔ modèle de menace + arbitrages Q1–Q8
- **Feu** : 🟢 READY_FOR_PUSH
- **Périmètre touché** : `orchestration/agents/design/` — MAJ des 4 SPECS sensibles (`SPECS-onboarding-coffre.md`, `SPECS-phrase-recuperation.md`, `SPECS-recuperation-zero-perte.md`, `SPECS-esim-secours.md` : section « Alignement modèle de menace (T-010) ») + création `ALIGNEMENT-MENACE.md` ; ce rapport. **Un seul writer respecté** (design) ; **lecture seule** sur `securite/` et le code.
- **Fait** : pour chaque écran sensible, la mesure UX contre les 4 vecteurs — (1) **capture d'écran par plateforme** (Android bloque / iOS occulte l'aperçu multitâche + détecte) ; (2) **overlay/tapjacking** (masquage + appuis obscurcis ignorés + détection de partage d'écran) ; (3) **phishing de la seed** (jamais saisie en ligne, vérif 100 % locale, pédagogie, notif hors-bande signée, jamais de SMS) ; (4) **autofill/presse-papiers** (clavier sécurisé, exclusion de l'autofill tiers, presse-papiers local + effacement). **Table de traçabilité menace↔UX** vers les SEC-### P0 « UX » + garde-fous VETO, **matrice écran×vecteur**, et **fiche décisions Q1–Q8** pour le patron (dans `ALIGNEMENT-MENACE.md`).
- **Preuve** : branche `claude/design-cmnaxa` (base `origin/atelier`) — commit T-010 + **PR draft → `atelier`** (référence ajoutée après ouverture). Vérif : 4 SPECS portent la section T-010 (réfs SEC-### : 11–14 par fichier) ; `ALIGNEMENT-MENACE.md` créé (traçabilité + Q1–Q8).
- **Tests (R8)** : **humains obligatoires** (revue en atelier de l'alignement UX + arbitrage Q1–Q8) ; **semi-auto** (relecture accessibilité WCAG des nouvelles mesures) ; **aucun test automatisable** (spécification documentaire, aucun code — R12).
- **Sécurité** : aucun impact zero-knowledge négatif ; l'alignement **renforce** la posture UX (SEC-004 P0 phishing seed, SEC-025/026 capture/presse-papiers, SEC-005/006 SIM-swap, SEC-018 biométrie). **Ne lève aucun P0** : les P0 restent ouverts côté **implémentation** (routés sécurité/mobile). Aucune mesure ne dégrade WCAG 2.2 AA.
- **Décisions nécessaires (R7)** — routées **patron** (ne bloquent pas la mission) : **Q2/Q3 — un seul kit de récupération vs deux secrets** (reco Design : **kit unique**, le double secret alimente le phishing SEC-004 et le DoS de verrouillage) ; **Q4/Q5** quota de la SIM de secours en offre gratuite ; **Q7** conformité (KYC/AML, RGPD Art. 9/22, PCI, PVID/eIDAS) ; **Q8** périmètre e-money (EMI/EMD2). Détail complet : `ALIGNEMENT-MENACE.md` §4.
- **Reste à faire / prompt de reprise** : après arbitrage Q1–Q8, mettre à jour les SPECS selon la décision (surtout un/deux secrets) ; décliner l'UX des flux sensibles pour le **client web/PWA** (routage Design + Infra).
- **Routage** : `securite` (implémentation des mécanismes : FLAG_SECURE, détection overlay/partage d'écran, liaison autofill au package/origine, pinning SM-DP+, signature d'intention matérielle) ; `patron` (arbitrage Q1–Q8) ; modules `mobile`/`infra` (réalisation).

---

## [2026-07-23] T-006 — Charte UX + SPECS des écrans sensibles
- **Feu** : 🟢 READY_FOR_PUSH — **livré et fusionné** (PR #2 mergée dans `atelier`).
- **Périmètre touché** : `orchestration/agents/design/CHARTE-UX.md` (v0.2) + 5 `SPECS-*.md` + ce rapport. Un seul writer (design).
- **Fait** : **charte socle** (mobile-first, WCAG 2.2 AA à **contrastes calculés**, i18n dès le départ dont **AR-RTL**, offline-first, sécurité UX, verrouillage du coffre, glossaire) + **5 SPECS** (onboarding coffre, phrase de récupération, récupération sans perte, carte SIM numérique de secours, abonnement gratuit/premium). Méthode : rédaction → **audit adversarial** → révision ; **15 défauts corrigés** sur la charte (v0.1 → v0.2).
- **Preuve** : PR **#2** `claude/design-cmnaxa → atelier`, **fusionnée** ; commits `0fe8347`, `15dff24`, `db815eb`.
- **Tests (R8)** : **humains obligatoires** (revue atelier) ; aucun code (R12) ; contrastes de tokens **calculés/vérifiés**.
- **Sécurité** : sécurité UX intégrée (anti-capture réaliste par plateforme, divulgation privée des secrets au lecteur d'écran, presse-papiers inter-appareils, conformité 3.3.8). Alignement formel au modèle de menace fait en **T-010**.
- **Décisions nécessaires (R7)** : Q1–Q8 (repris et enrichis en T-010).
- **Note livraison** : le 403 initial venait du dépôt **non initialisé** ; résolu une fois `atelier` amorcé.
- **Routage** : `securite` (→ T-010), `patron` (Q1–Q8).
