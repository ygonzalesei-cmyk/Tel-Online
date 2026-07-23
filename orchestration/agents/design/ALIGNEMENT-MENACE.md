# ALIGNEMENT MENACE ↔ UX — TEL ONLINE (T-010)

> **Objet.** Aligner les SPECS des écrans sensibles (T-006) sur le **modèle de menace** de la Sécurité
> (`orchestration/agents/securite/MODELE-MENACE.md` + registre `SEC-###` / garde-fous `VETO-###`), et fournir au
> patron une **fiche de décisions produit Q1–Q8** prête à arbitrer.
>
> **Rôle & limites (R2/R12).** Le Design **spécifie la mesure UX** ; il **ne code pas** (R12). Les mécanismes
> techniques cités (FLAG_SECURE, détection d'overlay, liaison autofill au package, pinning SM-DP+, signature
> d'intention matérielle…) sont **routés** vers les modules concernés (mobile/infra/sécurité) — ils sont
> nommés ici comme **exigence**, pas implémentés. La Sécurité pose le **veto** ; le Design décline **l'expérience**.
>
> **Accessibilité.** Aucune mesure ci-dessous ne dégrade l'accessibilité : **WCAG 2.2 AA maintenu** (cf. `CHARTE-UX.md` §5 et section accessibilité de chaque SPEC). Les points de tension (anti-capture vs lecteur d'écran, presse-papiers vs 3.3.8) sont traités explicitement.

