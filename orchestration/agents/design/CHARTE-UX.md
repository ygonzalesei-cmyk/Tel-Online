# CHARTE UX — TEL ONLINE

> **Document socle du pôle Design.** Toute SPEC d'écran (`SPECS-*.md`) applique cette charte. En cas de conflit, la charte fait foi ; une exception doit être justifiée et tracée dans la SPEC concernée.

| Champ | Valeur |
|---|---|
| Mission | T-006 — Charte UX + SPECS des écrans sensibles |
| Statut | v0.1 — Proposition pour revue en atelier |
| Périmètre d'écriture | `orchestration/agents/design/` (writer unique : pôle Design) |
| Contrainte | On **spécifie**, on ne **code pas** (R12). Lecture seule sur le code applicatif. |
| Cible d'accessibilité | **WCAG 2.2 niveau AA** (viser AAA sur le texte et les parcours critiques quand c'est atteignable) |
| Approche | **Mobile-first**, **offline-first**, **i18n dès le départ**, **langage clair (zéro jargon)** |
| Langues de lancement | 🇫🇷 FR (défaut) · 🇬🇧 EN · 🇪🇸 ES · 🇸🇦 AR (RTL) — architecture extensible (DE, PT, IT…) |

---

## 1. Contexte produit (hypothèses de travail)

> ⚠️ Le pôle Design **ne définit pas** le produit ni le modèle économique. Les points ci-dessous sont des **hypothèses** nécessaires à la cohérence des maquettes ; elles doivent être **confirmées par le pôle Produit**. Elles sont marquées `HYP-#` et reprises comme questions ouvertes en fin de charte.

- `HYP-1` **TEL ONLINE** est une application **grand public** offrant à chaque personne un **coffre numérique personnel et souverain** : les secrets (clés, mots de passe, documents, contacts de confiance) sont **chiffrés et gardés sous le contrôle de l'utilisateur** (auto-conservation / *self-custody*).
- `HYP-2` Le coffre est restaurable via une **phrase de récupération** (secret maître, liste de mots).
- `HYP-3` La **récupération « zéro perte »** combine plusieurs facteurs (phrase + canal eSIM/SMS + sauvegarde chiffrée + contacts de confiance) pour supprimer tout point unique de défaillance.
- `HYP-4` Une **eSIM de secours** fournit une connectivité minimale garantie et un **canal de récupération indépendant** du réseau habituel.
- `HYP-5` Le modèle est **freemium** : socle gratuit + options premium (capacité, data eSIM, fonctions avancées).

**Enjeu transversal :** il s'agit d'un produit **sensible** (secrets, argent, identité). L'UX doit rendre la sécurité **compréhensible et rassurante sans jamais infantiliser ni effrayer**, pour un public **non technique**.

---

## 2. Principes directeurs UX

Dix principes, opposables en revue de design :

1. **Grand public d'abord.** On conçoit pour une personne pressée, non technique, parfois âgée, parfois peu à l'aise avec le numérique. Si un élève de fin de primaire ne comprend pas un écran, on le réécrit.
2. **Zéro jargon.** Aucun terme technique non expliqué (voir §8 et le glossaire §17). On dit « phrase de récupération », pas « seed phrase » ; « carte SIM numérique », pas « profil eSIM ».
3. **Sécurité expliquée, pas subie.** Chaque étape sensible dit *pourquoi* elle existe, en une phrase, avant de demander l'action.
4. **Peu d'étapes, une décision à la fois.** Onboarding et parcours sensibles découpés en petites étapes ; un seul objectif par écran (*progressive disclosure*).
5. **Rien ne se perd.** Tout parcours critique est **interrompable et reprenable** ; l'utilisateur peut toujours revenir en arrière sans perdre son travail.
6. **Fonctionne hors ligne.** Consulter le coffre, préparer une récupération, lire l'aide : possible sans réseau. L'état de connexion est toujours honnête (voir §9).
7. **Rapide et léger.** Perçu comme instantané sur un téléphone d'entrée de gamme et un réseau lent (voir §10).
8. **Accessible par conception.** L'accessibilité (§6) n'est pas une option de fin de projet : elle est un critère d'acceptation de chaque écran.
9. **Prévenir plutôt que corriger.** On empêche les erreurs coûteuses (confirmation, garde-fous, aperçu) plutôt que de les rattraper après coup.
10. **Confiance par la sobriété.** Interface calme, hiérarchie claire, pas d'urgence artificielle ni de *dark patterns*. Le consentement est libre et éclairé.

