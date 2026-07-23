# SPEC — Récupération « zéro perte » (Configuration & Récupération)

Charte lue et appliquée. Les SPECS liées (`SPECS-phrase-recuperation.md`, `SPECS-esim-secours.md`) n'existent pas encore dans le dossier ; je crée les renvois vers leurs **noms de fichiers canoniques** listés dans le bloc **« Documents liés »** de clôture de la charte (les termes grand public correspondants figurent au **glossaire §16**), sans dupliquer leur contenu. Pour ne dépendre d'aucun fichier absent, les **comportements de sécurité critiques de la saisie du secret** (écran B3) sont **spécifiés directement dans cette SPEC** (§5/B3 et §9) ; la chorégraphie détaillée pas-à-pas de ces sous-parcours restera à consolider dans les fichiers dédiés (dépendance signalée, non bloquante pour les garanties listées ici).

## 0. Métadonnées

| Champ | Valeur |
|---|---|
| Titre | Récupération « zéro perte » — Volet A (Configuration des facteurs) & Volet B (Récupération effective) |
| Statut | **v0.2 — proposition corrigée** (pour revue en atelier) |
| Mission | recuperation-zero-perte |
| Charte de référence | `orchestration/agents/design/CHARTE-UX.md` (v0.1) — fait foi en cas de conflit |
| Écrans couverts (Volet A) | `A0` Tableau « Ma protection » · `A1` Ajouter la phrase de récupération (renvoi) · `A2` Ajouter la carte SIM numérique de secours (renvoi) · `A3` Activer la sauvegarde chiffrée · `A4` Ajouter un contact de confiance · `A5` Récapitulatif du niveau de protection |
| Écrans couverts (Volet B) | `B0` Démarrer une récupération · `B1` Vous identifier · `B2` Choisir une protection · `B3` Parcours guidé par facteur · `B4` Cette protection ne marche pas (alternative) · `B5` Compte récupéré (succès) |
| Renvois SPEC | Phrase de récupération → `SPECS-phrase-recuperation.md` · Carte SIM numérique de secours → `SPECS-esim-secours.md` · Onboarding → `SPECS-onboarding-coffre.md` |
| Hypothèses produit utilisées | `HYP-1` coffre souverain (auto-conservation) · `HYP-2` phrase de récupération = secret maître · `HYP-3` récupération « zéro perte » = 4 facteurs sans point unique de défaillance · `HYP-4` **carte SIM numérique de secours** = canal indépendant · `HYP-5` freemium |
| Questions ouvertes déclenchées | `Q3` (facteurs exacts et priorité) · `Q4` (carte SIM numérique de secours incluse ou premium) · `Q6` (langues) · `Q7` (KYC/RGPD) |

**Convention de nommage & i18n.** Toutes les clés de cette SPEC sont préfixées `rzp.` (récupération zéro perte). Aucune chaîne n'est en dur ; aucune n'est concaténée ; les pluriels utilisent le format ICU. **Toute variable interpolée** (`{count}`, `{total}`, `{minutes}`, et tout `{name}` affiché après preuve de possession) est **isolée bidirectionnellement** (FSI/PDI ou équivalent plateforme) et **tous les nombres passent par le formateur numérique de la locale** (voir §7, règles i18n transversales).

---

## 1. Objectif & enjeux

**Rôle du parcours.** Ce parcours a un double rôle, correspondant à deux moments de vie très différents :

