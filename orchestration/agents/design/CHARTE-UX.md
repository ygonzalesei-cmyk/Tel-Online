# CHARTE UX — TEL ONLINE

> **Document socle du pôle Design.** Toute SPEC d'écran (`SPECS-*.md`) applique cette charte. En cas de conflit, la charte fait foi ; une exception doit être justifiée et tracée dans la SPEC concernée.

| Champ | Valeur |
|---|---|
| Mission | T-006 — Charte UX + SPECS des écrans sensibles |
| Statut | **v0.2** — Proposition pour revue en atelier (révisée après audit accessibilité / sécurité / i18n) |
| Périmètre d'écriture | `orchestration/agents/design/` (writer unique : pôle Design) |
| Contrainte | On **spécifie**, on ne **code pas** (R12). Lecture seule sur le code applicatif. |
| Cible d'accessibilité | **WCAG 2.2 niveau AA** (viser AAA sur le texte et les parcours critiques quand c'est atteignable) |
| Approche | **Mobile-first**, **offline-first**, **i18n dès le départ**, **langage clair (zéro jargon)** |
| Langues de lancement | 🇫🇷 FR (défaut) · 🇬🇧 EN · 🇪🇸 ES · 🇸🇦 AR (RTL) — architecture extensible (DE, PT, IT…) |

> **Sommaire des sections (renvois vérifiés) :** 1 Contexte · 2 Principes · 3 Personas · 4 Mobile-first · 5 Accessibilité · 6 i18n · 7 Langage clair · 8 Offline · 9 Performance · 10 Sécurité · 11 Système de design · 12 Composants · 13 États d'écran · 14 Onboarding · 15 Gouvernance/DoD · 16 Glossaire · 17 Questions ouvertes · 18 Références · 19 Journal des révisions.

---

## 1. Contexte produit (hypothèses de travail)

> ⚠️ Le pôle Design **ne définit pas** le produit ni le modèle économique. Les points ci-dessous sont des **hypothèses** nécessaires à la cohérence des maquettes ; elles doivent être **confirmées par le pôle Produit**. Elles sont marquées `HYP-#` et reprises comme questions ouvertes (§17).

- `HYP-1` **TEL ONLINE** est une application **grand public** offrant à chaque personne un **coffre numérique personnel et souverain** : les secrets (clés, mots de passe, documents, contacts de confiance) sont **chiffrés et gardés sous le contrôle de l'utilisateur** (auto-conservation / *self-custody*).
- `HYP-2` Le coffre est restaurable via une **phrase de récupération** (secret maître, liste de mots).
- `HYP-3` La **récupération sans perte** (nom interne : « zéro perte ») combine plusieurs facteurs (phrase + canal carte SIM numérique/SMS + sauvegarde chiffrée + contacts de confiance) pour supprimer tout point unique de défaillance.
- `HYP-4` Une **carte SIM numérique de secours** (eSIM) fournit une connectivité minimale garantie et un **canal de récupération indépendant** du réseau habituel.
- `HYP-5` Le modèle est **freemium** : offre gratuite + options premium (capacité, data de la carte SIM de secours, fonctions avancées).

**Enjeu transversal :** produit **sensible** (secrets, argent, identité). L'UX doit rendre la sécurité **compréhensible et rassurante sans jamais infantiliser ni effrayer**, pour un public **non technique**. Les règles propres aux opérations portant sur de la **valeur** sont en §10.2.

---

## 2. Principes directeurs UX

Dix principes, opposables en revue de design :