---

## 3. Publics cibles & personas

| Persona | Profil | Besoins clés | Points de vigilance |
|---|---|---|---|
| **Camille, 34 ans — « pragmatique »** | Active, mobile, peu de temps | Aller vite, comprendre en un coup d'œil, confiance | Impatience, abandon si friction |
| **Robert, 71 ans — « prudent »** | Retraité, peu à l'aise avec le smartphone | Gros caractères, pas de jargon, réassurance, pouvoir demander de l'aide | Petites cibles, textes longs, peur de « tout casser » |
| **Amina, 28 ans — « multilingue »** | Bilingue FR/AR, lit en RTL | Interface dans sa langue, mise en page RTL correcte | Chaînes concaténées, formats de date/nombre |
| **Léa, 22 ans — « nomade connectée »** | Voyage, réseau instable | Fonctionnement hors ligne, eSIM de secours | Perte de connexion en plein parcours |
| **Marc, 45 ans — « déficient visuel »** | Utilise un lecteur d'écran (VoiceOver/TalkBack) | Ordre de lecture logique, libellés, annonces d'état | Éléments non étiquetés, focus perdu |

**Principe :** un écran est validé quand il fonctionne pour **Robert, Amina et Marc**, pas seulement pour Camille.

---

## 4. Mobile-first

- **Cible primaire :** smartphone tenu à une main, **portrait**. Le desktop/tablette est une **amélioration progressive**, jamais l'inverse.
- **Points de rupture (indicatifs) :** `≤ 359px` (très petits), `360–599px` (mobile — référence), `600–1023px` (tablette), `≥ 1024px` (desktop).
- **Zone du pouce :** actions primaires dans le **tiers inférieur** de l'écran ; barre d'action ancrée en bas. On évite les actions critiques en haut d'un grand écran.
- **Cibles tactiles :** **minimum 44 × 44 px** (viser **48 × 48 px**), **espacement ≥ 8 px** entre deux cibles. Aucune action essentielle uniquement au survol.
- **Gestes :** tout geste (glisser, appui long) a **une alternative visible** par bouton. Pas de geste comme seul moyen d'accès (WCAG 2.5.1).
- **Saisie :** clavier adapté au champ (`numérique`, `email`…), autocapitalisation désactivée sur les champs sensibles, pas d'autocorrection sur la phrase de récupération.
- **Orientation :** pas de blocage en portrait/paysage (WCAG 1.3.4) ; la mise en page s'adapte.
- **Reflow :** contenu utilisable **sans défilement à deux dimensions** jusqu'à **320 px de large** et à **400 % de zoom** (WCAG 1.4.10).

---

## 5. Accessibilité — WCAG 2.2 AA

**Engagement :** chaque écran est livré avec sa checklist d'accessibilité renseignée (§16). Critères structurants :

### 5.1 Contrastes (1.4.3 / 1.4.11)
- Texte normal : **≥ 4.5:1** ; texte large (≥ 24px, ou ≥ 18.66px gras) : **≥ 3:1**.
- Composants d'interface et états (bordures de champ, icônes porteuses de sens, focus) : **≥ 3:1**.
- Les valeurs de la palette (§12.1) sont **calculées** pour respecter ces seuils. **La couleur n'est jamais le seul vecteur d'information** (1.4.1) : on double toujours par une icône, un libellé ou un motif.

### 5.2 Gros caractères & zoom
- Corps de texte **≥ 16px** ; respect de la **taille de police système** (Dynamic Type / échelle Android) **jusqu'à 200 %** sans perte de contenu ni de fonction (1.4.4).
- Espacement de texte modifiable sans casse : interligne 1.5×, paragraphe 2×, lettres 0.12em, mots 0.16em (1.4.12).
- Aucune information portée uniquement par une image de texte (1.4.5).