| Champ | Valeur |
|---|---|
| Mission | T-010 — Alignement SPECS ↔ modèle de menace + arbitrages Q1–Q8 |
| Base | `origin/atelier` (T-006 fusionné : PR #2) |
| Entrées | `securite/MODELE-MENACE.md`, `securite/REGISTRE.md` (42 SEC-###, 24 VETO), SPECS T-006 |
| Écrans couverts | onboarding coffre · phrase de récupération · récupération « zéro perte » · carte SIM numérique de secours (eSIM) |
| Statut | v0.1 — proposition pour revue en atelier |

---

## 1. Les 4 vecteurs UX imposés (DoD T-010) et leur ancrage menace

| Vecteur UX | SEC-### / VETO | Réalité par plateforme (ce que l'UX doit refléter) |
|---|---|---|
| **Capture d'écran** | **SEC-025** (P1) · **VETO-V14** · VETO-V6 | **Android** : blocage réel (équiv. `FLAG_SECURE`) de la capture, de l'enregistrement **et** de l'aperçu multitâche. **iOS** : la capture **ne peut PAS être bloquée** → **occulter l'aperçu multitâche** (vue masquée en arrière-plan) **+ détecter** la capture et **avertir**. Aucun SDK tiers (session-replay/crash) sur ces écrans (VETO-V6). |
| **Overlay / tapjacking** | **SEC-004** (P0) · angle mort **D.1** (partage d'écran / RAT) | Sur écran sensible : si une **sur-couche** (overlay) ou un **partage/enregistrement d'écran** est détecté → **masquer** le contenu sensible + avertir ; **ignorer les appuis obscurcis** (pas de validation « à l'aveugle ») ; **cooldown** sur actions déclenchées pendant un partage d'écran détecté. |
| **Phishing de la seed** | **SEC-004** (P0) · **VETO-V15** · **VETO-V11** | La phrase **n'est JAMAIS** saisie en ligne, ni demandée par le support, ni envoyée au serveur ; **vérification 100 % locale** (re-dérivation cliente) ; **pédagogie anti-phishing** persistante ; **notifications hors-bande signées** + **délais** sur actions sensibles ; **jamais de SMS/appel/MSISDN** comme facteur (V11). |
| **Autofill / presse-papiers** | **SEC-026** (P1) · **VETO-V14** · angle mort **D.1** (autofill) | Secrets **non copiables** par défaut ; si copie/collage : **presse-papiers local, non synchronisé** (exclu de Handoff / presse-papiers universel / historique), **effacement automatique** ; **clavier système sécurisé** exigé (pas d'IME tiers) ; **autofill lié à l'origine/au package vérifié** (pas de *fuzzy-match*), **jamais d'autofill en WebView** ni d'un secret racine par un gestionnaire tiers. |

---

## 2. Table de traçabilité — menace `SEC-###` ↔ mesure UX

> Priorité aux **P0 « UX »**. Colonne « SPEC » = où la mesure est spécifiée (section « Alignement modèle de menace (T-010) » de chaque SPEC, sauf mention).

| SEC-### | Grav. | Menace (résumé) | Vecteur UX | Écran(s) | Mesure UX (Design) | WCAG maintenu | SPEC |
|---|---|---|---|---|---|---|---|
| **SEC-004** | **P0** | Phishing de la phrase de récupération (faux écran/overlay/support usurpé) | Phishing seed · Overlay | Phrase · Zéro-perte · (Onboarding: éducation) | Seed jamais saisie en ligne/demandée par support ; vérif locale ; anti-overlay/anti-tapjacking ; pédagogie ; notif hors-bande | Bandeau = texte réel (lecteur d'écran) ; avertissement overlay en région live assertive | phrase, zéro-perte, onboarding |
| **SEC-001/002/003** | **P0** | Récupération reconstructible serveur / rotation seed sans step-up (rançongiciel de compte) | Phishing seed | Zéro-perte | « Même TEL ONLINE ne peut pas récupérer à votre place » ; **aucun chemin support** ; step-up + **délai/cooldown** + notif hors-bande signée avant rotation ; undo | Messages en langage clair ; step-up avec repli accessible | zéro-perte |
| **SEC-005/006/007** | **P0** | Transfert eSIM = primitive de SIM-swap ; altération EID/code d'activation | Overlay · Phishing (quishing) | eSIM · Zéro-perte | Confirmation **hors-bande signée** + cooldown + gel financier post-transfert ; step-up lié à l'ancien appareil ; **WYSIWYS** (EID+appareil affichés = signés) ; QR in-app uniquement | Confirmations accessibles ; QR a une alternative texte/manuelle | eSIM, zéro-perte |
| **SEC-018** | **P0** | Déverrouillage biométrique non lié matériellement à la clé | Autofill/saisie · Capture | Onboarding | Biométrie **+ repli code PIN toujours** ; ré-enrôlement biométrique invalide l'accès ; présence utilisateur exigée | Repli PIN accessible (3.3.8) ; invite lisible lecteur d'écran | onboarding |
| **SEC-013** (VETO-V3) | **P0** | Mot de passe maître en clair / KDF négociable serveur | Autofill/presse-papiers | Onboarding | Mot de passe maître **jamais transmis** ; clavier sécurisé ; pas d'enregistrement par gestionnaire tiers | Clavier sécurisé reste navigable/gros caractères | onboarding |
| **SEC-025** | P1 | Fuite d'écran/seed via snapshot multitâche, capture, session-replay, SDK tiers | Capture | Onboarding · Phrase · Zéro-perte · eSIM | Anti-capture **par plateforme** (Android bloque / iOS occulte+détecte) ; occultation multitâche ; **aucun SDK tiers** sur ces écrans | Bandeau « écran protégé » = texte réel ; **divulgation privée** au lecteur d'écran (casque) | les 4 |
| **SEC-026** | P1 | Presse-papier / IME tiers captant mot de passe maître, seed, PAN | Autofill/presse-papiers | Onboarding · Phrase · Zéro-perte · eSIM | Copie désactivée par défaut ; presse-papiers **local + effacement** ; **clavier sécurisé** ; avertissement si IME tiers | Alternative de saisie assistée conforme **3.3.8** ; collage autorisé où requis | les 4 |
| **SEC-030/034** | P1 | QR/code d'activation eSIM spoofé (quishing), rejeu, BPP altéré | Phishing (quishing) · Overlay | eSIM | QR **in-app uniquement** (jamais scanné depuis e-mail/SMS/site) ; code **usage unique** ; profil lié compte+appareil | Alternative manuelle au QR ; messages clairs | eSIM |
| **SEC-031** | P1 | MFA fatigue / prompt-bombing / push spoofé | Phishing · Overlay | Zéro-perte · eSIM | **Number-matching** + **contexte affiché** sur toute approbation ; limitation de fréquence ; passkey plutôt que push simple | Number-matching lisible/gros caractères ; annonce d'état | zéro-perte, eSIM |
| **SEC-042** | P2 | Push exposant OTP/montants/contacts sur écran verrouillé | (transverse) | Zéro-perte · eSIM | Notifications **sans contenu sensible** (révélé après déverrouillage) ; aucun OTP en push | Contenu révélé après déverrouillage reste accessible | zéro-perte, eSIM |
| **SEC-027** (VETO-V16) | P1 | Sauvegarde OS (iCloud/Google) exfiltrant coffre/clés/seed | (message UX) | Onboarding | Message d'onboarding : « votre coffre **n'est pas copié** dans les sauvegardes iCloud/Google » ; exclusion de backup (routage module) | Message en langage clair | onboarding |
| **D.1 port-out / renvoi d'appel** | **P0** (angle mort) | Portage sortant du numéro & renvoi d'appel/voicemail = chemins de SIM-swap | Phishing · (télécom) | eSIM · Zéro-perte | **Verrou de portage** (PIN + gel + notif hors-bande + cooldown) ; **notification** de tout changement de renvoi d'appel/messagerie | Notifications accessibles | eSIM, zéro-perte |
| **D.1 autofill (AutoSpill)** | P1 (angle mort) | Autofill vers mauvais package/domaine, remplissage en WebView, overlay sur le prompt | Autofill | Onboarding · Phrase · Zéro-perte | Autofill **lié au package/à l'origine vérifiés** ; **jamais en WebView** ; confirmation avant remplissage cross-app ; détection d'overlay pendant le remplissage | Confirmation accessible ; pas de fuzzy-match trompeur | onboarding, phrase, zéro-perte |
| **D.1 partage d'écran / RAT** | P1 (angle mort) | Victime social-engineerée installe un outil de partage d'écran et opère elle-même | Overlay/capture | les 4 | **Détection de partage/enregistrement d'écran** → masquer seed/code/validation + avertir ; **cooldown** sur actions pendant partage détecté | Avertissement en région live assertive | les 4 |

> **Garde-fous transverses mobilisés :** VETO-V14 (anti-capture/clipboard), V15 (seed jamais en ligne), V11 (jamais SMS/MSISDN facteur), V6 (pas de SDK tiers sur écrans sensibles), V5 (récupération sans backdoor serveur), V13 (biométrie liée matériel + repli), V19 (WYSIWYS signature d'intention), V24 (deep links/QR vérifiés).

---

## 3. Matrice **écran × vecteur** (synthèse)

Légende : ✅ couvert par la SPEC (T-006 + addendum T-010) · ➕ renforcé/ajouté en T-010 · ↗ routé (mécanisme hors UX).

| Écran ↓ / Vecteur → | Capture (par plateforme) | Overlay / tapjacking | Phishing de la seed | Autofill / presse-papiers |
|---|---|---|---|---|
| **Onboarding coffre** | ✅ écrans code/mot de passe maître protégés ; occultation multitâche ; iOS détecte | ➕ masquage + appuis obscurcis ignorés pendant saisie du code/biométrie | ➕ éducation « jamais demandé par message/appel » ; code jamais envoyé serveur | ➕ mot de passe maître exclu autofill/enreg. tiers ; clavier sécurisé ; ↗ exclusion backup OS |
| **Phrase de récupération** | ✅ (E2/E4) anti-capture, occultation, détection iOS ➜ **SEC-025/V14** | ➕ détection overlay/partage d'écran → masquage phrase + anti-tapjacking sur « Afficher »/validation | ✅➕ jamais en ligne, **vérif locale**, pédagogie, notif hors-bande ➜ **SEC-004/V15** | ✅ copie off par défaut, clipboard cloud exclu ; ➕ champ vérif exclu autofill, pas de fuzzy-match |
| **Récupération « zéro perte »** | ✅ facteurs affichés (phrase/code) anti-capture par plateforme | ➕ récupération = moment d'attaque : masquage + cooldown si partage d'écran | ✅➕ saisie **locale only** ; **aucun support** (V5) ; step-up + délai + notif signée ; number-matching (anti prompt-bombing) | ➕ champs facteurs exclus autofill ; clavier sécurisé ; collage → effacement |
| **Carte SIM numérique de secours (eSIM)** | ✅ écran **code/QR d'activation** anti-capture ; occultation multitâche | ➕ transfert = action sensible : anti-overlay/anti-tapjacking sur la validation | ➕ **anti-quishing** (QR in-app only) ; profil lié compte+appareil ; ↗ pinning SM-DP+ ; ➕ verrou port-out/renvoi d'appel | ➕ code d'activation non copiable vers presse-papiers synchronisé |

---

## 4. Fiche décisions produit **Q1–Q8** (pour arbitrage patron)

> Reprend les questions ouvertes de la charte (`CHARTE-UX.md` §17), **réévaluées à la lumière du modèle de menace**, et intègre les décisions routées par la Sécurité (`REGISTRE.md` §D.3). Format : **Question · Enjeu (UX ⊕ sécurité) · Options · Recommandation Design · SEC liés**. Case « Décision patron » à cocher en atelier.

### Q1 — Confirmer les hypothèses produit (coffre ZK, self-custody, freemium)
- **Enjeu :** toute la charte et les SPECS reposent sur `HYP-1…HYP-5`. Le modèle de menace **confirme** l'exigence zero-knowledge (VETO-V3/V5) et self-custody.
- **Options :** (a) confirmer ; (b) amender le périmètre.
- **Reco Design :** **confirmer** ; c'est cohérent avec le modèle ZK.
- **SEC liés :** cadrage S0–S2. **Décision patron : ☐**

### Q2 + Q3 — ⭐ **Un seul secret (kit de récupération) OU deux secrets (phrase + mot de passe maître) ?** *(décision structurante)*
- **Enjeu :** la Sécurité (§D.2) juge le **double secret sur-dimensionné** pour le grand public : il **dégrade l'UX**, **pousse à photographier la seed** (annule l'anti-capture VETO-V14 et **alimente le phishing SEC-004**), et augmente le **DoS de verrouillage permanent** (SEC-037/038). Un **kit de récupération unique à haute entropie, hardware-backed** offre le même niveau ZK.
- **Options :** (a) **kit unique** hardware-backed ; (b) double secret (modèle wallet crypto).
- **Reco Design :** **(a) kit unique** — moins de phishing, moins d'abandon, aligné « grand public » de la charte. Si (b) retenu : renforcer la pédagogie et **interdire** toute incitation à photographier.
- **SEC liés :** SEC-004 (P0), SEC-037/038, VETO-V14/V15. **Décision patron : ☐ (a) ☐ (b)**
- **Sous-question Q2 :** longueur (12 vs 24 mots) et **liste de mots localisée** — à trancher **après** Q2/Q3.

### Q3bis — Facteurs de la récupération « sans perte » et leur priorité
- **Enjeu :** supprimer tout point unique **sans** réintroduire de backdoor (VETO-V5) ni de SMS (VETO-V11). Récupération sociale (Shamir) **dont aucune part n'est détenue par l'opérateur**.
- **Options :** kit hors-ligne · récupération sociale (contacts de confiance) · clé matérielle · code de secours. Ordre de priorité à fixer.
- **Reco Design :** proposer **≥ 2 facteurs indépendants**, priorité kit hors-ligne + récupération sociale ; **jamais** l'OTP SMS/voix.
- **SEC liés :** SEC-001/002/003 (P0), VETO-V5/V11. **Décision patron : ☐**

### Q4 + Q5 — Carte SIM numérique de secours : incluse dans l'offre gratuite (quota) ou premium ? Périmètre gratuit vs premium ?
- **Enjeu :** la SIM de secours est aussi un **canal de récupération** (indépendant du réseau). La rendre 100 % premium peut **priver un utilisateur gratuit d'un facteur de récupération**. Le **numéro n'est jamais un facteur d'authentification** (V11) — c'est un canal de **connectivité/data**, pas un OTP.
- **Options :** (a) quota data de secours minimal **gratuit** + premium étendu ; (b) strictement premium.
- **Reco Design :** **(a)** — un filet de connectivité de secours minimal pour tous préserve la récupération ; premium pour le volume.
- **SEC liés :** SEC-005/006 (P0), VETO-V11. **Décision patron : ☐ (a) ☐ (b)**

### Q6 — Langues de lancement, **système de chiffres par locale**, responsabilité traduction
- **Enjeu :** i18n dès le départ (charte §6). Pour un **code/une phrase/un montant**, le système de chiffres (occidental `0-9` vs arabo-indien `٠-٩`) doit être **fixé par locale** (sinon ambiguïté critique). RTL (arabe) = langue de lancement.
- **Options :** liste FR/EN/ES/AR (proposée) ± DE/PT ; qui traduit/relit.
- **Reco Design :** figer FR/EN/ES/AR au lancement ; **chiffres occidentaux** pour les secrets/codes par défaut, à confirmer par locale.
- **SEC liés :** — (qualité i18n). **Décision patron : ☐**

### Q7 — Contraintes réglementaires (KYC/AML, RGPD, PCI, PVID/eIDAS)
- **Enjeu :** flux financier ⇒ **KYC/AML obligatoire** (VETO-V4) ; **DPIA RGPD Art. 35** + base **Art. 9** pour le liveness biométrique ; **Art. 22** (décision automatisée : human-in-the-loop + contestation pour gel/rejet KYC-AML) ; **PCI-DSS** si PAN/CVV affichés ; **PVID/eIDAS** pour la vérification d'identité.
- **Options :** cadrage conformité à lancer (routé T-003 / conformité).
- **Reco Design :** prévoir dans les SPECS **écran de contestation/recours** (Art. 22) et **révélation PAN/CVV sous step-up** ; ne pas sur-vendre l'« identité numérique ».
- **SEC liés :** VETO-V4, SEC-010/033, Annexe C. **Décision patron : ☐ (routage conformité)**

### Q8 — Périmètre « valeur / argent » (portefeuille = monnaie électronique ?)
- **Enjeu :** un solde = **monnaie électronique** ⇒ **agrément EMI + safeguarding** (EMD2) ; **SCA à liaison dynamique** (V19) et **SCA sur l'accès** au wallet (DSP2) ; **révélation carte** sous step-up (PCI).
- **Options :** (a) portefeuille avec solde (EMI/partenariat) ; (b) périmètre réduit sans e-money au lancement.
- **Reco Design :** décision **produit/conformité** ; côté UX, prévoir **WYSIWYS** (montant+bénéficiaire = signés, V19) et confirmation destinataire anti-« clipboard-swap ».
- **SEC liés :** SEC-008/009/010 (P0), VETO-V1/V4/V19, Annexe C (EMD2/PCI). **Décision patron : ☐ (a) ☐ (b)**

### Décisions supplémentaires routées (Sécurité §D.3) — pour information patron
- **Client web / PWA + desktop** confirmé en périmètre (VISION) : surface **supply-chain/XSS** du crypto en JS ⇒ **UX des flux sensibles à décliner** pour le web (routage Design + Infra). *(Impacte de futures SPECS.)*
- **Isolation LI / rétention télco** vs coffre ZK : frontière à prouver (routage Infra).

---

## 5. Portée, preuves et suites

- **Périmètre respecté (R3) :** seuls `orchestration/agents/design/SPECS-*.md` (4 écrans sensibles) et ce fichier sont modifiés/créés ; lecture seule sur le code et sur les fichiers d'autres agents.
- **Ce que le Design NE fait PAS (R12) :** implémenter FLAG_SECURE, la détection d'overlay, la liaison autofill, le pinning SM-DP+, la signature d'intention matérielle. Ces **mécanismes sont routés** (mobile/infra/sécurité) ; le Design en **spécifie l'expérience et l'exigence**.
- **WCAG :** chaque mesure a sa contrepartie d'accessibilité (voir colonne dédiée §2 et section « Alignement modèle de menace (T-010) » de chaque SPEC).
- **Suite :** revue en atelier ; arbitrage **Q1–Q8** (surtout **Q2/Q3** — un ou deux secrets) ; puis mise à jour des SPECS selon les décisions.

---

*Fin de `ALIGNEMENT-MENACE.md` v0.1. Documents liés : `CHARTE-UX.md`, `SPECS-onboarding-coffre.md`, `SPECS-phrase-recuperation.md`, `SPECS-recuperation-zero-perte.md`, `SPECS-esim-secours.md`, `../securite/MODELE-MENACE.md`, `../securite/REGISTRE.md`.*