1. **Grand public d'abord.** On conçoit pour une personne pressée, non technique, parfois âgée, parfois peu à l'aise avec le numérique. Si un élève de fin de primaire ne comprend pas un écran, on le réécrit.
2. **Zéro jargon.** Aucun terme technique non expliqué (voir §7 et le glossaire §16). On dit « phrase de récupération », pas « seed phrase » ; « carte SIM numérique de secours », pas « profil eSIM ».
3. **Sécurité expliquée, pas subie.** Chaque étape sensible dit *pourquoi* elle existe, en une phrase, avant de demander l'action.
4. **Peu d'étapes, une décision à la fois.** Onboarding et parcours sensibles découpés en petites étapes ; un seul objectif par écran (*progressive disclosure*).
5. **Rien ne se perd.** Tout parcours critique est **interrompable et reprenable** ; l'utilisateur peut toujours revenir en arrière sans perdre son travail. **Aucun secret n'est jamais écrasé silencieusement** (voir §8).
6. **Fonctionne hors ligne.** Consulter le coffre, préparer une récupération, lire l'aide : possible sans réseau. L'état de connexion est toujours honnête (voir §8).
7. **Rapide et léger.** Perçu comme instantané sur un téléphone d'entrée de gamme et un réseau lent (voir §9).
8. **Accessible par conception.** L'accessibilité (§5) n'est pas une option de fin de projet : elle est un critère d'acceptation de chaque écran.
9. **Prévenir plutôt que corriger.** On empêche les erreurs coûteuses (confirmation, garde-fous, aperçu) plutôt que de les rattraper après coup.
10. **Confiance par la sobriété.** Interface calme, hiérarchie claire, pas d'urgence artificielle ni de *dark patterns*. Le consentement est libre et éclairé.

---

## 3. Publics cibles & personas

| Persona | Profil | Besoins clés | Points de vigilance |
|---|---|---|---|
| **Camille, 34 ans — « pragmatique »** | Active, mobile, peu de temps | Aller vite, comprendre en un coup d'œil, confiance | Impatience, abandon si friction |
| **Robert, 71 ans — « prudent »** | Retraité, peu à l'aise avec le smartphone | Gros caractères, pas de jargon, réassurance, aide à un endroit stable | Petites cibles, textes longs, peur de « tout casser » |
| **Amina, 28 ans — « multilingue »** | Bilingue FR/AR, lit en RTL | Interface dans sa langue, mise en page RTL correcte, texte bidi correct | Chaînes concaténées, formats de date/nombre, mots latins dans une UI arabe |
| **Léa, 22 ans — « nomade connectée »** | Voyage, réseau instable | Fonctionnement hors ligne, carte SIM numérique de secours | Perte de connexion en plein parcours |
| **Marc, 45 ans — « déficient visuel »** | Utilise un lecteur d'écran (VoiceOver/TalkBack) | Ordre de lecture logique, libellés, annonces d'état, **secrets non lus à voix haute** | Éléments non étiquetés, focus perdu, fuite audible d'un secret |

**Principe :** un écran est validé quand il fonctionne pour **Robert, Amina et Marc**, pas seulement pour Camille.

---

## 4. Mobile-first

- **Cible primaire :** smartphone tenu à une main, **portrait**. Le desktop/tablette est une **amélioration progressive**, jamais l'inverse.
- **Points de rupture (indicatifs) :** `≤ 359px` (très petits), `360–599px` (mobile — référence), `600–1023px` (tablette), `≥ 1024px` (desktop).
- **Zone du pouce :** actions primaires dans le **tiers inférieur** de l'écran ; barre d'action ancrée en bas. On évite les actions critiques en haut d'un grand écran.
- **Cibles tactiles :** **minimum 44 × 44 px** (viser **48 × 48 px**), **espacement ≥ 8 px** entre deux cibles. Aucune action essentielle uniquement au survol.
- **Gestes :** tout geste (glisser, appui long) a **une alternative visible** par bouton. Pas de geste comme seul moyen d'accès (2.5.1) ni de **mouvement de glissement obligatoire** (2.5.7) : une alternative par simple appui existe toujours.
- **Saisie :** clavier adapté au champ (numérique, e-mail…), autocapitalisation désactivée sur les champs sensibles, pas d'autocorrection sur la phrase de récupération.
- **Orientation :** pas de blocage en portrait/paysage (1.3.4) ; la mise en page s'adapte.
- **Reflow :** contenu utilisable **sans défilement à deux dimensions** jusqu'à **320 px de large** et à **400 % de zoom** (1.4.10).

---

## 5. Accessibilité — WCAG 2.2 AA

**Engagement :** chaque écran est livré avec sa checklist d'accessibilité renseignée (DoD §15). Critères structurants :