### 5.3 Lecteurs d'écran & sémantique
- Structure **sémantique** (titres hiérarchisés, régions, listes, boutons vs liens) ; rôles/états ARIA quand le natif ne suffit pas.
- **Ordre de focus logique** (2.4.3) suivant l'ordre visuel/lecture ; en RTL, l'ordre suit la lecture droite-à-gauche.
- **Libellés explicites** pour chaque contrôle (2.4.6, 4.1.2) ; le nom accessible contient le texte visible (2.5.3).
- **Annonces d'état** via région *live* (chargement, succès, erreur, passage hors ligne) — polies par défaut, assertives seulement pour les erreurs bloquantes.
- **Focus jamais piégé** (2.1.2) ; à l'ouverture d'une modale, focus déplacé dedans, restitué à la fermeture.
- **Cibler la page par son titre** ; chaque écran a un titre unique et parlant (2.4.2).

### 5.4 Navigation simplifiée & robustesse
- **Tout au clavier / commande vocale** (2.1.1), sans dépendre d'un *timing* (2.2.1 : délais paramétrables/désactivables).
- **Focus visible renforcé** (2.4.7 / 2.4.11) : contour **≥ 3px**, offset 2px, contraste ≥ 3:1 avec le fond **et** le composant.
- **Cible ≥ 24px** minimum garanti même hors mobile (2.5.8) — notre norme interne 44px est plus stricte.
- **Aides à la saisie persistantes** : ne pas redemander une information déjà fournie dans le même parcours (3.3.7).
- **Authentification accessible** (3.3.8) : jamais de test cognitif (recopier, résoudre une énigme) comme seul facteur ; coller/gestionnaire de mots de passe autorisé sauf sur les champs secrets où une alternative est fournie.
- **Mouvement & animation :** respect de `prefers-reduced-motion` ; aucune animation clignotante > 3 fois/s (2.3.1) ; animations décoratives désactivables.

---

## 6. Internationalisation (i18n) — dès le départ

- **Aucune chaîne en dur.** Tous les textes proviennent d'un **catalogue de messages** identifié par clé (`onboarding.coffre.titre`).
- **Pas de concaténation.** On utilise des messages paramétrés (format ICU) pour les pluriels, les genres et les insertions : `{count, plural, one {# mot} other {# mots}}`.
- **Expansion :** prévoir **+40 %** de longueur (FR/DE plus longs que l'EN). Les libellés de boutons ne doivent jamais tronquer ; pas de largeur figée basée sur le FR.
- **RTL natif :** l'arabe est une langue de lancement pour **forcer le support droite-à-gauche dès le début**. Mise en miroir de la mise en page via **propriétés logiques** (début/fin, pas gauche/droite) ; icônes directionnelles (flèches, progression) miroir ; nombres et heures selon la locale.
- **Formats localisés :** dates, nombres, devises, unités et sens de lecture toujours issus de la locale (jamais formatés « à la main »).
- **Langue par défaut = langue de l'appareil**, avec repli `FR → EN`. **Choix de langue accessible très tôt** (dès le 1er écran) et à tout moment dans les réglages.
- **Localisation ≠ traduction :** exemples de récupération, formats de téléphone, mentions légales adaptés au marché.
- **Icônes & couleurs culturellement neutres** ; pas de symbole dont le sens dépend d'une culture unique.

---

## 7. Langage clair & voix éditoriale

- **Niveau de lecture visé :** compréhensible dès la fin du primaire. Phrases courtes, voix active, une idée par phrase.
- **Compatible FALC** (Facile À Lire et à Comprendre) sur les parcours critiques : phrases affirmatives, pas de double négation, vocabulaire courant.
- **Ton :** calme, direct, respectueux, rassurant. On explique *pourquoi*, on ne fait pas peur.
- **On dit / on évite** (extrait, glossaire complet §17) :

| ✅ On dit | ❌ On évite |
|---|---|
| phrase de récupération | seed phrase, mnémonique, entropie |
| carte SIM numérique de secours | profil eSIM, provisioning |
| coffre | vault, wallet, keystore |
| récupérer votre compte | *recovery flow*, restaurer la clé privée |
| chiffré (lisible par vous seul) | AES-256, cryptographie asymétrique |

- **Microcopie :** boutons = verbe d'action (« Continuer », « Enregistrer ma phrase »). Erreurs = ce qui s'est passé **+** comment le résoudre, jamais de code brut.

---

## 8. Offline-first & résilience réseau

- **Local d'abord :** l'app est utilisable sans réseau pour tout ce qui n'exige pas de serveur (consulter le coffre, générer/afficher la phrase, lire l'aide, préparer une récupération).
- **État de connexion honnête :** un indicateur discret mais clair signale « hors ligne » ; les actions nécessitant le réseau sont **mises en file** et explicitement marquées « en attente d'envoi ».
- **Synchronisation :** au retour du réseau, synchro automatique + résolution de conflits transparente ; l'utilisateur est informé du résultat.
- **Pas de perte de saisie :** une coupure réseau ne fait **jamais** perdre une étape ; reprise à l'endroit exact.
- **eSIM de secours** = filet de connectivité : proposée comme solution quand le réseau principal manque, notamment pendant une récupération.
- **Messages réseau :** jamais d'erreur technique brute ; « Pas de connexion — vos modifications seront envoyées dès le retour du réseau. »