- **Volet A — CONFIGURATION (à froid, l'utilisateur va bien).** Aider chaque personne à mettre en place **plusieurs façons de récupérer son coffre**, pour qu'**aucune perte unique** (téléphone volé, code oublié, mot perdu) ne rende le coffre inaccessible. On rend visible un **niveau de protection** simple : « combien de protections sont actives ». Objectif émotionnel : passer de « j'ai peur de tout perdre » à « je suis tranquille ».
- **Volet B — RÉCUPÉRATION (à chaud, l'accès est perdu).** Aider une personne qui a **changé de téléphone** ou **oublié son code** à **retrouver son coffre**, en la guidant pas à pas, en lui laissant toujours **au moins une autre option** si une protection ne fonctionne pas, et en confirmant clairement que tout est restauré.

**Pourquoi ce parcours est SENSIBLE.**

- **Secrets.** Il touche à la **phrase de récupération** (secret maître, `HYP-2`) et aux facteurs qui la protègent. Une divulgation = perte totale de contrôle du coffre.
- **Argent & identité.** Le coffre contient clés, mots de passe, documents et contacts de confiance (`HYP-1`). Une usurpation lors d'une récupération = vol d'identité numérique et, potentiellement, d'argent.
- **Irréversibilité (auto-conservation).** Conformément à la charte §10 : **personne, pas même TEL ONLINE, ne peut récupérer les secrets à la place de l'utilisateur**. Donc si la configuration (Volet A) est bâclée, le Volet B **ne peut rien réparer**. La qualité du Volet A conditionne la survie du coffre — d'où l'exigence UX de rendre la configuration **désirable et compréhensible**, jamais anxiogène.
- **Cible d'un hameçonnage et d'un ciblage.** Le Volet B est le moment rêvé pour un attaquant (« aidez-moi à récupérer votre compte »). L'anti-hameçonnage **et l'anti-fuite d'informations avant authentification** sont donc structurants (§9).

---

## 2. Personas & cas d'usage concernés

Renvoi aux personas de la charte §3. Priorités pour ce parcours :

| Persona (charte §3) | Concerné par | Pourquoi c'est décisif ici |
|---|---|---|
| **Robert, 71 ans — prudent** | A & B | A **la peur de « tout casser »** : le niveau de protection doit rassurer sans jargon ; la récupération doit être guidée, gros caractères, sans test cognitif. Persona de validation prioritaire. |
| **Léa, 22 ans — nomade connectée** | A & B | Réseau instable, risque de perte/vol du téléphone en voyage : c'est **la cliente type de la carte SIM numérique de secours** comme canal de récupération indépendant (`HYP-4`). |
| **Marc, 45 ans — déficient visuel** | A & B | La récupération étant un moment de stress, l'**ordre de focus**, les **annonces d'état** et l'**absence de piège au clavier** sont critiques. Validation prioritaire. |
| **Amina, 28 ans — multilingue (AR-RTL)** | A & B | Les libellés de niveau de protection et les messages d'échec/alternative doivent **fonctionner en RTL et à +40 % de longueur**, sans concaténation, avec **isolation bidi** des variables et **miroir directionnel** de la jauge et des flèches. |
| **Camille, 34 ans — pragmatique** | A | Voudra configurer vite : la config doit être **découpée, reportable, reprenable**, avec valeur visible d'emblée. Ne doit pas être la seule persona validante (charte §3). |

**Principe (charte §3) :** l'écran est validé quand il marche pour **Robert, Amina et Marc**, pas seulement pour Camille.

---

## 3. Pré-requis, points d'entrée et de sortie

### 3.1 Pré-requis

**Volet A (Configuration)**
- Le coffre existe (onboarding effectué, cf. `SPECS-onboarding-coffre.md`).
- Aucun facteur n'est obligatoire pour *entrer* dans A : on peut arriver avec **0 protection** et en ressortir avec 1 à 4.
- La création de la **phrase de récupération** est un pré-requis logique du reste (c'est le secret maître) mais **ne bloque pas l'entrée** dans le tableau : si elle manque, A0 la présente comme **première action recommandée**.

**Volet B (Récupération)**
- Au moins **un facteur** a été configuré auparavant (sinon, cul-de-sac assumé et honnête → voir §12).
- Application installée sur l'appareil (nouvel appareil) **ou** application déjà présente mais code oublié.

### 3.2 Points d'entrée

**Volet A**
- Depuis **la fin de l'onboarding** (« Renforcez votre protection »).
- Depuis **Réglages → Sécurité → Ma protection**.
- Depuis un **rappel** (bannière non intrusive) si le niveau de protection est faible (jamais d'urgence artificielle, charte §10 / §2.10).

**Volet B**
- Écran d'accueil / connexion → lien **« Je n'arrive plus à accéder à mon coffre »**.
- Sur un **nouvel appareil**, dès le premier lancement → **« J'ai déjà un coffre »**.
- Depuis l'écran de saisie du code → après échec, **« Code oublié ? »** (jamais après un compte à rebours forcé ; délais désactivables, WCAG 2.2.1).

### 3.3 Points de sortie

- **Volet A → succès :** retour au tableau « Ma protection » (A0/A5) avec le **niveau mis à jour** et une prochaine action proposée. Sortie possible à tout moment sans perte (reprise ultérieure).
- **Volet B → succès :** écran **« Compte récupéré »** (B5) → coffre déverrouillé + **invitation à reconfigurer** les protections liées à l'ancien appareil (rotation).
- **Volet B → échec définitif contrôlé :** page d'aide « Options restantes » (jamais une impasse muette) + rappel anti-hameçonnage.

### 3.4 Reprise après interruption (charte §2.5 / §8)

- Toute étape est **interrompable et reprenable** ; l'état est conservé **localement** (aucune saisie perdue, y compris coupure réseau en plein parcours).
- À la reprise, un **bandeau discret** indique « Reprise là où vous vous étiez arrêté ».
- Les **secrets saisis ne sont jamais persistés en clair** : à la reprise d'une étape secrète, le champ est **vide et re-demandé** (compromis assumé sécurité > commodité, §9).

---

## 4. Parcours utilisateur (flux)

Notation : `→` étape suivante · `⤺` retour arrière possible · `⎇` branchement.

### 4.1 Volet A — Configuration

1. **Entrée A0 « Ma protection ».** Affiche le **niveau de protection** (nombre de protections actives) et la liste des 4 protections avec leur état (active / à activer). ⤺ quitter à tout moment.
2. **⎇ Choix d'une protection à ajouter.**
   - `A1` **Phrase de récupération** → parcours détaillé dans `SPECS-phrase-recuperation.md` (créer / vérifier / sauvegarder). Retour → A0 avec niveau +1. ⤺
   - `A2` **Carte SIM numérique de secours** → parcours détaillé dans `SPECS-esim-secours.md` (installer / associer le canal de récupération). Retour → A0. ⤺
   - `A3` **Sauvegarde chiffrée** → activer une copie **chiffrée (lisible par vous seul)** du coffre, stockée localement et/ou synchronisée. Retour → A0. ⤺
   - `A4` **Contact de confiance** → inviter une personne de confiance qui pourra **aider** (jamais accéder seule) à une future récupération. Retour → A0. ⤺
3. **Chaque ajout** met à jour le niveau et propose **la prochaine protection recommandée** (progressive disclosure, une décision à la fois).
4. **A5 Récapitulatif** (atteint dès ≥ 2 protections, ou sur demande) : montre le **niveau atteint**, ce qui change concrètement (« aucune perte unique ne vous bloque »), et les protections encore possibles. → Fin ou retour A0.

> Règle produit UX : **aucune protection n'est obligatoire**, mais A0 **recommande** un ordre (`Q3` à confirmer) : d'abord la **phrase** (socle), puis un **canal indépendant** (carte SIM numérique de secours), puis **sauvegarde chiffrée**, puis **contacts de confiance**. Aucune étape n'est « passée à l'aveugle » (charte §14) : reporter est permis avec rappel clair du risque.

### 4.2 Volet B — Récupération

1. **Entrée B0 « Démarrer une récupération ».** Explique le but + **rappel anti-hameçonnage** immédiat. ⎇ « Nouvel appareil » / « Code oublié ». → B1.
2. **B1 « Vous identifier ».** Saisie d'un **identifiant public non secret** de façon **accessible** (pas de test cognitif seul, WCAG 3.3.8). **L'app ne confirme ni n'infirme l'existence d'un compte, ni les facteurs configurés** : elle affiche un **message neutre identique pour tout identifiant** (anti-énumération, §9). → B2. ⤺
3. **B2 « Choisir une protection ».** Présente un **ensemble générique** des méthodes de récupération possibles, **identique quel que soit l'identifiant** ; la disponibilité affichée porte sur la **méthode** (ex. « nécessite une connexion », « nécessite la réponse d'un contact de confiance »), **jamais sur les facteurs réellement configurés pour ce compte**. → B3. ⤺
4. **B3 Parcours guidé (selon le facteur choisi) :**
   - via **phrase de récupération** → saisie sécurisée **spécifiée directement dans cette SPEC** (§5/B3), chorégraphie détaillée à consolider dans `SPECS-phrase-recuperation.md`.
   - via **carte SIM numérique de secours** → code reçu sur le canal indépendant, guidé par `SPECS-esim-secours.md`.
   - via **sauvegarde chiffrée** → récupérer la copie chiffrée puis la **déverrouiller** avec un facteur possédé.
   - via **contact(s) de confiance** → demande d'aide envoyée, **approbation** par le(s) contact(s), reconstitution.
5. **⎇ Échec d'un facteur → B4 « Cette protection ne marche pas ».** On explique **pourquoi** (indisponible / expiré / refusé) et on **propose une autre protection** — **jamais de cul-de-sac**. → retour B2 avec le facteur en échec marqué. ⤺
6. **Succès → B5 « Compte récupéré ».** Confirmation claire, coffre déverrouillé, **prochaine étape** proposée (reconfigurer les protections de l'ancien appareil : **rotation** de la phrase si compromise, réassocier une nouvelle carte SIM numérique de secours). → Fin.
7. **Échec de toutes les options disponibles :** page **« Options restantes »** listant ce qui peut encore être tenté plus tard (ex. attendre qu'un contact de confiance soit joignable, réessayer avec le réseau ou la carte SIM numérique de secours) + anti-hameçonnage + accès à l'aide. **Jamais un mur.**

> **Anti-énumération (structurant).** Atteindre B1/B2 ne requiert qu'un identifiant **public**. La détermination réelle des facteurs configurés n'a lieu **qu'au moment où l'utilisateur tente effectivement un facteur** en B3 (première preuve de possession). Avant cela, aucun écran ne révèle : (a) qu'un compte existe, (b) quels facteurs sont configurés, (c) le **nom d'un contact de confiance** (§9).

---

## 5. Écrans & états

Pour chaque écran : **but**, **éléments**, **action primaire (une seule)**, **actions secondaires**, puis les **états applicables** parmi les 7 (charte §13).

### A0 — Tableau « Ma protection »

- **But.** Montrer le **niveau de protection** en un coup d'œil et permettre d'ajouter une protection.
- **Éléments.** Titre ; **jauge/compteur** « X protection(s) active(s) sur 4 » (nombres formatés selon la locale) + libellé qualitatif non anxiogène (ex. « Bien protégé ») ; liste des 4 protections avec état (active ✓ / à activer +) et **micro-explication d'une phrase** ; rappel « TEL ONLINE ne pourra jamais récupérer vos secrets à votre place → d'où l'importance de ces protections » ; lien aide/glossaire (emplacement constant, 3.2.6).
- **Action primaire.** **« Ajouter une protection »** (mène à la protection recommandée) — un seul bouton primaire.
- **Actions secondaires.** Ouvrir une protection précise ; « Comprendre la récupération » (aide) ; quitter.
- **États :**
  - **Chargement** — squelette de la jauge + 4 lignes.
  - **Vide** — 0 protection : message « Commencez par créer votre phrase de récupération » + action claire (pas de cul-de-sac).
  - **Contenu** — 1 à 4 protections, jauge à jour.
  - **Erreur** — impossible de lire l'état d'une protection : ligne marquée « État indisponible — réessayer », le reste reste utilisable.
  - **Hors ligne** — les protections **préparables hors ligne** restent cliquables ; celles qui exigent le réseau (carte SIM numérique de secours, invitation d'un contact) sont marquées « nécessite une connexion » + **proposition de la carte SIM numérique de secours** (§10).
  - **Succès** — retour après ajout : ligne passe à ✓, jauge s'incrémente, annonce vocale « Protection ajoutée. X sur 4. ».
  - **Permission requise** — n/a ici (déléguée aux sous-parcours au moment utile).

### A3 — Activer la sauvegarde chiffrée

- **But.** Créer une **copie chiffrée (lisible par vous seul)** du coffre, pour restaurer sur un nouvel appareil.
- **Éléments.** Explication « pourquoi » (1 phrase) ; choix **où** (sur cet appareil / synchronisée) ; mention **chiffré, personne d'autre ne peut la lire** ; rappel qu'il faut **la phrase ou un facteur possédé** pour l'ouvrir (une sauvegarde seule ne suffit pas → pas de faux sentiment de sécurité).
- **Action primaire.** **« Activer la sauvegarde chiffrée ».**
- **Actions secondaires.** « Plus tard » (report avec rappel de risque) ; aide.
- **États :** Chargement (préparation locale) · Vide (n/a) · Contenu · Erreur (échec d'écriture/sync → message + reprise) · **Hors ligne** (préparation locale possible ; **synchro mise en file**, marquée « en attente d'envoi ») · Succès (« Sauvegarde chiffrée active ») · **Permission requise** (accès stockage/espace, demandé **au moment utile**).

### A4 — Ajouter un contact de confiance

- **But.** Inviter une personne qui pourra **aider** (jamais accéder seule) à une future récupération.
- **Éléments.** Explication du rôle en langage clair (« Cette personne ne voit pas vos secrets. Elle confirme seulement que c'est bien vous. ») ; choix du contact ; message d'invitation prêt à l'emploi ; indication qu'il faut **le réseau** pour envoyer.
- **Action primaire.** **« Envoyer l'invitation ».**
- **Actions secondaires.** « Plus tard » ; « Comment ça marche » (aide) ; retirer un contact déjà ajouté.
- **États :** Chargement · Vide (aucun contact) · Contenu (liste des contacts + statut invité/confirmé) · Erreur (envoi échoué → reprise) · **Hors ligne** (invitation **mise en file** + **proposition de la carte SIM numérique de secours**) · Succès (« Invitation envoyée ») · **Permission requise** (accès aux contacts — demandé **en contexte**, avec explication, jamais au démarrage ; alternative : saisie manuelle sans permission).

### A5 — Récapitulatif du niveau de protection

- **But.** Rendre tangible ce que le niveau atteint **change** (« plus aucune perte unique ne vous bloque »).
- **Éléments.** Niveau atteint + libellé ; liste des protections actives ✓ ; ce qui manque encore et pourquoi c'est utile (sans culpabiliser) ; anti-hameçonnage.
- **Action primaire.** **« Terminer »** (retour A0).
- **Actions secondaires.** « Ajouter une autre protection » ; partager l'aide (jamais partager un secret).
- **États (7, explicités) :**
  - **Chargement** — squelette pendant le **calcul du niveau** (jauge + lignes).
  - **Vide** — **n/a justifié** : A5 n'est atteint qu'à partir de ≥ 2 protections ; en deçà, l'utilisateur reste sur A0 (aucun récapitulatif vide n'est présenté).
  - **Contenu** — niveau atteint + liste des protections actives + ce qui manque.
  - **Erreur** — impossible de calculer/lire l'état d'une protection : message + « réessayer » ; le reste du récapitulatif reste lisible.
  - **Hors ligne** — affiche le **niveau connu localement** ; le statut réseau des contacts (invité/confirmé) est marqué « en attente de mise à jour ».
  - **Succès** — état nominal après un ajout (« Protection ajoutée, voici votre niveau »).
  - **Permission requise** — n/a.

### B0 — Démarrer une récupération

- **But.** Rassurer, cadrer, **prévenir l'hameçonnage** avant toute saisie.
- **Éléments.** Titre « Récupérer votre coffre » ; **bannière anti-hameçonnage persistante** (« TEL ONLINE ne vous demandera jamais votre phrase par message, e-mail ou appel ») ; choix « Nouvel appareil » / « J'ai oublié mon code » ; lien aide (emplacement constant, 3.2.6).
- **Action primaire.** **« Commencer ».**
- **Actions secondaires.** « Qu'est-ce qu'une récupération ? » (aide) ; retour à l'accueil.
- **États :** Chargement · Vide (n/a) · Contenu · Erreur (n/a saisie) · **Hors ligne** (explique ce qui va exiger le réseau + **propose la carte SIM numérique de secours**) · Succès (n/a ici) · Permission requise (n/a).

### B1 — Vous identifier

- **But.** Recueillir **quelle identité** on cherche à récupérer, de façon **accessible** (WCAG 3.3.8) et **sans révéler d'information à un tiers** (anti-énumération, §9).
- **Éléments.** Champ d'identifiant public **non secret** (ex. adresse e-mail / numéro liés au compte — `Q7` à confirmer) ; explication « ceci n'est pas un secret » ; **aucun test cognitif comme seul facteur** ; **collage / gestionnaire autorisés** sur ce champ non secret ; **bannière anti-hameçonnage** (cohérence sur tout le Volet B) ; **message neutre** `rzp.recup.identite.neutre` indiquant que, *si* un coffre correspond, les options apparaîtront à l'écran suivant.
- **Action primaire.** **« Continuer ».**
- **Actions secondaires.** « Je ne me souviens plus de mon identifiant » (aide) ; retour.
- **États (non énumérants) :**
  - **Chargement** — bref ; **validation locale du format** uniquement (aucune recherche réseau qui révélerait l'existence d'un compte).
  - **Vide** — **n/a** : aucune « recherche sans résultat » n'est affichée (anti-énumération).
  - **Contenu** — champ + message neutre + bannière.
  - **Erreur** — **uniquement erreur de format** (ex. e-mail mal écrit) → message clair ; **jamais** « compte inconnu » ou « identifiant introuvable ».
  - **Hors ligne** — B1 reste utilisable (aucun lookup réseau requis pour avancer) ; la vérification réelle d'un facteur a lieu en B3.
  - **Succès** — → B2, avec un message **identique quel que soit l'identifiant** saisi.
  - **Permission requise** — n/a.

### B2 — Choisir une protection

- **But.** Laisser l'utilisateur **choisir une méthode de récupération** parmi un **ensemble générique**, sans jamais divulguer sa cartographie de sécurité.
- **Éléments.** Liste des **quatre méthodes possibles**, **présentée à l'identique pour tout identifiant** — l'app **ne confirme pas** lesquelles sont configurées (anti-énumération, §9). Pour chaque méthode : ce qu'elle requiert **au niveau de la méthode** (« Disponible hors ligne » / « Nécessite une connexion » / « Nécessite la réponse d'un contact de confiance »), le **temps estimé** et ce qu'il faut avoir sous la main. **Aucun nom de contact affiché.** Bannière anti-hameçonnage présente.
- **Action primaire.** **« Utiliser cette protection »** (sur l'option sélectionnée).
- **Actions secondaires.** « Aucune de celles-ci ? » → page options restantes ; retour B1.
- **États :**
  - **Chargement** — squelette bref (préparation de la liste statique locale).
  - **Vide** — **n/a justifié** : la liste des méthodes possibles est toujours présentée (choisir une méthode non configurée mène simplement à B4, sans fuite).
  - **Contenu** — les 4 méthodes.
  - **Erreur** — n/a saisie (aucun lookup) ; en cas d'échec de préparation locale, message + « réessayer ».
  - **Hors ligne** — grise les méthodes qui exigent le réseau **au niveau de la méthode**, met en avant la **phrase de récupération** (utilisable hors ligne) + **propose la carte SIM numérique de secours** ; ne révèle rien sur le compte.
  - **Succès** — → B3.
  - **Permission requise** — n/a.

### B3 — Parcours guidé (par facteur)

> **Statut de couverture.** Les **comportements de sécurité critiques de la saisie du secret** (phrase) sont **normatifs et spécifiés ci-dessous dans cette SPEC**, afin de ne pas dépendre d'un fichier absent. La **chorégraphie détaillée pas-à-pas** de chaque sous-parcours (phrase, carte SIM numérique de secours) sera **consolidée** dans `SPECS-phrase-recuperation.md` / `SPECS-esim-secours.md` — **non encore rédigées** : dépendance signalée, **non bloquante** pour les garanties listées ici.

- **But.** Exécuter la récupération avec le facteur choisi, **étape par étape**, un objectif par écran. Le **premier facteur validé constitue la première preuve de possession** (au-delà, l'app peut afficher l'état réel du compte).

- **Facteur — Phrase de récupération (saisie du secret). Comportement normatif :** action primaire « Récupérer mon coffre ».
  - **Masquage par défaut** (points), bascule **« Afficher »** accessible (nom accessible explicite), affichage temporaire.
  - **Capture d'écran bloquée** sur cet écran, avec message accessible expliquant pourquoi (`rzp.secu.capture.bloquee`).
  - **Clavier :** autocapitalisation **désactivée**, **autocorrection désactivée**, **suggestions/prédiction du clavier système désactivées**, **pas d'apprentissage/mise en cache** du champ (champ traité comme secret).
  - **Presse-papiers :** **pas de collage par défaut** ; le secret n'est jamais laissé dans le presse-papiers.
  - **Alternative accessible (WCAG 3.3.8), normative — lève l'ambiguïté avec §9 :** l'utilisateur choisit librement entre **(a)** saisie manuelle, **(b)** **saisie assistée mot à mot** avec auto-complétion **restreinte à la liste de mots officielle localisée** (liste **fermée**, **hors ligne**, **aucune** suggestion cloud ni autocorrection), qui **réduit l'effort de transcription et de mémoire** sans exposer le secret, ou **(c)** **collage depuis un gestionnaire de secrets dédié**, encadré par un **effacement automatique du presse-papiers** après un court délai + avertissement. **Aucune** de ces options n'impose de test cognitif (pas de captcha, pas d'énigme). Précision : l'interdiction §9 (« pas de suggestion clavier / autocorrection », « presse-papiers évité ») vise les **suggestions/prédictions du clavier système** et le **presse-papiers ordinaire** ; elle **n'interdit pas** l'auto-complétion **locale sur liste fermée** ni le **collage encadré depuis un gestionnaire**, qui **constituent** l'alternative accessible.
  - **Aucun envoi au serveur :** validation **locale** (§9).
  - **Tentatives :** pas de verrou opaque ni de compte à rebours non désactivable (2.2.1) ; après échec, réorienter vers une autre protection (B4).

- **Facteur — Carte SIM numérique de secours** → réception d'un code sur le canal indépendant (guidé par `SPECS-esim-secours.md`). Action primaire : « Valider le code ».
- **Facteur — Sauvegarde chiffrée** → récupérer la copie chiffrée puis la **déverrouiller** avec un facteur possédé. Action primaire : « Ouvrir ma sauvegarde ».
- **Facteur — Contacts de confiance** → « Demander l'aide de mes contacts », puis écran d'attente d'approbation (voir États). **Aucun nom de contact n'est affiché avant validation** (§9).

- **Actions secondaires (communes).** « Cette protection ne marche pas » (→ B4) ; « Comprendre cette étape » (aide, emplacement constant) ; retour B2.
- **États :** Chargement (préparation de la saisie / attente d'un code / calcul) · Vide (n/a — un facteur est toujours en cours) · Contenu · **Erreur** (facteur refusé/incorrect → message + **proposer une autre protection**, pas d'énième tentative punitive) · **Hors ligne** (phrase : possible hors ligne si la sauvegarde est locale ; sinon expliquer que la récupération de la sauvegarde/contact exige le réseau → **carte SIM numérique de secours**) · Succès (→ B5) · **Permission requise** (ex. lecture d'un code entrant sur le canal de secours — au moment utile).

> **Écran d'attente (contacts de confiance).** État « en cours » explicite **sans nom** : « Demande envoyée à un contact de confiance. En attente de sa confirmation. » ; annonce polie à chaque changement ; possibilité de **continuer plus tard** (reprenable) ; jamais de compte à rebours bloquant. Le nom d'un contact **peut** apparaître **après** validation (utilisateur désormais authentifié), jamais avant.

### B4 — « Cette protection ne marche pas » (alternative)

- **But.** Transformer un échec en **choix**, jamais en impasse.
- **Éléments.** Cause **en langage clair** (indisponible / code expiré / **un contact de confiance injoignable** / phrase incorrecte — **sans nom**) ; **liste des autres protections proposées** ; conseil concret ; anti-hameçonnage (« ne communiquez votre phrase à personne, même à une personne qui prétend vous aider »). Bannière anti-hameçonnage présente.
- **Action primaire.** **« Essayer une autre protection »** (→ B2).
- **Actions secondaires.** « Réessayer cette protection » (si pertinent) ; « Voir toutes mes options » ; aide.
- **États :**
  - **Chargement** — squelette pendant le **calcul des alternatives disponibles**.
  - **Contenu** — nominal.
  - **Vide** — plus aucune alternative maintenant → « Options restantes » : ce qui redeviendra possible avec le réseau, la carte SIM numérique de secours ou une réponse de contact.
  - **Erreur** — impossible de calculer les alternatives → message + réessayer.
  - **Hors ligne** — met en avant la **carte SIM numérique de secours**.
  - **Succès** — n/a.
  - **Permission requise** — n/a.

### B5 — « Compte récupéré » (succès)

- **But.** Confirmer sans ambiguïté et guider la **remise en sécurité**.
- **Éléments.** Confirmation claire + coche ; résumé « Votre coffre est de nouveau accessible sur cet appareil » ; **prochaines étapes** : reconfigurer les protections de l'ancien appareil (rotation de la phrase si vous pensez qu'elle a été vue, réassocier une nouvelle carte SIM numérique de secours) ; rappel auto-conservation ; **bannière anti-hameçonnage** (cohérence sur tout le Volet B).
- **Action primaire.** **« Ouvrir mon coffre ».**
- **Actions secondaires.** « Renforcer ma protection » (→ Volet A / A0) ; aide.
- **États :** Succès (état principal) · Chargement (déverrouillage final) · Erreur (échec au tout dernier pas → reprise, pas de retour à zéro) · Hors ligne (confirme ce qui est fait localement, marque le reste « en attente d'envoi ») · autres n/a.

---

## 6. Wireframes textuels

Zone du pouce respectée : **action primaire ancrée dans le tiers inférieur** (charte §4). Descriptions, aucun code.

> **RTL (charte §6).** En arabe, la mise en page est **mise en miroir** via propriétés logiques : la jauge se remplit depuis le bord de **début** (à droite), les **flèches « → » et les pastilles de progression sont inversées**, et **tous les chiffres suivent la locale** (chiffres arabes orientaux en AR). Les schémas ci-dessous sont donnés en LTR.

### A0 — Ma protection
```
┌───────────────────────────────┐
│  Ma protection                 │  ← titre H1, unique
│                                │
│   ●●●○   « Bien protégé »      │  ← jauge 4 pastilles (3 pleines)
│   3 protections actives sur 4  │  ← libellé chiffré (locale) + qualitatif
│                                │
│  ✓ Phrase de récupération      │
│     Votre secret principal     │
│  ✓ Carte SIM numérique secours │
│     Un canal indépendant       │
│  ✓ Sauvegarde chiffrée         │
│     Copie lisible par vous     │
│  +  Contact de confiance       │  ← à activer (icône + libellé, pas
│     Une personne qui vous aide │     que la couleur)
│                                │
│  ℹ Personne, pas même nous, ne │  ← rappel auto-conservation
│    peut récupérer à votre place│
│  ‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥ │
│  [   Ajouter une protection  ] │  ← primaire, bas, ≥44px, pleine largeur
│  Comprendre la récupération    │  ← lien secondaire (emplacement constant)
└───────────────────────────────┘
```

### B0 — Démarrer une récupération
```
┌───────────────────────────────┐
│  ← Retour                      │
│  Récupérer votre coffre        │  ← H1
│                                │
│  ⚠ Rappel de sécurité          │  ← bannière anti-hameçonnage
│  TEL ONLINE ne vous demandera  │     persistante (icône+texte)
│  jamais votre phrase par       │
│  message, e-mail ou appel.     │
│                                │
│  Que s'est-il passé ?          │
│  ( ) J'ai un nouveau téléphone │  ← radio, cibles ≥44px, espacées ≥8px
│  ( ) J'ai oublié mon code      │
│                                │
│  ‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥ │
│  [        Commencer         ]  │  ← primaire, bas
│  Qu'est-ce qu'une récupération?│
└───────────────────────────────┘
```

### B2 — Choisir une protection
```
┌───────────────────────────────┐
│  ← Retour                      │
│  Comment récupérer votre       │  ← H1 (aligné sur la fonction)
│  coffre ?                      │
│                                │
│  ⚠ TEL ONLINE ne vous demande  │  ← bannière anti-hameçonnage (compacte)
│    jamais votre phrase.        │
│                                │
│  ○ Ma phrase de récupération   │
│    Disponible hors ligne · ~2 min │ ← disponibilité de la MÉTHODE
│  ○ Ma carte SIM num. de secours│
│    Nécessite une connexion     │  ← état honnête (grisé si hors ligne)
│  ○ Un contact de confiance     │
│    Nécessite la réponse d'un   │  ← libellé GÉNÉRIQUE, aucun nom
│    contact de confiance        │
│                                │
│  Aucune de celles-ci ?         │  ← jamais de cul-de-sac
│  ‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥ │
│  [   Utiliser cette protection]│  ← primaire, bas
└───────────────────────────────┘
```

### B4 — Cette protection ne marche pas
```
┌───────────────────────────────┐
│  Cette protection n'a pas      │  ← H1, ton calme
│  fonctionné                    │
│                                │
│  Le code de votre carte SIM    │  ← cause en langage clair
│  numérique a expiré.           │
│                                │
│  Bonne nouvelle : vous pouvez  │  ← rassure, oriente
│  essayer autrement :           │
│  → Ma phrase de récupération   │  (→ mis en miroir en RTL)
│  → Un contact de confiance     │
│                                │
│  ⚠ Ne communiquez votre phrase │  ← anti-hameçonnage
│    à personne.                 │
│  ‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥ │
│  [ Essayer une autre protection]│ ← primaire, bas
│  Réessayer cette protection    │
└───────────────────────────────┘
```

### B5 — Compte récupéré
```
┌───────────────────────────────┐
│            ✓                   │  ← coche succès (icône + texte,
│  Votre coffre est de nouveau   │     pas que la couleur)
│  accessible.                   │
│                                │
│  ⚠ TEL ONLINE ne vous demande  │  ← bannière anti-hameçonnage
│    jamais votre phrase.        │     (cohérence Volet B)
│                                │
│  Pour rester tranquille :      │
│  • Remplacez votre phrase si   │
│    vous pensez qu'elle a été   │
│    vue.                        │
│  • Réassociez une carte SIM    │
│    numérique de secours.       │
│                                │
│  ‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥‥ │
│  [     Ouvrir mon coffre     ] │  ← primaire, bas
│  Renforcer ma protection       │
└───────────────────────────────┘
```

---

## 7. Microcopie (FR)

Aucune concaténation ; pluriels ICU ; chaque texte a une clé. (Extraits représentatifs ; les sous-parcours phrase/carte SIM numérique de secours portent leurs propres clés dans leurs SPECS.)

| Élément | Clé i18n | Texte FR | Note i18n/traduction |
|---|---|---|---|
| Titre A0 | `rzp.config.titre` | Ma protection | +40 % : « My protection » plus court ; prévoir marge FR/DE. |
| Compteur de protections | `rzp.config.compteur` | `{count, plural, one {# protection active sur {total}} other {# protections actives sur {total}}}` | ICU ; `{count}`/`{total}` **formatés selon la locale** (chiffres locaux) et **isolés bidi** ; « sur 4 » **paramétré** (`{total}`) si `Q3` change le nombre de facteurs. |
| Libellé niveau (qualitatif) | `rzp.config.niveau.{level}` | `depart` → « Premiers pas » · `bon` → « Bien protégé » · `fort` → « Très protégé » · `max` → « Protection maximale » | Clés par palier, jamais construites par concaténation ; ton non anxiogène. |
| Rappel auto-conservation | `rzp.config.autoconservation` | Personne, pas même TEL ONLINE, ne peut récupérer vos secrets à votre place. Ces protections servent à ne rien perdre. | Bénéfice, pas la technique (charte §7). |
| Bouton ajouter | `rzp.config.cta.ajouter` | Ajouter une protection | Verbe d'action ; ne jamais tronquer. |
| Ligne « phrase » | `rzp.factor.phrase.nom` / `rzp.factor.phrase.desc` | Phrase de récupération / Votre secret principal | Renvoi `SPECS-phrase-recuperation.md`. |
| Ligne « carte SIM » | `rzp.factor.simsecours.nom` / `rzp.factor.simsecours.desc` | Carte SIM numérique de secours / Un canal indépendant | Renvoi `SPECS-esim-secours.md`. **Jamais « eSIM » à l'écran** (glossaire §16). |
| Ligne « sauvegarde » | `rzp.factor.sauvegarde.nom` / `rzp.factor.sauvegarde.desc` | Sauvegarde chiffrée / Une copie lisible par vous seul | « chiffré (lisible par vous seul) » (glossaire §16). |
| Ligne « contact » | `rzp.factor.contact.nom` / `rzp.factor.contact.desc` | Contact de confiance / Une personne qui vous aide sans voir vos secrets | Jamais « guardian » / « tuteur ». |
| État actif | `rzp.factor.etat.actif` | Activée | Doublé d'une icône (jamais la couleur seule). |
| État à activer | `rzp.factor.etat.aactiver` | À activer | Idem. |
| Annonce ajout (live) | `rzp.config.annonce.ajout` | `Protection ajoutée. {count, plural, one {# protection active sur {total}} other {# protections actives sur {total}}}.` | Région live polie ; nombres localisés + isolés bidi. |
| Titre B0 | `rzp.recup.titre` | Récupérer votre coffre | — |
| Anti-hameçonnage (bannière) | `rzp.secu.antiphishing` | TEL ONLINE ne vous demandera jamais votre phrase de récupération par message, e-mail ou appel. | **Présente sur tout le Volet B (B0 à B5)** ; renforcée sur B4. Ne jamais raccourcir au point de perdre le sens. |
| Choix nouvel appareil | `rzp.recup.cas.nouvelappareil` | J'ai un nouveau téléphone | — |
| Choix code oublié | `rzp.recup.cas.codeoublie` | J'ai oublié mon code | — |
| CTA commencer | `rzp.recup.cta.commencer` | Commencer | — |
| Titre B1 | `rzp.recup.identite.titre` | Vous identifier | — |
| Aide champ identifiant | `rzp.recup.identite.aide` | Ce n'est pas un secret. C'est seulement pour retrouver votre coffre. | Rassure ; distingue du champ secret. |
| Message neutre B1 (anti-énumération) | `rzp.recup.identite.neutre` | Si un coffre correspond à cet identifiant, vous verrez vos options à l'écran suivant. | **Réponse identique** que le compte existe ou non (§9). |
| Erreur de format B1 | `rzp.recup.identite.formaterreur` | Vérifiez le format de votre identifiant (par exemple : nom@exemple.fr). | Erreur de **format** uniquement ; **jamais** « compte inconnu ». |
| Titre B2 | `rzp.recup.choix.titre` | Comment récupérer votre coffre ? | **Aligné sur la fonction** (WCAG 2.4.2) ; ne réintroduit plus « identifier » (porté par B1). |
| Dispo — utilisable hors ligne | `rzp.recup.dispo.pret` | Disponible hors ligne | Porte sur la **méthode** ; doublé d'une icône. |
| Dispo — réseau requis | `rzp.recup.dispo.reseau` | Nécessite une connexion | + proposition carte SIM numérique de secours (§10). |
| Dispo — contact requis | `rzp.recup.dispo.contact` | En attente d'une réponse d'un contact de confiance | **Libellé générique, sans nom** (anti-énumération §9) ; le nom n'apparaît qu'après preuve de possession. |
| Durée estimée | `rzp.recup.duree` | `Environ {minutes, plural, one {# minute} other {# minutes}}` | ICU ; `{minutes}` localisé + isolé bidi. |
| Aucune option | `rzp.recup.choix.aucune` | Aucune de celles-ci ? | Mène aux options restantes. |
| CTA utiliser | `rzp.recup.cta.utiliser` | Utiliser cette protection | — |
| Titre B4 | `rzp.recup.echec.titre` | Cette protection n'a pas fonctionné | Ton calme, pas d'alarme. |
| Cause (paramétrée) | `rzp.recup.echec.cause.{raison}` | `expire` → « Le code a expiré. » · `incorrect` → « La phrase saisie ne correspond pas. » · `injoignable` → « Un contact de confiance n'a pas encore répondu. » · `indispo` → « Cette protection n'est pas disponible pour l'instant. » | Une clé par raison ; `injoignable` est **générique, sans nom** (§9). |
| Réassurance échec | `rzp.recup.echec.rassure` | Bonne nouvelle : vous pouvez essayer autrement. | Jamais de cul-de-sac. |
| CTA autre protection | `rzp.recup.echec.cta.autre` | Essayer une autre protection | — |
| Titre B5 succès | `rzp.recup.succes.titre` | Votre coffre est de nouveau accessible | — |
| Conseil rotation | `rzp.recup.succes.conseil.rotation` | Remplacez votre phrase de récupération si vous pensez que quelqu'un a pu la voir. | Prévention. |
| CTA ouvrir | `rzp.recup.succes.cta.ouvrir` | Ouvrir mon coffre | — |
| Bandeau reprise | `rzp.commun.reprise` | Vous reprenez là où vous vous étiez arrêté. | Reprise sans perte (§3.4). |
| Message hors ligne (réseau requis) | `rzp.offline.reseaurequis` | Cette étape a besoin d'une connexion. Pas de réseau ? Votre carte SIM numérique de secours peut vous connecter. | Honnête + solution (charte §8). |
| Message capture bloquée | `rzp.secu.capture.bloquee` | Pour vous protéger, la capture d'écran est désactivée sur les écrans secrets. | Accessible, explique pourquoi (§9). |
| Aide saisie assistée (secret) | `rzp.secu.secret.aide` | Vous pouvez saisir votre phrase mot à mot : chaque mot est complété à partir de la liste officielle, sans quitter l'application. | **Alternative accessible 3.3.8** ; liste **fermée**, hors ligne, aucune suggestion cloud. |

**Règles i18n transversales (toutes les clés).**
1. **Isolation bidirectionnelle** de toute variable interpolée (`{count}`, `{total}`, `{minutes}`, et tout `{name}` affiché **après** preuve de possession) via les marques Unicode d'isolation (**FSI/PDI**) ou l'équivalent plateforme, pour éviter tout mélange/inversion quand la directionnalité de la valeur diffère de celle de la phrase (ex. nom latin dans une phrase arabe, ponctuation ou chiffres qui migrent).
2. **Formatage numérique localisé :** tous les nombres (`count`, `total`, `minutes`) passent par le **formateur de la locale** (chiffres arabes orientaux en AR, etc.), jamais formatés à la main.
3. **Miroir directionnel en RTL :** **sens de remplissage de la jauge**, **flèches « → »** et **glyphes de progression** mis en miroir via propriétés logiques.

---

## 8. Accessibilité — WCAG 2.2 AA (spécifique à ces écrans)

| Critère (n°) | Comment il est satisfait ici |
|---|---|
| **1.4.3 / 1.4.11 Contraste** | Textes via `color.ink` (16.9:1) / `color.ink-muted` (5.9:1) ; jauge, coche succès, bannière anti-hameçonnage et bordures ≥ 3:1 (`color.border-strong` 4.6:1). Palette charte §11. |
| **1.4.1 Sans couleur seule** | État « Activée » / « À activer », « Disponible » / « Nécessite une connexion », succès/échec : toujours **icône + libellé texte** en plus de la couleur. Jauge : pastilles pleines/vides **et** compteur chiffré. |
| **1.4.4 / 1.4.10 Zoom & reflow** | Utilisable à 200 % et jusqu'à 320px sans défilement 2D ; libellés de niveau et messages d'échec ne se figent pas sur la longueur FR (+40 % prévu). |
| **2.4.2 Titre de page** | Chaque écran a un titre unique, parlant **et aligné sur sa fonction** (« Ma protection », « Récupérer votre coffre », « Comment récupérer votre coffre ? » pour B2, « Cette protection n'a pas fonctionné »). Le nom d'écran (métadonnées/§5) et le H1 affiché sont **cohérents**. |
| **2.4.3 Ordre de focus** | Ordre logique haut→bas ; action primaire ancrée en bas mais **dernière dans l'ordre de lecture** ; en RTL, l'ordre suit la lecture droite-à-gauche (**propriétés logiques**) ; jauge, flèches et glyphes de progression **mis en miroir** ; nombres selon la locale. |
| **2.4.7 / 2.4.11 Focus visible** | Contour ≥ 3px, offset 2px, contraste ≥ 3:1 sur fond **et** composant ; aucun élément focusable masqué derrière la barre d'action ancrée. |
| **2.4.6 / 2.5.3 Libellés & nom accessible** | Chaque contrôle a un libellé explicite ; le **nom accessible contient le texte visible** (ex. bouton « Utiliser cette protection »). |
| **3.2.6 Aide cohérente** | Les mécanismes d'aide (lien « aide » / « Comprendre… » / glossaire) apparaissent **au même emplacement et dans le même ordre** sur tous les écrans du parcours ; la **bannière anti-hameçonnage** est présente de façon **cohérente sur tout le Volet B (B0 à B5)**. |
| **4.1.2 / région live** | **Annonces d'état** : ajout de protection, passage hors ligne, échec/alternative, succès de récupération → région live **polie** ; erreurs bloquantes **assertives** uniquement. |
| **2.1.1 / 2.1.2 Clavier / commande vocale** | Tout est actionnable au clavier/voix ; focus **jamais piégé** ; à l'ouverture d'une feuille inférieure (choix de protection), focus déplacé dedans puis restitué. |
| **2.5.8 / cibles 44px** | Toutes les cibles ≥ 44×44px (norme interne, plus stricte que le 24px WCAG), espacement ≥ 8px ; radios de B0/B2 et lignes de A0 dimensionnées en conséquence. |
| **2.5.1 Gestes** | Aucun geste requis : tout appui long / glisser a une **alternative bouton visible**. |
| **2.2.1 Timing** | Aucun compte à rebours bloquant ; les délais (ex. attente d'un contact, expiration d'un code) sont **explicables et l'action est reprenable** ; « Code oublié ? » n'est pas verrouillé derrière un minuteur non désactivable. |
| **2.3.1 / mouvement réduit** | Respect de `prefers-reduced-motion` ; la jauge n'anime pas de remplissage clignotant ; transitions désactivables. |
| **3.3.7 Aides persistantes** | L'identifiant public saisi en B1 n'est **pas redemandé** plus loin dans le même parcours. |
| **3.3.8 Authentification accessible** | **Aucun test cognitif comme seul facteur.** L'identité repose sur **possession** (phrase, code sur canal indépendant, approbation d'un contact), pas sur « recopiez / résolvez ». Sur le champ **non secret** (identifiant B1), coller / gestionnaire de mots de passe **autorisés**. Sur le champ **secret** (phrase, B3), l'**alternative accessible est définie et normative** (§5/B3) : **saisie assistée mot à mot sur liste fermée localisée** (hors ligne, sans suggestion cloud) et/ou **collage depuis un gestionnaire dédié avec effacement automatique du presse-papiers** — sans exposer le secret ni imposer de transcription cognitive. Ambiguïté avec §9 **levée** (voir §5/B3 et §9). |
| **1.3.4 Orientation** | Pas de blocage portrait/paysage. |

---

## 9. Sécurité & confiance UX

Règles dérivées de la charte §10, spécifiques à ce parcours :

- **Anti-énumération & anti-fuite avant authentification.** B1 et B2 **ne confirment ni n'infirment l'existence d'un compte** et ne révèlent **jamais** la posture de sécurité (quels facteurs sont configurés). B1 renvoie une **réponse identique** que le compte existe ou non (`rzp.recup.identite.neutre`) ; ses erreurs portent **uniquement sur le format**, jamais sur l'existence. B2 présente un **ensemble générique** de méthodes, **identique pour tout identifiant** ; la disponibilité affichée porte sur la **méthode**, jamais sur le compte. La détermination réelle des facteurs n'a lieu **qu'à la première tentative effective d'un facteur** en B3 (première preuve de possession).
- **Nom d'un contact de confiance jamais révélé avant possession.** En B2, à l'écran d'attente de B3 et en B4, on utilise un **libellé générique** (« un contact de confiance »), **jamais** `{name}`. Le nom d'un contact ne peut apparaître qu'**après** validation d'un facteur de possession (ou après récupération réussie, à l'utilisateur désormais authentifié). Objectif : ne pas fournir à un tiers qui connaîtrait l'e-mail de la victime une cible de faux support / faux contact.
- **Auto-conservation affichée sans détour.** A0/A5 et B5 rappellent que **personne, pas même TEL ONLINE, ne peut récupérer les secrets à la place de l'utilisateur** — d'où l'importance des protections. Aucune promesse trompeuse de « support qui débloque le compte ».
- **Aucune divulgation de secret côté serveur.** L'identification (B1) utilise un **identifiant public non secret**. **La phrase de récupération n'est jamais envoyée au serveur, ni saisie sur un écran serveur** : sa saisie a lieu **localement** (comportement normatif §5/B3). Le serveur ne voit **jamais** de secret en clair.
- **Écrans secrets protégés (comportement normatif, §5/B3).** Dès qu'un secret s'affiche/se saisit (B3 via phrase), s'appliquent : **capture d'écran bloquée** avec message accessible (`rzp.secu.capture.bloquee`), **masquage par défaut** + bouton « Afficher », **pas de suggestion/prédiction du clavier système ni d'autocorrection**, **presse-papiers ordinaire non utilisé par défaut**. **Exception accessible encadrée (3.3.8) :** collage **depuis un gestionnaire de secrets dédié** avec **effacement automatique du presse-papiers** + avertissement, et/ou **saisie assistée locale sur liste fermée** — c'est l'alternative qui **lève** le compromis sécurité/accessibilité, sans jamais exposer le secret à un service tiers.
- **Anti-hameçonnage persistant et cohérent.** Bannière (`rzp.secu.antiphishing`) présente sur **tout le Volet B (B0, B1, B2, B3, B4, B5)**, à un emplacement **constant** (3.2.6) : « TEL ONLINE ne vous demandera jamais votre phrase par message, e-mail ou appel. » Sur B4, message **renforcé** : ne jamais communiquer sa phrase à une personne qui « aide », y compris un faux support ou un faux contact de confiance.
- **Contacts de confiance = aide, jamais accès.** L'UX martèle qu'un contact **confirme l'identité** mais **ne voit aucun secret** et **ne peut pas accéder seul** au coffre. Une **approbation multiple** peut être requise (`Q3`), présentée simplement.
- **Confirmations sur actions sensibles.** Retirer un contact, désactiver une protection, ou **remplacer la phrase** (rotation en B5) = **modale de confirmation décrivant la conséquence** ; action destructive placée à la fin, jamais bouton par défaut destructeur ; aucune case pré-cochée.
- **Pas d'urgence artificielle.** Aucune minuterie stressante, aucun « dépêchez-vous ». Le ton reste calme même en récupération.
- **Journalisation sans contenu.** Aucun secret, aucun identifiant sensible, aucun nom de contact en clair dans les logs/l'analytique ; les événements (« facteur X tenté / réussi / échoué ») sont **anonymisés et sans contenu**.

---

## 10. Offline-first & réseau

Principe honnête (charte §8) : dire clairement **ce qui marche hors ligne** et **ce qui exige le réseau**, et proposer la **carte SIM numérique de secours** comme filet.

**Ce qui fonctionne hors ligne**
- **Volet A :** consulter le **tableau « Ma protection »** et le niveau ; **créer / afficher / vérifier la phrase de récupération** (le socle se prépare hors ligne, cf. SPEC phrase) ; **préparer la sauvegarde chiffrée localement** ; lire l'aide et le glossaire.
- **Volet B :** lire B0 et l'anti-hameçonnage ; **saisir l'identifiant (B1) et parcourir le menu générique (B2)** sans réseau ; **récupérer via la phrase de récupération** **si** la sauvegarde chiffrée est **déjà présente localement** (ex. code oublié sur le même appareil).

**Ce qui exige le réseau (marqué explicitement)**
- **Associer / activer la carte SIM numérique de secours** (`SPECS-esim-secours.md`).
- **Envoyer une invitation** à un contact de confiance et **recevoir son approbation** (A4 / B3-contacts).
- **Synchroniser** la sauvegarde chiffrée et, sur un **nouvel appareil vierge**, **télécharger** cette sauvegarde avant de la déverrouiller.
- **Valider effectivement un facteur** côté service au moment de la tentative en B3, selon `Q7` (jamais avant, pour préserver l'anti-énumération).

**Comportements**
- Les actions réseau hors ligne sont **mises en file** et marquées **« en attente d'envoi »** ; synchro automatique au retour du réseau + résultat annoncé.
- Message honnête unique (`rzp.offline.reseaurequis`) : « Cette étape a besoin d'une connexion. Pas de réseau ? Votre carte SIM numérique de secours peut vous connecter. » — jamais d'erreur technique brute.
- **Aucune perte de saisie** sur coupure : reprise à l'endroit exact (§3.4), secrets non persistés en clair (re-demandés).
- Sur B2, hors ligne, on **met en avant la phrase** (utilisable hors ligne) et on **grise** honnêtement les méthodes réseau (au niveau méthode), avec **proposition de la carte SIM numérique de secours** — sans rien révéler du compte.

---

## 11. Performance

Budgets charte §9 (mobile d'entrée de gamme / réseau lent). Points spécifiques :

- **Feedback < 100 ms** sur chaque appui (sélection d'une protection, bouton primaire) même si le résultat complet arrive après — essentiel en récupération stressante.
- **Squelettes** (pas de spinner nu) pour : chargement du tableau A0, calcul du niveau (A5), calcul des alternatives (B4), écran d'attente d'approbation d'un contact.
- **Poids critique ≤ 200 Ko** : ces écrans sont **majoritairement textuels** ; aucune illustration lourde ; icônes légères ; respect de `prefers-reduced-data` (pas de préchargement des sous-parcours tant que non choisis).
- **Chargement progressif** : afficher le niveau et les lignes déjà connues **immédiatement** (données locales), compléter les états « réseau » **au fil de l'eau**, sans jamais révéler une posture de sécurité avant preuve de possession (§9).
- **60 fps** sur les transitions entre étapes ; jauge sans animation coûteuse (respect mouvement réduit).

---

## 12. Cas limites & erreurs

| Situation | Comportement | Message (FR, langage clair) |
|---|---|---|
| **Aucune protection configurée** au moment de récupérer (Volet B) | Pas d'impasse muette : page « Options restantes » + aide humaine + anti-hameçonnage. On explique honnêtement les limites de l'auto-conservation. **Sans confirmer** à un tiers qu'aucun facteur n'existait avant une preuve de possession (message générique). | `rzp.recup.aucuneprotection` : « Nous n'avons pas pu récupérer ce coffre avec les méthodes essayées. Voici ce que vous pouvez encore tenter. » |
| **Facteur choisi indisponible** (code expiré, contact injoignable, phrase incorrecte) | → B4 : cause claire (**sans nom de contact**) + **proposer une autre protection**, jamais de cul-de-sac ; facteur en échec marqué à la reprise. | `rzp.recup.echec.cause.{raison}` + `rzp.recup.echec.rassure` |
| **Toutes les options disponibles échouent maintenant** | Page « Options restantes » : ce qui redeviendra possible (réseau, carte SIM numérique de secours, réponse d'un contact) ; possibilité de **reprendre plus tard** ; jamais un mur. | `rzp.recup.optionsrestantes` : « Rien n'a fonctionné pour l'instant. Vous pourrez réessayer dès qu'une connexion ou une réponse d'un contact sera disponible. » |
| **Coupure réseau en plein parcours** | Reprise à l'endroit exact, saisie conservée (hors secrets) ; action réseau mise en file. | `rzp.offline.reseaurequis` + `rzp.commun.reprise` |
| **Nouvel appareil sans réseau, sauvegarde non locale** | Explique qu'il faut télécharger la sauvegarde → **propose la carte SIM numérique de secours**. | `rzp.offline.reseaurequis` |
| **Permission « contacts » refusée** (A4) | Pas de blocage : proposer la **saisie manuelle** du contact ; expliquer pourquoi la permission aidait. | `rzp.factor.contact.permissionrefusee` : « Pas de problème. Vous pouvez ajouter votre contact à la main. » |
| **Tentatives répétées sur la phrase** (B3) | Pas de punition anxiogène ni de verrou opaque ; réorienter vers une **autre protection** ; aucun compte à rebours non désactivable (2.2.1). | `rzp.recup.echec.cause.incorrect` + `rzp.recup.echec.cta.autre` |
| **Suspicion d'hameçonnage** (l'utilisateur dit qu'on lui a demandé sa phrase) | Renforcer le message ; ne jamais demander la phrase hors saisie locale ; orienter vers l'aide. | `rzp.secu.antiphishing` (renforcé sur B4) |
| **Contact de confiance approuve mais réseau tombe** | Approbation mise en file ; l'utilisateur est informé au retour du réseau ; pas de double demande. | `rzp.offline.reseaurequis` |
| **Sauvegarde chiffrée corrompue / illisible** | Message clair + **proposer un autre facteur** (phrase, carte SIM numérique de secours, contact) ; jamais de code technique. | `rzp.factor.sauvegarde.illisible` : « Cette sauvegarde n'a pas pu être ouverte. Essayons une autre protection. » |

---

## 13. Critères d'acceptation (testables)

- [ ] A0 affiche un **niveau de protection chiffré** (« X protection(s) active(s) sur 4 ») **et** un libellé qualitatif non anxiogène, doublés d'icônes (jamais la couleur seule).
- [ ] Le compteur et toutes les chaînes à quantité utilisent des **pluriels ICU** ; aucune concaténation ; testés **FR / EN / AR-RTL** et à **+40 %** de longueur sans troncature.
- [ ] **Isolation bidi** : toute variable interpolée (`{count}`, `{total}`, `{minutes}`, et tout `{name}` post-possession) est isolée (FSI/PDI) ; **test dédié** : afficher, en AR-RTL, un nom/valeur de **directionnalité opposée** sans mélange ni inversion.
- [ ] **Localisation numérique & directionnelle** : `count`, `total`, `minutes` **formatés selon la locale** (chiffres locaux) ; en RTL, **sens de remplissage de la jauge**, **flèches** et **glyphes de progression** mis en miroir ; testé FR/EN/AR-RTL.
- [ ] Chaque protection de A0 est **ajoutable** et **reportable** ; reporter affiche un **rappel de risque** sans culpabiliser ; l'ajout **incrémente** le niveau et **l'annonce** en région live.
- [ ] Les sous-parcours **phrase** et **carte SIM numérique de secours** sont **liés** (renvois vers `SPECS-phrase-recuperation.md` / `SPECS-esim-secours.md`) et **non dupliqués** ; à défaut de ces fichiers, **le comportement critique de saisie du secret en B3 est spécifié dans cette SPEC** (§5/B3, §9), avec ses 7 états.
- [ ] En Volet B, l'**identification** n'utilise **aucun test cognitif comme seul facteur** (WCAG 3.3.8) et repose sur la **possession** d'un facteur.
- [ ] **Anti-énumération :** **B1 et B2 ne permettent pas de distinguer un compte existant d'un compte inexistant** — réponse neutre identique en B1, erreurs de **format** seulement, et **ensemble générique** de méthodes en B2 (aucune liste de facteurs réellement configurés révélée avant une preuve de possession).
- [ ] **Non-divulgation du nom d'un contact :** aucun nom de contact de confiance n'est affiché avant validation d'un facteur de possession (B2, écran d'attente B3, et B4 utilisent un **libellé générique**).
- [ ] **Saisie du secret (B3) :** phrase **masquée par défaut**, **capture d'écran bloquée** (message accessible), **suggestions/autocorrection du clavier système désactivées**, **presse-papiers ordinaire non utilisé par défaut** ; **alternative accessible 3.3.8 disponible et testée** (saisie assistée sur liste fermée localisée hors ligne et/ou collage depuis gestionnaire dédié avec effacement du presse-papiers).
- [ ] La **phrase de récupération n'est jamais envoyée au serveur** ni saisie sur un écran serveur ; **aucun secret** n'apparaît dans les logs/analytique.
- [ ] Tout échec de facteur mène à **B4 avec au moins une alternative** quand elle existe, et à une page **« Options restantes »** sinon — **jamais un cul-de-sac muet**.
- [ ] La **bannière anti-hameçonnage** est présente sur **B0, B1, B2, B3, B4, B5** (cohérence 3.2.6), et **renforcée** sur B4.
- [ ] **Aide cohérente (3.2.6) :** les mécanismes d'aide apparaissent à un **emplacement et dans un ordre constants** sur tous les écrans.
- [ ] Les **7 états** (§13 charte) sont spécifiés pour **A0, A3, A4, A5, B0, B1, B2, B3, B4, B5** (ou leur absence justifiée) — dont le **Chargement** de A5 (calcul du niveau) et de B4 (calcul des alternatives).
- [ ] **Hors ligne** : ce qui exige le réseau est **marqué**, la **carte SIM numérique de secours** est proposée, et **aucune saisie n'est perdue** sur coupure ; les secrets sont re-demandés à la reprise.
- [ ] **Accessibilité** : contraste ≥ seuils, focus visible ≥ 3px, cibles ≥ 44px, ordre de focus logique (RTL inclus), **annonces d'état** en région live, **aucun geste sans alternative**, aucun timing bloquant non désactivable.
- [ ] B5 confirme la restauration **et** propose la **remise en sécurité** (rotation de la phrase, réassociation d'une carte SIM numérique de secours).
- [ ] **Cohérence nom d'écran / titre affiché** vérifiée pour tous les écrans (2.4.2) — notamment B2 (« Choisir une protection » / H1 « Comment récupérer votre coffre ? »).
- [ ] Toute microcopie affichée possède une **clé i18n** (préfixe `rzp.`) ; validation contre le **glossaire §16** (aucun « eSIM », « seed », « vault », « guardian » à l'écran), **y compris dans la prose interne** de cette SPEC.

---

## 14. Métriques de succès (KPIs UX)

1. **Taux de configuration multi-facteurs** : part d'utilisateurs atteignant **≥ 2 protections actives** (suppression du point unique de défaillance, `HYP-3`). *Cible interne à fixer avec Produit.*
2. **Taux de réussite de récupération** : part des parcours Volet B **aboutissant à B5** (compte récupéré), et **répartition par facteur** utilisé.
3. **Taux de rebond sur échec** : après un échec de facteur, part d'utilisateurs qui **basculent sur une autre protection** via B4 plutôt que d'abandonner (mesure directe de « jamais de cul-de-sac »).
4. **Temps de récupération** : durée médiane B0→B5, segmentée par facteur ; surveiller les personas Robert/Marc.
5. **Taux d'abandon par étape** : identifier l'étape la plus quittée (attendue : identification B1 et attente d'approbation d'un contact) pour prioriser les améliorations.

> Rappel charte §10 : ces métriques sont **anonymisées, sans contenu** ; aucun secret ni donnée personnelle sensible dans l'analytique. En particulier, aucun **nom de contact** ni signal permettant de reconstituer la posture de sécurité d'un compte n'est journalisé (§9).

---

## 15. Questions ouvertes

- **`Q3` (charte) — Facteurs exacts et priorité.** Cette SPEC suppose **4 facteurs** (phrase / carte SIM numérique de secours / sauvegarde chiffrée / contacts de confiance) et un ordre recommandé. À **confirmer** : nombre, dénomination, ordre, et **nombre d'approbations** requis pour les contacts de confiance. Le compteur « sur 4 » est **paramétré** (`{total}`) pour absorber un changement.
- **`Q4` (charte) — Carte SIM numérique de secours : gratuite (quota) ou premium ?** Impacte A2/B2 : faut-il un libellé « offre premium » sur ce facteur, et que se passe-t-il si l'utilisateur n'a pas l'offre au moment de récupérer ? (Le libellé « premium » ne doit pas devenir un vecteur d'énumération, §9.)
- **`Q7` (charte) — Contraintes réglementaires (KYC / RGPD / mentions légales).** Impacte **B1** (quel identifiant public est acceptable, quelle vérification d'identité est permise **sans divulguer de secret ni énumérer**) et la journalisation. **Point d'arbitrage sécurité :** confirmer qu'aucune contrainte réglementaire n'impose de confirmer l'existence d'un compte avant preuve de possession.
- **`Q6` (charte) — Langues de lancement & responsabilité de relecture** (FR/EN/ES/AR au minimum) : nécessaire pour figer les libellés de niveau, les messages d'échec en RTL, l'isolation bidi et le formatage numérique localisé.
- **Spécifique à cette SPEC — Rotation après récupération.** Faut-il **imposer** la rotation de la phrase après une récupération jugée à risque, ou seulement la **recommander** (choix retenu ici : recommander en B5, sans blocage) ? À arbitrer avec Produit/Sécurité.
- **Spécifique — Seuil de « niveau faible »** déclenchant le rappel non intrusif en entrée A (1 protection ? 0 ?), pour éviter toute pression perçue comme de l'urgence artificielle (charte §2.10).
- **Spécifique — Consolidation des SPECS de renvoi.** `SPECS-phrase-recuperation.md` et `SPECS-esim-secours.md` doivent être **rédigées** pour reprendre la chorégraphie détaillée ; d'ici là, les garanties de sécurité critiques de B3 restent portées par la présente SPEC (§5/B3, §9).

*Documents liés : `CHARTE-UX.md` (socle) · `SPECS-phrase-recuperation.md` · `SPECS-esim-secours.md` · `SPECS-onboarding-coffre.md` · `SPECS-abonnement-freemium.md`.*

---

## Alignement modèle de menace (T-010)

> Ajout T-010 : la récupération est **le moment d'attaque** (phishing, prise de contrôle). Alignement sur `../securite/MODELE-MENACE.md`. Constats centraux : **SEC-001/002/003 (P0)** (récupération sans backdoor, rotation sous step-up) et **SEC-004 (P0)** (phishing seed). On **spécifie la mesure UX** ; mécanismes **routés** (R12). **WCAG 2.2 AA maintenu**. Traçabilité : `ALIGNEMENT-MENACE.md`.

### Capture d'écran (par plateforme) — SEC-025 (P1) · VETO-V14
- Tout écran affichant un **facteur** (phrase re-affichée, code de secours, code d'activation de la SIM de secours) est **écran protégé** : **Android** bloque ; **iOS** occulte l'aperçu multitâche + détecte. Aucun SDK tiers (VETO-V6).

### Overlay / tapjacking — SEC-004 (P0) · angle mort D.1 (partage d'écran / RAT)
- **Détection d'overlay et de partage/enregistrement d'écran** pendant tout le parcours de récupération → **masquage des secrets** + avertissement ; **appuis obscurcis ignorés** sur les validations.
- **Cooldown** sur les actions sensibles (rotation, restauration) déclenchées **pendant** un partage d'écran détecté (contre la fraude par télé-assistance / RAT).

### Phishing de la seed — SEC-004 (P0) · VETO-V15 · VETO-V5 · VETO-V11
- La phrase est **saisie / re-dérivée localement uniquement** (jamais envoyée) ; **aucune API n'accepte la phrase**.
- **Aucun chemin support** ne peut restaurer le coffre : message explicite « **Même TEL ONLINE ne peut pas récupérer à votre place** » (VETO-V5, anti-escrow).
- **Step-up phishing-resistant** (passkey) — **jamais** d'OTP SMS/voix (VETO-V11) ; **délai / cooldown + notification hors-bande signée** avant toute rotation de clé/seed (SEC-003).
- **Number-matching + contexte affiché** sur toute approbation push (anti prompt-bombing / MFA-fatigue — SEC-031) ; limitation de fréquence.

### Autofill / presse-papiers — SEC-026 (P1) · VETO-V14 · angle mort D.1 (autofill)
- Les champs de saisie des facteurs (phrase, code de secours) sont **exclus de l'autofill tiers** (pas de fuzzy-match) ; **clavier système sécurisé** ; collage éventuel → **effacement immédiat** du presse-papiers, **local non synchronisé**.

### Télécom : port-out & renvoi d'appel — angle mort D.1 (P0) · SEC-005/006
- Comme la SIM de secours peut servir de facteur : **verrou de portage** (PIN + gel + **notification hors-bande** + cooldown) et **notification de tout changement de renvoi d'appel / messagerie vocale** (chemins de SIM-swap). Le **numéro n'est jamais un facteur d'authentification** (VETO-V11).

### Résilience (anti-DoS de récupération) — SEC-037/038 (P1)
- Rate-limit **par identité légitime** (pas par ressource) ; **alternatives hors-bande** ; jamais de cul-de-sac (un facteur indisponible propose toujours un autre facteur).

### Accessibilité maintenue (WCAG 2.2 AA)
- Parcours guidé **sans cul-de-sac** entièrement accessible ; **step-up** avec repli accessible (3.3.8) ; **number-matching** lisible/gros caractères ; avertissements en région live assertive ; messages en langage clair.

### Traçabilité (extrait)
| SEC-### / VETO | Mesure UX ici | Statut |
|---|---|---|
| **SEC-001/002/003 (P0)** · V5 | Aucun support ne restaure ; step-up + délai + notif signée avant rotation | ➕ T-010 |
| **SEC-004 (P0)** · V15 · V11 | Saisie/dérivation locale, pas de SMS, pédagogie | ✅ + tracé |
| SEC-031 | Number-matching + contexte + limitation (anti prompt-bombing) | ➕ T-010 |
| D.1 port-out / renvoi d'appel (P0) | Verrou de portage + notifications | ➕ T-010 |
| SEC-025 · SEC-026 · V14 | Anti-capture, anti-overlay, autofill exclu, clavier sécurisé | ➕ T-010 |
| SEC-037/038 | Anti-DoS de récupération, alternatives hors-bande | ➕ T-010 |