### 5.1 Contrastes (1.4.3 / 1.4.11)
- Texte normal : **≥ 4.5:1** ; texte large (≥ 24px, ou ≥ 18.66px gras) : **≥ 3:1**.
- Composants d'interface et états (bordures de champ, icônes porteuses de sens, focus) : **≥ 3:1**.
- Les valeurs de la palette (§11.1 et §11.2) sont **calculées** pour respecter ces seuils. **La couleur n'est jamais le seul vecteur d'information** (1.4.1) : on double toujours par une icône, un libellé ou un motif.

### 5.2 Gros caractères & zoom
- Corps de texte **≥ 16px** ; respect de la **taille de police système** (Dynamic Type / échelle Android) **jusqu'à 200 %** sans perte de contenu ni de fonction (1.4.4).
- Espacement de texte modifiable sans casse : interligne 1.5×, paragraphe 2×, lettres 0.12em, mots 0.16em (1.4.12).
- Aucune information portée uniquement par une image de texte (1.4.5).

### 5.3 Lecteurs d'écran & sémantique
- Structure **sémantique** (titres hiérarchisés, régions, listes, boutons vs liens) ; rôles/états ARIA quand le natif ne suffit pas.
- **Ordre de focus logique** (2.4.3) suivant l'ordre visuel/lecture ; en RTL, l'ordre suit la lecture droite-à-gauche.
- **Libellés explicites** pour chaque contrôle (2.4.6, 4.1.2) ; le nom accessible contient le texte visible (2.5.3).
- **Annonces d'état** via région *live* (chargement, succès, erreur, passage hors ligne) — polies par défaut, assertives seulement pour les erreurs bloquantes. **Jamais de région *live* sur un contenu secret.**
- **Secrets & lecteur d'écran — divulgation privée :** un secret (phrase de récupération, code, montant sensible) n'est **jamais lu automatiquement**. Avant toute révélation : proposer de **brancher un casque** ; la lecture se fait **mot à mot, à la demande** (focus sur chaque élément), le contenu masqué reste `aria-hidden` tant qu'il n'est pas révélé. Le parcours « phrase de récupération » est **testé sous lecteur d'écran** (DoD §15). Concerne directement le persona Marc.
- **Focus jamais piégé** (2.1.2) ; à l'ouverture d'une modale, focus déplacé dedans, restitué à la fermeture.
- **Cibler la page par son titre** ; chaque écran a un titre unique et parlant (2.4.2).

### 5.4 Navigation simplifiée & robustesse
- **Tout au clavier / commande vocale** (2.1.1), sans dépendre d'un *timing* (2.2.1 : délais paramétrables/désactivables).
- **Focus non masqué** (2.4.11, AA) : l'élément ciblé n'est jamais caché par un en-tête/pied collant. **Apparence de focus renforcée** (visée AAA 2.4.13) : contour **≥ 3px**, offset 2px, contraste ≥ 3:1 avec le fond **et** le composant.
- **Cible ≥ 24px** minimum garanti même hors mobile (2.5.8) — notre norme interne 44px est plus stricte.
- **Aide à un emplacement cohérent** (3.2.6, A) : le point d'accès à l'aide/au support est au même endroit sur tous les écrans — crucial pour Robert.
- **Contenu au survol/focus** (1.4.13) : bulles/infobulles persistantes, survolables et masquables sans perte de contenu.
- **Navigation cohérente** (3.2.3) et **contournement de blocs** (2.4.1) sur les vues longues ou en usage desktop : un moyen d'atteindre directement le contenu principal.
- **Aides à la saisie persistantes** : ne pas redemander une information déjà fournie dans le même parcours (3.3.7).
- **Finalité de la saisie** (1.3.5) : attributs d'autocomplétion standard sur les champs **non secrets** (nom, e-mail…) ; **désactivés explicitement** sur les champs **secrets** (voir §10).
- **Authentification accessible** (3.3.8) : jamais de test cognitif (recopier, résoudre une énigme) comme seul facteur. Le **collage** et les **gestionnaires de mots de passe** sont **autorisés** ; l'alternative concrète pour la **saisie** d'un secret est définie en §10 (collage autorisé avec effacement immédiat du presse-papiers, ou saisie assistée mot à mot depuis la liste de mots).
- **Mouvement & animation :** respect de `prefers-reduced-motion` ; aucune animation clignotante > 3 fois/s (2.3.1) ; animations décoratives désactivables.