---

## 9. Performance

Budgets indicatifs sur **mobile d'entrée de gamme / réseau lent (type 3G, ~400 kb/s, RTT 400ms)** :

| Indicateur | Budget |
|---|---|
| Premier contenu utile (FCP) | ≤ 2.5 s |
| Interactif (TTI) | ≤ 4 s |
| Poids initial (transféré) | ≤ 200 Ko critique |
| Réponse à une interaction (INP) | ≤ 200 ms |
| Fluidité | 60 fps sur les transitions |

- **Squelettes de chargement** (skeletons) plutôt que spinners nus ; contenu progressif.
- **Respect de `prefers-reduced-data`** : images allégées, pas de préchargement lourd.
- **Perçu > mesuré :** privilégier la réactivité immédiate (feedback < 100 ms) même si le résultat complet arrive après.

---

## 10. Sécurité & confiance perçue (UX de sécurité)

Règles UX **transversales** aux écrans sensibles (déclinées précisément dans chaque SPEC) :

- **Auto-conservation claire :** on explique que les secrets restent sur l'appareil et que **personne, pas même TEL ONLINE, ne peut les récupérer à la place de l'utilisateur** — donc la sauvegarde est cruciale.
- **Écrans secrets protégés :** sur les écrans affichant la phrase de récupération, **capture d'écran bloquée** (avec message accessible expliquant pourquoi), masquage par défaut avec bouton « Afficher », aucun envoi au serveur, aucune trace en clair dans les journaux.
- **Champs secrets :** masqués par défaut avec bascule « afficher » accessible ; **pas de suggestion clavier / autocorrection / cache** ; presse-papiers évité par défaut, et si copie autorisée, **effacement automatique** après un court délai + avertissement.
- **Vérifier avant de continuer :** après avoir montré un secret, on **fait confirmer** quelques éléments (ex. mots à replacer) avant de valider — sans transformer ça en test cognitif bloquant (alternative fournie, cf. 3.3.8).
- **Anti-hameçonnage :** rappels contextuels « TEL ONLINE ne vous demandera jamais votre phrase par message/appel ».
- **Pas d'urgence artificielle** ni de consentement pré-coché ; actions destructrices/irréversibles = confirmation explicite décrivant la conséquence.
- **Journalisation :** aucun secret, aucune donnée personnelle sensible dans les logs ou l'analytique ; les événements sont anonymisés et sans contenu.

---

## 11. Système de design (tokens)

> Tokens **de référence** pour cadrer les SPECS. Les valeurs finales seront affinées avec le design visuel ; les **ratios de contraste ci-dessous ont été calculés** et respectent WCAG. Toute nouvelle paire de couleurs doit être validée par le même calcul avant usage.

### 11.1 Couleurs — thème clair (fond `#FFFFFF`)