---

## 6. Internationalisation (i18n) — dès le départ

- **Aucune chaîne en dur.** Tous les textes proviennent d'un **catalogue de messages** identifié par clé (`onboarding.coffre.titre`).
- **Pas de concaténation.** On utilise des messages paramétrés (format ICU) pour les pluriels, les genres et les insertions. Les pluriels couvrent **les 6 catégories ICU** (`zero, one, two, few, many, other`) — l'arabe les utilise toutes — et non seulement `one/other` : `{count, plural, zero {aucun mot} one {# mot} other {# mots}}` (catégories à compléter par locale, à **tester en AR**).
- **Texte bidirectionnel (bidi) :** tout fragment inséré d'un **script ou d'une direction différents** de l'UI — mots de la phrase de récupération (latin dans une UI arabe), numéros de téléphone, e-mails, « TEL ONLINE », montants — est **isolé** (isolats Unicode LRI/RLI/FSI, ou `dir="auto"`) pour éviter l'inversion d'ordre. Critique pour les secrets et les codes.
- **Système de chiffres :** fixé **par locale** (occidentaux `0-9` vs arabo-indiens `٠-٩`) ; pour une **phrase de récupération, un code ou un montant**, le système retenu est explicite et cohérent, jamais laissé au hasard du rendu.
- **Expansion par plage de longueur :** les libellés **courts** (un bouton d'un mot) peuvent croître de **100–300 %** (DE), les textes longs de **~30–40 %**. Aucun libellé ne tronque ; pas de largeur figée basée sur le FR. Vérification par **pseudo-localisation**.
- **RTL natif :** l'arabe est une langue de lancement pour **forcer le support droite-à-gauche dès le début**. Mise en miroir via **propriétés logiques** (début/fin, pas gauche/droite) ; icônes directionnelles (flèches, progression) en miroir ; **interligne/hauteur de ligne majorés** pour l'arabe et les scripts à diacritiques (voir §11.3).
- **Formats localisés :** dates, nombres, devises, unités et sens de lecture toujours issus de la locale (jamais formatés « à la main »).
- **Langue par défaut = langue de l'appareil**, avec repli `FR → EN`. **Choix de langue accessible très tôt** (dès le 1er écran) et à tout moment dans les réglages.
- **Localisation ≠ traduction :** exemples de récupération, formats de téléphone, mentions légales adaptés au marché.
- **Icônes & couleurs culturellement neutres** ; pas de symbole dont le sens dépend d'une culture unique.

---

## 7. Langage clair & voix éditoriale

- **Niveau de lecture visé :** compréhensible dès la fin du primaire. Phrases courtes, voix active, une idée par phrase.
- **Compatible FALC** (Facile À Lire et à Comprendre) sur les parcours critiques : phrases affirmatives, pas de double négation, vocabulaire courant.
- **Ton :** calme, direct, respectueux, rassurant. On explique *pourquoi*, on ne fait pas peur.
- **On dit / on évite** (extrait, glossaire complet §16) :

| ✅ On dit | ❌ On évite |
|---|---|
| phrase de récupération | seed phrase, mnémonique, entropie |
| carte SIM numérique de secours | eSIM, profil, provisioning |
| coffre | vault, wallet, keystore |
| récupérer votre compte | *recovery flow*, restaurer la clé privée |
| chiffré (lisible par vous seul) | AES-256, cryptographie asymétrique |
| verrouiller / déverrouiller le coffre | *session lock*, *timeout* |

- **Microcopie :** boutons = verbe d'action (« Continuer », « Enregistrer ma phrase »). Erreurs = ce qui s'est passé **+** comment le résoudre, jamais de code brut.

---

## 8. Offline-first & résilience réseau

- **Local d'abord :** l'app est utilisable sans réseau pour tout ce qui n'exige pas de serveur (consulter le coffre, générer/afficher la phrase, lire l'aide, préparer une récupération).
- **État de connexion honnête :** un indicateur discret mais clair signale « hors ligne » ; les actions nécessitant le réseau sont **mises en file** et explicitement marquées « en attente d'envoi ».
- **Synchronisation & conflits :** au retour du réseau, synchro automatique. **Aucun écrasement silencieux d'un secret** (principe 5) : en cas de divergence sur un contenu du coffre, **les deux versions sont conservées** et l'utilisateur choisit ; seuls les conflits anodins (réglages d'affichage) sont résolus automatiquement. L'utilisateur est toujours informé du résultat.
- **Pas de perte de saisie :** une coupure réseau ne fait **jamais** perdre une étape ; reprise à l'endroit exact.
- **Carte SIM numérique de secours** = filet de connectivité : proposée comme solution quand le réseau principal manque, notamment pendant une récupération.
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
- **Écrans affichant un secret — protection réaliste par plateforme :** masquage par défaut avec bouton « Afficher », aucun envoi au serveur, aucune trace en clair dans les journaux, et masquage de l'**aperçu du multitâche** (snapshot d'app-switcher) dès le passage en arrière-plan. Contre la **capture d'écran** : sur **Android**, blocage effectif (équivalent `FLAG_SECURE`) ; sur **iOS**, le blocage est **impossible** — on se limite à **détecter** la capture et à **avertir** du risque. On ne promet **jamais** un « blocage » universel.
- **Champ d'AFFICHAGE d'un secret (lecture) :** copie **restreinte** par défaut ; si la copie est proposée, **effacement automatique** du presse-papiers après un court délai + avertissement ; presse-papiers marqué **local/non synchronisé** (exclu de Handoff / du presse-papiers universel et de l'historique presse-papiers système).
- **Champ de SAISIE d'un secret (récupération) :** pour respecter l'**authentification accessible (3.3.8)**, le **collage** et les **gestionnaires de mots de passe** sont **autorisés** (saisir 12–24 mots à la main est un obstacle, notamment pour Robert), avec **effacement immédiat** du presse-papiers après collage ; alternative : **saisie assistée mot à mot** avec autocomplétion depuis la liste de mots. Pas de suggestion/autocorrection/cache clavier ; **clavier système sécurisé exigé** (pas de clavier tiers — risque de keylogger/synchro cloud), avec avertissement accessible si indisponible.
- **Vérifier avant de continuer :** après avoir montré un secret, on **fait confirmer** quelques éléments (ex. mots à replacer) avant de valider — sans transformer ça en test cognitif bloquant (alternative fournie, cf. 3.3.8).
- **Anti-hameçonnage :** rappels contextuels « TEL ONLINE ne vous demandera jamais votre phrase par message/appel ».
- **Pas d'urgence artificielle** ni de consentement pré-coché ; actions destructrices/irréversibles = confirmation explicite décrivant la conséquence.
- **Journalisation :** aucun secret, aucune donnée personnelle sensible dans les logs ou l'analytique ; les événements sont anonymisés et sans contenu.

### 10.1 Verrouillage & déverrouillage du coffre
Le verrouillage est le **contrôle de sécurité central** du coffre.
- **Verrouillage automatique** après un **délai d'inactivité paramétrable** (défaut court) ; **verrouillage immédiat** au passage en arrière-plan ; **coffre verrouillé après redémarrage** de l'appareil.
- **Déverrouillage** par **biométrie** (empreinte / reconnaissance faciale) avec **code PIN de repli toujours disponible** (la biométrie n'est jamais l'unique moyen — panne, accessibilité, échec de lecture).
- **Politique d'échec :** nombre d'essais limité, temporisation progressive, message clair et non culpabilisant ; jamais de blocage définitif sans voie de secours (renvoi vers la récupération sans perte).
- **Accessibilité :** l'invite de déverrouillage est entièrement utilisable au lecteur d'écran et au clavier ; le repli PIN respecte 3.3.8.
- L'**état d'écran « Verrouillé »** (§13) est spécifié pour tout écran donnant accès à des données sensibles.

### 10.2 Opérations portant sur de la valeur
Pour tout envoi de valeur ou action irréversible sur un actif :
- **Confirmation explicite** affichant **destinataire et montant en clair** avant validation.
- **Protection anti-substitution** du destinataire collé (attaque « clipboard-swap ») : affichage intégral et mise en évidence de tout changement.
- **Double confirmation** pour les opérations irréversibles ; aucune action de valeur déclenchée par un simple geste.

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
| `color.border-strong` | `#6B7684` | Bordures de contrôles (champs, cases, liseré d'alerte) | **4.6:1** sur blanc — ≥ 3:1 UI ✔ |
| `color.border-subtle` | `#D6DCE3` | Séparateurs décoratifs (non essentiels) | 1.4:1 — décoratif uniquement |
| `color.surface-alt` | `#F5F7FA` | Fond de section/carte | — |

> La surface `warning` claire (`#FFE8B3`) sur fond blanc n'a qu'un liseré ~1.2:1 : toute bannière d'avertissement **doit** porter une bordure `border-strong` (≥ 3:1, 1.4.11) pour rester perceptible (voir §12).

### 11.2 Couleurs — thème sombre (fond `#0E1116`, surface `#171B21`)

Toutes les paires ci-dessous sont **calculées**. En thème sombre, une **action principale** n'utilise **pas** de texte blanc sur bleu clair (échec de contraste) : le bouton porte un **texte foncé sur fond bleu clair**.

| Token | Hex | Usage | Contraste vérifié |
|---|---|---|---|
| `color.ink` | `#F2F4F7` | Texte principal sur fond | **17.2:1** — AAA |
| `color.ink-muted` | `#7E8A99` | Texte secondaire sur fond | **5.4:1** — AA |
| `color.primary-text` | `#6AA6FF` | **Liens / texte** d'action sur fond | **7.7:1** — AAA |
| `color.primary-fill` (+ texte `#0E1116`) | fond `#6AA6FF` | **Bouton** d'action (texte foncé) | **7.7:1** (texte foncé sur fill) — AAA |
| `color.success` | `#46C08A` | Succès (texte/icône) | **8.3:1** — AAA |
| `color.danger` | `#FF6B6B` | Erreur (texte/icône) | **6.8:1** — AAA |
| `color.warning` | `#F2B84B` | Avertissement (texte/icône) | **10.6:1** — AAA |
| `color.border-strong` | `#5B6779` | Bordures de contrôles | **3.3:1** — ≥ 3:1 UI ✔ |

> Le thème sombre suit le réglage système et sert aussi l'accessibilité (sensibilité à la lumière) et l'autonomie batterie. **Chaque paire listée est validée** : texte ≥ 4.5:1, composant ≥ 3:1. Un bouton clair porte un **texte foncé** (jamais blanc sur `#6AA6FF`).

### 11.3 Typographie
- Police système (San Francisco / Roboto) pour perf et familiarité ; pas de police décorative sur le texte courant.
- Échelle (base 16px, ratio ~1.25) : `12 · 14 · 16 (corps) · 20 · 25 · 31 · 39`. **Jamais < 12px**, corps **≥ 16px**.
- Interligne corps **1.5** ; titres **1.25**. Longueur de ligne cible 45–75 caractères.
- **Arabe & scripts à diacritiques :** interligne/hauteur de ligne **majorés (≥ 1.7)** et police adaptée pour ne pas tronquer les signes ; à vérifier en pseudo-localisation.
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
- **Champ secret** — **deux types distincts** : **affichage** (copie restreinte) et **saisie** (collage autorisé, cf. 3.3.8) ; masqué par défaut, bascule « afficher » accessible, protections §10 (divulgation privée, presse-papiers, clavier sécurisé).
- **Invite de déverrouillage** — biométrie + repli PIN, entièrement accessible (§10.1).
- **Barre d'étapes / progression** — « Étape 2 sur 4 », annoncée aux lecteurs d'écran.
- **Feuille inférieure (bottom sheet)** — actions ancrées, focus géré, fermable au clavier.
- **Bannière d'état** (info / succès / avertissement / erreur / hors-ligne) — icône + texte + couleur, **jamais la couleur seule** ; **bordure/liseré ≥ 3:1** contre le fond de page (1.4.11) pour que la limite du composant reste perceptible.
- **Modale de confirmation** (actions irréversibles) — décrit la conséquence, action destructive à droite/fin, jamais bouton par défaut destructeur.
- **Puce/étiquette** (ex. « Gratuit », « Premium ») — contraste et sens jamais portés par la seule couleur.

---

## 13. Modèles d'états d'écran (obligatoires)

Chaque écran spécifie les **8 états** suivants (ou justifie leur absence) :

1. **Chargement** — squelette, jamais page blanche.
2. **Vide** — explique quoi faire, avec une action claire (pas de cul-de-sac).
3. **Contenu** — état nominal.
4. **Erreur** — cause + solution + action de reprise ; jamais de code technique seul.
5. **Hors ligne** — ce qui reste possible, ce qui est mis en attente.
6. **Succès** — confirmation claire, prochaine étape proposée.
7. **Permission requise** — pourquoi la permission est demandée, demandée **au moment utile** (pas au démarrage).
8. **Verrouillé** — pour tout écran donnant accès à des données sensibles : contenu masqué, invite de déverrouillage (biométrie + PIN de repli), aucune fuite dans l'aperçu multitâche (voir §10.1).

---

## 14. Onboarding — principes

- **Valeur avant effort :** montrer l'intérêt avant de demander un effort de configuration.
- **Quelques étapes**, une décision par écran, **progression visible** et **reprise** possible.
- **Permissions différées** (notifications, carte SIM numérique…) : demandées **en contexte**, avec explication, jamais en rafale au lancement.
- **Rien d'irréversible sans compréhension :** les étapes critiques (phrase de récupération) ne sont pas « passables » à l'aveugle, mais peuvent être **reportées** avec un rappel clair du risque.
- **Sortie de secours :** on peut toujours revenir en arrière ou quitter sans perdre l'état.

---

## 15. Gouvernance & « Definition of Done » design

Un écran/parcours est **« prêt »** quand :

- [ ] Les **8 états** (§13, dont « Verrouillé » si données sensibles) sont spécifiés.
- [ ] La **checklist WCAG 2.2 AA** de la SPEC est renseignée (contraste, focus, lecteur d'écran, cibles, mouvement, 3.3.8).
- [ ] Les **secrets** ne fuient pas au **lecteur d'écran** ni à l'**aperçu multitâche** ; le parcours secret est **testé sous lecteur d'écran** (§5.3, §10).
- [ ] Le **verrouillage/déverrouillage** (§10.1) et l'état « Verrouillé » sont couverts si l'écran donne accès à des données sensibles.
- [ ] **Toute chaîne** est une clé i18n, sans concaténation, pluriels ICU complets, **texte bidi isolé**, testée en **FR / EN / AR (RTL)** avec l'**expansion par plage de longueur** (§6).
- [ ] La **microcopie** est en langage clair, sans jargon (validée contre le glossaire §16).
- [ ] Les **règles de sécurité UX** (§10, §10.1, §10.2) applicables sont couvertes.
- [ ] Le comportement **hors ligne** (§8) est décrit, sans écrasement silencieux d'un secret.
- [ ] Les **critères d'acceptation** testables sont listés.
- [ ] Les **renvois de section** de la SPEC ont été vérifiés.
- **Versionnement :** ce document est versionné (SemVer documentaire) ; tout changement passe par revue en atelier. Statut actuel : **v0.2 (proposition)**.

---

## 16. Glossaire (grand public ↔ à éviter)

| Terme grand public (✅) | À éviter / à ne jamais afficher seul (❌) | Note |
|---|---|---|
| Coffre | vault, wallet, keystore | Métaphore centrale du produit |
| Phrase de récupération | seed phrase, mnémonique, clé privée, entropie | Liste de mots secrets |
| **Récupération sans perte** (« plusieurs sécurités ») | « zéro perte » brut, disaster recovery, key sharding | « Récupération zéro perte » = **nom interne** du dispositif ; libellé **grand public** à l'écran : « récupération sans perte » |
| Carte SIM numérique de secours | eSIM, profil, provisioning | Connectivité de repli |
| Verrouiller / déverrouiller le coffre | *session lock*, *timeout* | Biométrie + code PIN de repli |
| Contact de confiance | tuteur social, *social recovery guardian* | Personne aidant à récupérer |
| Chiffré (lisible par vous seul) | AES, RSA, asymétrique | Bénéfice, pas la technique |
| Synchroniser | *sync*, réplication | — |
| Offre gratuite / offre premium | freemium, SKU, tier | — |

---

## 17. Questions ouvertes (à trancher en atelier)

- `Q1` Confirmer les hypothèses produit `HYP-1` à `HYP-5` (§1).
- `Q2` Longueur de la phrase de récupération (12 vs 24 mots) et vocabulaire (liste de mots localisée ?).
- `Q3` Facteurs exacts de la récupération sans perte et leur ordre de priorité (phrase / SIM numérique-SMS / sauvegarde chiffrée / contacts de confiance).
- `Q4` La carte SIM numérique de secours est-elle incluse dans l'offre gratuite (quota) ou strictement premium ?
- `Q5` Périmètre précis du gratuit vs premium (capacité, nombre d'appareils, data de la SIM de secours).
- `Q6` Liste définitive des langues de lancement, système de chiffres par locale, et responsabilité de la traduction/relecture.
- `Q7` Contraintes réglementaires (KYC, mentions légales, RGPD) impactant les écrans sensibles.
- `Q8` Périmètre « valeur/argent » (§10.2) : quelles opérations, quelles règles de confirmation, quelle SPEC dédiée ?

---

## 18. Références normatives

- **WCAG 2.2** niveau AA (W3C) — critères cités entre parenthèses (ex. « 1.4.3 »). Nouveautés 2.2 mobilisées : 2.4.11, 2.5.7, 2.5.8, 3.2.6, 3.3.7, 3.3.8.
- **RGAA** (référentiel français) — alignement à viser pour le marché FR.
- Recommandations d'ergonomie mobile (cibles tactiles, zone du pouce) — HIG / Material comme repères, adaptés à nos contraintes.
- **FALC** — Facile À Lire et à Comprendre, pour les parcours critiques.
- **ICU MessageFormat** — pluriels/genres/insertions ; **algorithme bidi Unicode (UAX #9)** — isolats.

---

## 19. Journal des révisions

- **v0.2** — Révision après audit adversarial (accessibilité / sécurité / i18n) :
  - Ajout du **verrouillage/déverrouillage** du coffre (§10.1) et de l'état d'écran **« Verrouillé »** (§13).
  - **Divulgation privée des secrets** au lecteur d'écran, interdiction des régions *live* sur un secret (§5.3).
  - Protection **capture d'écran réaliste par plateforme** + aperçu multitâche + presse-papiers inter-appareils / clavier sécurisé (§10).
  - Distinction **champ d'affichage vs de saisie** d'un secret et conformité **3.3.8** (§10, §5.4).
  - **Palette sombre** complétée et calculée ; distinction **lien vs bouton** (§11.2) ; liseré d'alerte ≥ 3:1 (§11.1, §12).
  - i18n : **6 catégories de pluriel ICU**, **isolation bidi**, système de chiffres par locale, **expansion par plage de longueur**, interligne AR majoré (§6, §11.3).
  - **Citations WCAG corrigées** (2.4.13 pour l'apparence du focus) et critères ajoutés (2.5.7, 3.2.6, 1.4.13, 1.3.5, 2.4.1, 3.2.3).
  - **Stratégie de conflits** pour secrets, sans écrasement silencieux (§8) ; **opérations de valeur** (§10.2).
  - **Correction de tous les renvois de section** + harmonisation terminologique (« carte SIM numérique de secours », « récupération sans perte »).
- **v0.1** — Version initiale.

---

*Fin de la CHARTE UX v0.2. Documents liés : `SPECS-onboarding-coffre.md`, `SPECS-phrase-recuperation.md`, `SPECS-recuperation-zero-perte.md`, `SPECS-esim-secours.md`, `SPECS-abonnement-freemium.md`.*