| Token | Hex | Usage | Contraste vérifié |
|---|---|---|---|
| `color.ink` | `#1A1D21` | Texte principal | **16.9:1** sur blanc — AAA |
| `color.ink-muted` | `#5A6572` | Texte secondaire | **5.9:1** sur blanc — AA |
| `color.primary` | `#1656C7` | Action principale (texte blanc dessus) | **6.6:1** (blanc sur primary) — AA |
| `color.success` | `#17754A` | Succès (texte blanc dessus) | **5.7:1** — AA |
| `color.danger` | `#C42B2B` | Erreur/destructif (texte blanc dessus) | **5.6:1** — AA |
| `color.warning-surface` | `#FFE8B3` | Fond d'alerte (texte `ink` dessus) | **14.0:1** (ink sur surface) — AAA |
| `color.border-strong` | `#6B7684` | Bordures de contrôles (champs, cases) | **4.6:1** sur blanc — ≥ 3:1 UI ✔ |
| `color.border-subtle` | `#D6DCE3` | Séparateurs décoratifs (non essentiels) | 1.4:1 — décoratif uniquement |
| `color.surface-alt` | `#F5F7FA` | Fond de section/carte | — |

### 11.2 Couleurs — thème sombre (fond `#0E1116`)

| Token | Hex | Usage | Contraste vérifié |
|---|---|---|---|
| `color.ink` (sombre) | `#F2F4F7` | Texte principal | ~17:1 — AAA |
| `color.primary` (sombre) | `#6AA6FF` | Action principale / liens | **7.7:1** sur fond sombre — AAA |
| `color.surface` (sombre) | `#171B21` | Cartes/surfaces | — |

> Le thème sombre suit le réglage système et sert aussi l'accessibilité (sensibilité à la lumière) et l'autonomie batterie. **Toute paire texte/fond en sombre est validée ≥ 4.5:1**, chaque composant ≥ 3:1.

### 11.3 Typographie
- Police système (San Francisco / Roboto) pour perf et familiarité ; pas de police décorative sur le texte courant.
- Échelle (base 16px, ratio ~1.25) : `12 · 14 · 16 (corps) · 20 · 25 · 31 · 39`. **Jamais < 12px**, corps **≥ 16px**.
- Interligne corps **1.5** ; titres **1.25**. Longueur de ligne cible 45–75 caractères.
- Respect de l'échelle système jusqu'à 200 % (cf. 5.2).

### 11.4 Espacement, rayons, élévation, cibles
- Échelle d'espacement (px) : `4 · 8 · 12 · 16 · 24 · 32 · 48`.
- Rayons : `8` (contrôles), `16` (cartes/feuilles), `plein` (pastilles).
- Élévation : ombres discrètes ; la hiérarchie passe d'abord par l'espace et le contraste.
- **Cible tactile : 44px min, 48px recommandé** ; espace inter-cibles ≥ 8px.

---

## 12. Bibliothèque de composants (principes communs)

Chaque composant respecte : libellé accessible, focus visible, état désactivé **compréhensible** (pas seulement grisé — un texte explique pourquoi), cible ≥ 44px, contraste ≥ 3:1, comportement RTL.

- **Bouton** (primaire / secondaire / danger / lien) — verbe d'action, un seul bouton primaire par écran.
- **Champ de saisie** — libellé au-dessus (jamais placeholder seul), aide et erreur reliées par `aria-describedby`, erreur annoncée.
- **Champ secret** — masqué par défaut, bascule « afficher », protections §10.
- **Barre d'étapes / progression** — « Étape 2 sur 4 », annoncée aux lecteurs d'écran.
- **Feuille inférieure (bottom sheet)** — actions ancrées, focus géré, fermable au clavier.
- **Bannière d'état** (info / succès / avertissement / erreur / hors-ligne) — icône + couleur + texte.
- **Modale de confirmation** (actions irréversibles) — décrit la conséquence, action destructive à droite/fin, jamais bouton par défaut destructeur.
- **Puce/étiquette** (ex. « Gratuit », « Premium ») — contraste et sens jamais portés par la seule couleur.

---

## 13. Modèles d'états d'écran (obligatoires)

Chaque écran spécifie les **7 états** suivants (ou justifie leur absence) :

1. **Chargement** — squelette, jamais page blanche.
2. **Vide** — explique quoi faire, avec une action claire (pas de cul-de-sac).
3. **Contenu** — état nominal.
4. **Erreur** — cause + solution + action de reprise ; jamais de code technique seul.
5. **Hors ligne** — ce qui reste possible, ce qui est mis en attente.
6. **Succès** — confirmation claire, prochaine étape proposée.
7. **Permission requise** — pourquoi la permission est demandée, demandée **au moment utile** (pas au démarrage).

---

## 14. Onboarding — principes

- **Valeur avant effort :** montrer l'intérêt avant de demander un effort de configuration.
- **Quelques étapes**, une décision par écran, **progression visible** et **reprise** possible.
- **Permissions différées** (notifications, eSIM…) : demandées **en contexte**, avec explication, jamais en rafale au lancement.
- **Rien d'irréversible sans compréhension :** les étapes critiques (phrase de récupération) ne sont pas « passables » à l'aveugle, mais peuvent être **reportées** avec un rappel clair du risque.
- **Sortie de secours :** on peut toujours revenir en arrière ou quitter sans perdre l'état.

---

## 15. Gouvernance & « Definition of Done » design

Un écran/parcours est **« prêt »** quand :

- [ ] Les **7 états** (§13) sont spécifiés.
- [ ] La **checklist WCAG 2.2 AA** de la SPEC est renseignée (contraste, focus, lecteur d'écran, cibles, mouvement).
- [ ] **Toute chaîne** est une clé i18n, sans concaténation, testée en **FR / EN / AR (RTL)** et avec **+40 %** de longueur.
- [ ] La **microcopie** est en langage clair, sans jargon (validée contre le glossaire §17).
- [ ] Les **règles de sécurité UX** (§10) applicables sont couvertes.
- [ ] Le comportement **hors ligne** (§8) est décrit.
- [ ] Les **critères d'acceptation** testables sont listés.
- **Versionnement :** ce document est versionné (SemVer documentaire) ; tout changement passe par revue en atelier. Statut actuel : **v0.1 (proposition)**.

---

## 16. Glossaire (grand public ↔ à éviter)

| Terme grand public (✅) | À éviter / à ne jamais afficher seul (❌) | Note |
|---|---|---|
| Coffre | vault, wallet, keystore | Métaphore centrale du produit |
| Phrase de récupération | seed phrase, mnémonique, clé privée, entropie | Liste de mots secrets |
| Récupération « zéro perte » | disaster recovery, key sharding | Marketing interne à traduire simplement |
| Carte SIM numérique de secours | eSIM, profil, provisioning | Connectivité de repli |
| Contact de confiance | tuteur social, *social recovery guardian* | Personne aidant à récupérer |
| Chiffré (lisible par vous seul) | AES, RSA, asymétrique | Bénéfice, pas la technique |
| Synchroniser | *sync*, réplication | — |
| Abonnement / offre gratuite / offre premium | freemium, SKU, tier | — |

---

## 17. Questions ouvertes (à trancher en atelier)

- `Q1` Confirmer les hypothèses produit `HYP-1` à `HYP-5` (§1).
- `Q2` Longueur de la phrase de récupération (12 vs 24 mots) et vocabulaire (liste de mots localisée ?).
- `Q3` Facteurs exacts de la récupération « zéro perte » et leur ordre de priorité (phrase / eSIM-SMS / sauvegarde chiffrée / contacts de confiance).
- `Q4` L'eSIM de secours est-elle incluse dans l'offre gratuite (quota) ou strictement premium ?
- `Q5` Périmètre précis du gratuit vs premium (capacité, nombre d'appareils, data eSIM).
- `Q6` Liste définitive des langues de lancement et responsabilité de la traduction/relecture.
- `Q7` Contraintes réglementaires (KYC, mentions légales, RGPD) impactant les écrans sensibles.

---

## 18. Références normatives

- **WCAG 2.2** niveau AA (W3C) — critères cités entre parenthèses (ex. « 1.4.3 »).
- **RGAA** (référentiel français) — alignement à viser pour le marché FR.
- Recommandations d'ergonomie mobile (cibles tactiles, zone du pouce) — HIG / Material comme repères, adaptés à nos contraintes.
- **FALC** — Facile À Lire et à Comprendre, pour les parcours critiques.

---

*Fin de la CHARTE UX v0.1. Documents liés : `SPECS-onboarding-coffre.md`, `SPECS-phrase-recuperation.md`, `SPECS-recuperation-zero-perte.md`, `SPECS-esim-secours.md`, `SPECS-abonnement-freemium.md`.*
