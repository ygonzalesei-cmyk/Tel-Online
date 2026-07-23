# SPEC — Onboarding & création du coffre

## 0. Métadonnées

| Champ | Valeur |
|---|---|
| **Titre** | Onboarding & création du coffre |
| **Identifiant** | `SPECS-onboarding-coffre` |
| **Statut** | **v0.2 — révision post-audit** (pour revue en atelier) |
| **Mission** | onboarding-coffre — De la première ouverture jusqu'à « coffre prêt à l'emploi » |
| **Charte de référence** | [`CHARTE-UX.md`](./CHARTE-UX.md) — en cas de conflit, la charte fait foi |
| **Écrans couverts** | O1 Accueil & langue · O2 Auto-conservation · O3 Protection locale (code + biométrie) · O4 Confirmation du code · O5 Coffre prêt · O6 Notifications (permission différée) |
| **Langues** | Langue **affichée = langue de l'appareil si supportée** (FR · EN · ES · AR RTL). **FR = locale garantie de base**, EN = repli avant FR pour les non-francophones (cf. §6). Testé au minimum FR / EN / AR + expansion +40 %. |
| **Périmètre** | On **spécifie**, on ne code pas (R12). Wireframes textuels, exemples i18n = tables clé → texte. |
| **SPEC liée (lien, non dupliquée)** | [`SPECS-phrase-recuperation.md`](./SPECS-phrase-recuperation.md) — sécurisation de la récupération |

**Hypothèses produit utilisées** (à confirmer par le pôle Produit — cf. §15) :

| Réf. | Hypothèse | Source |
|---|---|---|
| `HYP-1` | Coffre numérique personnel en **auto-conservation** : les secrets restent chiffrés sur l'appareil. | Charte §1 |
| `HYP-2` | Le coffre est restaurable via une **phrase de récupération**. | Charte §1 |
| `HYP-6` | La **protection locale** du coffre est un **code numérique de 6 chiffres** (longueur **fixe** dans cette version). La robustesse **ne repose pas sur la seule longueur** du code, mais sur une **dérivation ancrée matériellement et limitée en débit** (voir §9 « Plancher de sécurité »). **Biométrie optionnelle** en accélérateur, **jamais unique facteur imposé**. Une longueur variable ≥ 6 reste à l'étude (Q-A). *Extension locale de la charte — à valider.* | Cette SPEC |
| `HYP-7` | La **création du coffre** (génération des clés locales) se fait **entièrement hors ligne**, sans compte serveur préalable. | Cette SPEC + Charte §8 |
| `HYP-8` | La **phrase de récupération** est proposée **juste après** « coffre prêt » ; elle peut être **reportée** avec rappel du risque, jamais imposée à l'aveugle. | Charte §14 + `HYP-2` |

---

## 1. Objectif & enjeux

**Rôle du parcours.** Faire passer une personne, en **5 écrans au plus** (O1 → O5), de la **première ouverture de l'application** à un **coffre créé, protégé localement et prêt à l'emploi**, tout en préparant clairement l'étape suivante (sécuriser la récupération). C'est le **premier contact** avec le produit : il doit livrer la valeur (« un coffre à vous seul ») avant de demander un effort, et poser d'emblée la confiance.

**Pourquoi ce parcours est sensible.**
- **Secrets** : dès la création, on génère des clés et un secret maître qui vivront sur l'appareil. Une mauvaise compréhension à ce stade (« qui peut récupérer mes données ? ») a des conséquences durables.
- **Argent & identité** : le coffre est destiné à contenir des éléments à forte valeur (clés, mots de passe, documents, contacts de confiance). L'auto-conservation signifie que **personne, pas même TEL ONLINE, ne peut restaurer à la place de l'utilisateur** — la responsabilité est réelle et doit être expliquée sans effrayer (Charte §10).
- **Point de bascule d'abandon** : la friction ou la peur ici fait perdre l'utilisateur avant toute valeur perçue. L'onboarding doit être **calme, court, réversible**.

**Position dans le produit.** Ce parcours **s'arrête à « coffre prêt »** et **passe le relais** à la sécurisation de la récupération, spécifiée ailleurs (lien §0). Il ne décrit **pas** la phrase de récupération, l'eSIM ni le premier remplissage du coffre. **La politique de déverrouillage du coffre à l'usage** (temporisation/verrouillage après échecs) relève de l'écran d'ouverture, mais son **plancher de sécurité est posé ici** (§9) car il conditionne la valeur du code créé.

---

## 2. Personas & cas d'usage concernés

Renvoi aux personas de la **Charte §3**. Le parcours doit être validé pour Robert, Amina et Marc, pas seulement Camille.

| Persona | Concerné ? | Pourquoi / points d'attention ici |
|---|---|---|
| **Camille (pragmatique)** | ✅ Fort | Veut aller vite : onboarding en peu d'étapes, biométrie pour accélérer, pas de mur de texte. Risque : passer trop vite la sécurisation de la récupération → rappel clair mais non bloquant. |
| **Robert (prudent, 71 ans)** | ✅ Fort | A peur de « tout casser ». Besoin de réassurance à chaque étape, gros caractères, langage FALC, possibilité de revenir en arrière, code plutôt que geste ; **aucun test de mémoire sans échappatoire** (alternative gestionnaire de mots de passe garantie). |
| **Amina (multilingue FR/AR, RTL)** | ✅ Fort | **Choix de langue dès l'écran 1**, atteignable **à une main**, mise en page RTL correcte, `lang`/`dir` posés, **chiffres localisés** (arabes-indiens), aucune concaténation. |
| **Léa (nomade, réseau instable)** | ✅ Fort | La **création doit marcher hors ligne** (avion, étranger, réseau coupé) sans perte d'étape. |
| **Marc (lecteur d'écran)** | ✅ Fort | Ordre de lecture logique, libellés explicites, annonces d'état, progression annoncée, **prononciation AR correcte** (`lang=ar`), saisie du code accessible, désactivé **restitué** (aria-disabled). |

**Cas d'usage couverts :** première installation ; reprise après interruption (app fermée en cours d'onboarding) ; **coffre déjà existant** (garde-fou anti-écrasement, §3) ; utilisateur hors ligne ; utilisateur qui refuse la biométrie et/ou les notifications ; utilisateur non francophone.

---

## 3. Pré-requis, points d'entrée et de sortie

**Pré-requis (ce qui doit exister avant).**
- Application installée ; aucun compte serveur requis (`HYP-7`).
- Catalogue de messages i18n disponible hors ligne (FR/EN/ES/AR), avec **catégories de pluriel complètes par locale** (jusqu'à 6 en AR).
- Capacité de génération locale des clés et de **stockage sécurisé ancré matériellement** (Keychain/Secure Enclave, Keystore/StrongBox).
- **Gestionnaire de mots de passe / remplissage automatique système** (Trousseau iOS, Autofill Android) — support requis comme **alternative non cognitive** à la saisie (§8, WCAG 3.3.8).
- API biométrique système (optionnelle) et permission notifications système (différée).

**Points d'entrée.**
- **E1** — Première ouverture de l'app après installation (cas nominal → écran O1).
- **E2** — Réouverture alors que l'onboarding n'est **pas** terminé → **reprise** (cf. ci-dessous).
- **E3** — Lien profond « créer mon coffre » (site, invitation) → O1 avec langue pré-résolue.

**Points de sortie.**
- **S1 (succès)** — « Coffre prêt » (O5), coffre créé et protégé localement.
- **S2 (relais)** — Depuis O5, bascule vers **sécurisation de la récupération** ([`SPECS-phrase-recuperation.md`](./SPECS-phrase-recuperation.md)).
- **S3 (report)** — Depuis O5, entrée dans l'app avec un **rappel persistant** « récupération non sécurisée » (non bloquant).
- **S4 (abandon)** — Sortie avant O5 ; aucun coffre créé tant que le code n'est pas **confirmé** (O4). Reprise possible via E2.

**Reprise après interruption.**
- Chaque étape enregistre un **jalon local** (aucun secret persisté avant confirmation).
- Interruption **avant O4 confirmé** → reprise au **début de la protection (O3)** ; le code saisi n'est **jamais** conservé pour un retour partiel (sécurité).
- Interruption **après O4 confirmé** (coffre existant, non encore « sécurisé récupération ») → reprise sur **O5 / rappel S3**.
- Aucune saisie utile perdue ; retour arrière toujours possible sans effacer les choix déjà validés (Charte §2.5).

**Garde-fou anti-écrasement (coffre déjà existant).**
- Si un **coffre est déjà créé et protégé** et que l'utilisateur re-déclenche un parcours de **création** (lien profond, entrée manuelle), on **n'entre pas** silencieusement dans O3. Une **modale de confirmation** (Charte §12) décrit la conséquence : « Un coffre existe déjà sur cet appareil. En créer un nouveau **remplacera** l'actuel et **effacera** son contenu. Cette action est **irréversible**. » Action destructrice à la fin/droite, jamais bouton par défaut ; sortie « Annuler » proéminente. Sans confirmation explicite, on redirige vers l'ouverture du coffre existant, pas vers la création.

---

## 4. Parcours utilisateur (flux)

> **Progression numérotée.** O1 et O2 constituent la phase d'**introduction & valeur** (non numérotées). Les **3 étapes** de création — O3, O4, O5 — portent la barre « Étape *n* sur 3 », **affichée et annoncée de façon identique sur les trois écrans** (y compris « Étape 3 sur 3 » sur O5). Pour éviter tout saut brutal, **O2 annonce à l'avance** « Encore 3 étapes pour créer votre coffre » (`onboarding.valeur.etapesAvenir`).

```
        [E1 première ouverture]
                 │
        ┌────────▼─────────┐
        │  O1 Accueil +    │  choix de langue accessible ici (à une main)
        │  proposition     │  (et à tout moment ensuite)  — non numéroté
        │  de valeur       │
        └────────┬─────────┘
                 │ « Commencer »
        ┌────────▼─────────┐
        │  O2 Auto-        │  « vos secrets ne quittent pas votre téléphone »
        │  conservation    │  annonce « Encore 3 étapes »  — non numéroté
        └────────┬─────────┘
                 │ « Créer mon coffre »
        ┌────────▼─────────┐   ← Étape 1 sur 3
        │  O3 Protection   │  choisir un code (6 chiffres, non triviaux)
        │  locale          │  biométrie = option proposée, non imposée
        └────────┬─────────┘
                 │ code saisi
        ┌────────▼─────────┐   ← Étape 2 sur 3
        │  O4 Confirmation │  re-saisir le même code (autofill autorisé)
        │  du code         │  → génération locale du coffre (hors ligne OK)
        └────────┬─────────┘
                 │ codes identiques → coffre créé
        ┌────────▼─────────┐   ← Étape 3 sur 3
        │  O5 Coffre prêt  │  invite à sécuriser la récupération
        └───┬─────────┬────┘
   « Sécuriser │       │ « Plus tard »
    maintenant»│       │
        ┌───────▼──┐  ┌─▼──────────────┐
        │ →SPEC    │  │ Entrée app +   │
        │ phrase   │  │ rappel S3      │
        │ récup.   │  │ (non bloquant) │
        └──────────┘  └──────┬─────────┘
                             │ 1re action utile (ex. activer un rappel)
                      ┌──────▼─────────┐
                      │ O6 Notifs      │  permission demandée EN CONTEXTE
                      │ (différée)     │  refus accepté ; fermeture au tap
                      └────────────────┘
```

**Branchements & retours.**
- **Retour arrière** disponible sur O1→O5 (flèche « Retour » ≥ 44 px + geste, bouton toujours visible). Depuis O3/O4, revenir n'efface pas la langue ni les écrans lus.
- **O3 biométrie** : si l'appareil ne propose pas de biométrie ou si l'utilisateur refuse → on continue avec le code seul, sans cul-de-sac. **L'alternative non cognitive garantie n'est pas la biométrie mais le remplissage automatique système** (§8).
- **O4 échec** (codes différents) → message + nouvelle saisie de la confirmation, sans reprendre tout O3 ; **compteur d'essais neutre** (pas de menace — la confirmation n'est pas une surface d'attaque, le plancher anti-force-brute de §9 concerne l'ouverture du coffre).
- **O6 notifications** : n'apparaît **jamais** au lancement ; déclenché par un besoin réel (rappel de sécurisation, alerte). Un refus laisse l'app pleinement fonctionnelle. Fermeture possible au **simple appui** (bouton/poignée), pas seulement au glissement (WCAG 2.5.7).

---

## 5. Écrans & états

> Pour chaque écran : **but**, **éléments**, **action primaire unique**, **actions secondaires**, puis les **états applicables** parmi les 7 (Charte §13). Les états non pertinents sont justifiés.

### O1 — Accueil & proposition de valeur (choix de langue)

- **But** : donner envie (valeur en une phrase) et permettre le **choix de langue immédiat**.
- **Éléments** : titre produit, phrase de valeur, illustration sobre (décorative, `alt` vide), **sélecteur de langue** (cible ≥ 44 px, emoji globe **masqué au lecteur d'écran** `aria-hidden`, nom accessible = « Langue »), action primaire, lien secondaire discret.
- **Action primaire (unique)** : « Commencer ».
- **Actions secondaires** : ouvrir le sélecteur de langue ; « J'ai déjà un coffre » (renvoi vers récupération — hors périmètre, simple lien, cible ≥ 44 px).
- **Langue initiale & repli** : voir §6 — la langue **de l'appareil** est utilisée si supportée ; à défaut, repli **EN puis FR** ; **jamais un basculement forcé sur FR** pour un utilisateur AR/EN.
- **États** :
  - *Chargement* : squelette du titre + boutons (jamais page blanche).
  - *Contenu* : état nominal ci-dessus.
  - *Hors ligne* : **fonctionne à l'identique** ; aucun bandeau d'erreur (rien ici n'exige le réseau).
  - *Permission requise* : sans objet (aucune permission ici).
  - *Vide / Erreur / Succès* : sans objet (écran d'entrée statique). **En cas d'échec de chargement d'un catalogue i18n**, repli sur la **locale supportée la plus proche déjà disponible**, puis **EN**, puis FR en dernier recours (§6) — sans bloquer.

### O2 — Auto-conservation (langage clair)

- **But** : expliquer, sans jargon, que **les secrets restent sur l'appareil** et que la sauvegarde sera cruciale (Charte §10) ; **annoncer les 3 étapes à venir**.
- **Éléments** : titre court, 2–3 points clés illustrés par icône + texte (jamais la couleur seule), encart rassurant, **annonce « Encore 3 étapes »**, action primaire, retour. **Point d'aide « En savoir plus »** placé à l'**emplacement d'aide constant** défini au §8 (WCAG 3.2.6).
- **Action primaire (unique)** : « Créer mon coffre ».
- **Actions secondaires** : « Retour » ; lien « En savoir plus » (aide, ouverte hors ligne, cible ≥ 44 px).
- **États** :
  - *Chargement* : squelette liste de points.
  - *Contenu* : nominal.
  - *Hors ligne* : identique, aucune dépendance réseau.
  - *Vide / Erreur / Succès / Permission* : sans objet (écran explicatif statique).

### O3 — Protection locale : code + biométrie optionnelle

- **But** : créer un **code local** protégeant l'ouverture du coffre ; proposer la **biométrie** comme accélérateur.
- **Éléments** : **barre de progression « Étape 1 sur 3 »** (annoncée), titre, aide (ICU, message pluralisé, cf. §7), **champ code secret** (masqué par défaut, bascule « Afficher »), **rappel anti-hameçonnage contextuel bref** (« TEL ONLINE ne vous demandera jamais ce code »), option biométrie présentée **après** validité du code (bascule claire), action primaire.
- **Saisie & alternative (3.3.8)** : clavier **numérique** système ; **presse-papiers manuel non proposé**, mais **enregistrement/remplissage par le gestionnaire de mots de passe système autorisé et proposé** (opt-in), ce qui constitue l'alternative non cognitive disponible sur tous les appareils cibles (§8/§9).
- **Action primaire (unique)** : « Continuer ».
- **Actions secondaires** : bascule « Afficher / Masquer » le code (état `aria-pressed`) ; bascule biométrie (option) ; « Retour ».
- **États** :
  - *Chargement* : squelette champ + progression.
  - *Vide* : champ au repos, aide visible, primaire en **`aria-disabled`** (bouton **focalisable**) ; l'explication (« Saisissez 6 chiffres pour continuer ») est **visible** et **liée** au bouton/champ par `aria-describedby`, donc restituée au lecteur d'écran (Charte §12).
  - *Contenu* : saisie en cours ; primaire activée dès le format atteint (6 chiffres).
  - *Erreur* : code trop court / caractères non numériques → message inline relié au champ, annoncé (poli). **Code trivial** (000000, 123456, répétitions/suites) → **refus calme** avec message (cf. §9, §12).
  - *Hors ligne* : **pleinement fonctionnel** (aucun serveur).
  - *Permission requise* : à l'activation de la biométrie, si l'OS demande l'autorisation → explication **en contexte** (« Utiliser votre empreinte / visage pour ouvrir le coffre plus vite »). Refus → on reste sur code seul, **sans perdre l'alternative gestionnaire de mots de passe**.
  - *Succès* : implicite au passage vers O4.
- **Sécurité d'affichage** : le code révélé via « Afficher » est **re-masqué automatiquement** au passage en arrière-plan, au verrouillage de l'appareil et après une courte inactivité (§9).

### O4 — Confirmation du code

- **But** : vérifier que l'utilisateur **mémorise** son code (prévenir l'erreur — Charte §2.9) et **créer le coffre**.
- **Éléments** : **barre « Étape 2 sur 3 »**, titre « Confirmez votre code », **champ code secret** (masqué, bascule), rappel discret « le même que précédemment ». À la validation : **génération locale** du coffre.
- **Alternative à la ressaisie (3.3.8)** : le **remplissage automatique** du gestionnaire de mots de passe est **autorisé** ici — la confirmation n'est donc **pas un test cognitif sans échappatoire** ; l'utilisateur sans biométrie dispose de cette alternative non cognitive.
- **Action primaire (unique)** : « Créer mon coffre ».
- **Actions secondaires** : bascule « Afficher » ; « Retour » (revient à O3 pour redéfinir le code).
- **États** :
  - *Chargement* : bref squelette ; à la validation, **retour < 100 ms** (« Création… ») puis génération locale.
  - *Vide* : primaire en `aria-disabled` (focalisable) avec explication liée.
  - *Contenu* : saisie en cours.
  - *Erreur* : codes différents → message clair + reprise de la **confirmation seule**, sans reperdre O3 ; **compteur d'essais neutre** (pas de menace).
  - *Hors ligne* : **création réalisée hors ligne** ; aucun envoi serveur (Charte §8, §10).
  - *Succès* : transition vers O5 avec annonce « Votre coffre est prêt ».
  - *Permission requise* : sans objet.

### O5 — Coffre prêt (invite à sécuriser la récupération)

- **But** : confirmer le succès et **orienter clairement** vers la sécurisation de la récupération, **sans dupliquer** cette SPEC.
- **Éléments** : **barre « Étape 3 sur 3 » (pleine)**, bandeau succès (icône + texte), titre « Votre coffre est prêt », phrase de responsabilité, **encart d'avertissement calme** expliquant le risque de report, action primaire, action secondaire.
- **Thème sombre** : l'encart d'avertissement n'utilise **pas** le token clair `warning-surface` (ambre) avec `ink` foncé (illisible en sombre). En thème sombre, il emploie une **surface d'avertissement dédiée sombre** (ambre désaturé/assombri + texte clair) garantissant **texte ≥ 4.5:1** et **icône/bordure ≥ 3:1** (token à valider avec le design visuel — voir §8 et §15 Q-H).
- **Action primaire (unique)** : « Sécuriser ma récupération » → **lien** vers [`SPECS-phrase-recuperation.md`](./SPECS-phrase-recuperation.md).
- **Actions secondaires** : « Plus tard » (report → entrée app + **rappel persistant** S3, non bloquant).
- **États** :
  - *Succès* : état nominal (c'est l'écran de succès du parcours).
  - *Hors ligne* : identique ; l'entrée dans l'app et le rappel fonctionnent hors ligne. Si la sécurisation de la récupération nécessite un réseau, ce point est traité dans **sa** SPEC (lien), pas ici.
  - *Chargement / Vide / Erreur / Permission* : sans objet (écran de confirmation).

### O6 — Notifications (permission différée, en contexte)

- **But** : n'obtenir l'autorisation de notifier **qu'au moment utile** (jamais au lancement), avec explication du bénéfice.
- **Éléments** : feuille inférieure (bottom sheet) déclenchée par une action à valeur (ex. « me rappeler de sécuriser ma récupération »), **poignée + bouton de fermeture au simple appui** (WCAG 2.5.7), texte de bénéfice, action primaire, action secondaire.
- **Action primaire (unique)** : « Activer les rappels » → déclenche la **demande système** OS.
- **Actions secondaires** : « Pas maintenant » (ferme sans pénalité, ré-proposable plus tard) ; **fermeture au tap** équivalente au glissement vers le bas.
- **États** :
  - *Permission requise* : **état central** de cet écran (Charte §13.7) — pourquoi, quand, bénéfice concret.
  - *Contenu* : feuille affichée ; focus déplacé dedans, jamais piégé, restitué à la fermeture ; **reste visible** malgré le clavier/la barre ancrée (2.4.11).
  - *Succès* : « Rappels activés » (bannière brève).
  - *Erreur* : permission refusée au niveau OS → message honnête + chemin vers les réglages système, sans blocage.
  - *Hors ligne* : l'autorisation ne dépend pas du réseau ; fonctionne hors ligne.
  - *Chargement / Vide* : sans objet.

---

## 6. Internationalisation appliquée (langue, repli, numération, direction)

> Décline la **Charte §6** sur ce parcours. Aucune chaîne en dur, aucune concaténation ; toutes les insertions et tous les décomptes passent par **messages ICU** (pluriels + formatage de nombre).

**Langue initiale & chaîne de repli.**
- Langue affichée = **langue de l'appareil** si elle fait partie des langues supportées (FR / EN / ES / AR).
- Si la langue de l'appareil n'est **pas** supportée : repli sur la **locale supportée la plus proche** (famille/variante), puis **EN**, puis **FR** en dernier recours. On **ne bascule jamais d'office sur FR** un utilisateur AR ou EN (correctif de l'ancien comportement « FR par défaut universel »).
- **Échec de chargement d'un catalogue** : repli sur la locale supportée la plus proche **déjà chargée**, puis EN, puis FR — **jamais** un retour systématique sur FR ; l'onboarding continue sans bloquer.
- FR reste la **locale garantie de base** du produit (charte §6), mais ce statut ne prime pas sur la langue de l'appareil pour l'affichage.

**Direction & prononciation (WCAG 3.1.1 / 3.1.2).**
- Le changement de langue met à jour **`lang`** et **`dir`** sur la **racine** du contenu (`dir=rtl` + `lang=ar` en arabe), afin que le lecteur d'écran prononce l'arabe avec la **voix et les règles AR**, pas FR.
- Tout **segment dans une autre langue** (nom de produit, exemple) est marqué avec son propre `lang`.
- La **mise en page RTL** utilise des **propriétés logiques** (début/fin) ; progression, flèches et alignements sont **mis en miroir**.

**Numération localisée.**
- Les **compteurs et libellés** (« Étape 1 sur 3 », « à 6 chiffres ») affichent les **chiffres selon la locale** — **arabes-indiens (٠١٢٣…)** en AR — via le formatage de nombre ICU (`{n, number}`), jamais des chiffres occidentaux figés.
- **Le clavier de saisie du code** peut **rester en chiffres occidentaux** pour la cohérence et l'univocité de sécurité de la valeur saisie ; ce choix est **documenté** et ne dépend pas de la locale d'affichage.

---

## 7. Microcopie (FR)

> Aucune chaîne en dur, aucune concaténation ; pluriels/insertions/nombres via **format ICU**. Chaque décompte est un **message pluralisé** ; les catalogues fournissent **toutes les catégories de pluriel de la locale** (jusqu'à `zero/one/two/few/many/other` en AR). Clés sous les namespaces `onboarding.*`. Les traductions EN/ES/AR sont produites depuis ces clés (non listées ici).

| Élément | Clé i18n | Texte FR | Note i18n / traduction |
|---|---|---|---|
| Sélecteur de langue (label) | `onboarding.langue.label` | Langue | Nom accessible ; emoji globe `aria-hidden` ; valeur courante annoncée séparément. |
| Titre accueil | `onboarding.accueil.titre` | Votre coffre numérique, à vous seul. | Prévoir +40 % ; ne pas tronquer. « coffre » = terme glossaire. |
| Valeur accueil | `onboarding.accueil.valeur` | Gardez vos secrets en sécurité, sur votre téléphone. | FALC, voix active. |
| Bouton commencer | `onboarding.accueil.cta` | Commencer | Verbe d'action. |
| Lien « déjà un coffre » | `onboarding.accueil.dejaCoffre` | J'ai déjà un coffre | Renvoi récupération (autre SPEC). |
| Titre auto-conservation | `onboarding.valeur.titre` | Vos secrets restent sur votre appareil. | Phrase clé demandée par la mission. |
| Point chiffrement | `onboarding.valeur.point.chiffre` | Chiffré, lisible par vous seul. | Bénéfice, pas la technique (glossaire). |
| Point « reste local » | `onboarding.valeur.point.local` | Vos secrets ne quittent pas votre téléphone. | **Reformulé** : sans jargon (« serveur » retiré) et sans ambiguïté ; toute nuance sur une future sauvegarde chiffrée est réservée à l'écran concerné. |
| Point responsabilité | `onboarding.valeur.point.sauvegarde` | Personne ne peut récupérer à votre place. La sauvegarde compte. | Explique sans effrayer (Charte §10). |
| Étapes à venir | `onboarding.valeur.etapesAvenir` | `Encore {n, plural, one {# étape} other {# étapes}} pour créer votre coffre.` | ICU pluralisé + nombre localisé ; évite le saut brutal de la progression. |
| Lien en savoir plus | `onboarding.valeur.aide` | En savoir plus | Emplacement d'aide constant (§8, 3.2.6). Ouvre l'aide hors ligne. |
| Bouton créer (O2) | `onboarding.valeur.cta` | Créer mon coffre | — |
| Progression | `onboarding.progression` | `Étape {n, number} sur {total, number}` | ICU ; **nombres formatés par locale** (chiffres arabes-indiens en AR) ; structure/accord laissés à la locale ; jamais concaténé. |
| Titre choix code | `onboarding.code.titre` | Choisissez un code | — |
| Aide code | `onboarding.code.aide` | `Un code à {longueur, plural, one {# chiffre} other {# chiffres}} que vous seul connaissez.` | **ICU pluralisé** ; `#` formaté par locale ; catalogue AR = 6 catégories. |
| Rappel anti-hameçonnage (contextuel) | `onboarding.securite.antiHameconnage` | TEL ONLINE ne vous demandera jamais ce code, par message ou par appel. | **Surfacé au moins une fois** dans l'onboarding (O3). Version complète disponible dans l'aide. |
| Libellé champ code | `onboarding.code.champ.label` | Code | Libellé au-dessus, jamais placeholder seul. |
| Bascule afficher | `onboarding.code.afficher` | Afficher | Alterne avec `onboarding.code.masquer` ; `aria-pressed`. |
| Bascule masquer | `onboarding.code.masquer` | Masquer | — |
| Option biométrie | `onboarding.biometrie.option` | Ouvrir avec l'empreinte ou le visage | Neutre (empreinte OU visage) ; pas de marque OS. |
| Bouton continuer | `onboarding.code.cta` | Continuer | — |
| Primaire désactivée (aide) | `onboarding.code.ctaDesactive` | `Saisissez {longueur, plural, one {# chiffre} other {# chiffres}} pour continuer.` | **ICU pluralisé** ; texte **visible** lié par `aria-describedby` (bouton `aria-disabled`). |
| Code trivial refusé | `onboarding.erreur.codeTrivial` | Ce code est trop facile à deviner. Choisissez-en un autre. | Calme, sans reproche (Charte §7/§10). |
| Titre confirmation | `onboarding.confirmation.titre` | Confirmez votre code | — |
| Aide confirmation | `onboarding.confirmation.aide` | Saisissez à nouveau le même code. | — |
| Bouton créer (O4) | `onboarding.confirmation.cta` | Créer mon coffre | — |
| État création | `onboarding.confirmation.enCours` | Création de votre coffre… | Feedback < 100 ms ; annoncé (région live polie). |
| Titre coffre prêt | `onboarding.pret.titre` | Votre coffre est prêt | Écran succès. |
| Sous-titre coffre prêt | `onboarding.pret.sousTitre` | Pour ne jamais le perdre, sécurisez votre récupération. | Oriente vers l'autre SPEC. |
| Avertissement report | `onboarding.pret.avertissement` | Sans récupération, un téléphone perdu peut signifier un coffre perdu. Personne ne peut le restaurer à votre place. | Calme, pas d'urgence artificielle. |
| Primaire sécuriser | `onboarding.pret.ctaSecuriser` | Sécuriser ma récupération | **Lien** vers `SPECS-phrase-recuperation`. |
| Secondaire report | `onboarding.pret.plusTard` | Plus tard | Report autorisé (Charte §14). |
| Rappel persistant | `onboarding.rappel.recuperation` | Récupération non sécurisée — protégez votre coffre. | Bannière non bloquante (S3). |
| Confirmation écrasement coffre | `onboarding.ecrasement.confirmation` | Un coffre existe déjà sur cet appareil. En créer un nouveau remplacera l'actuel et effacera son contenu. Cette action est irréversible. | Modale de confirmation (Charte §12) ; action destructive à la fin, jamais par défaut. |
| Titre notifs | `onboarding.notifs.titre` | Rester informé | — |
| Bénéfice notifs | `onboarding.notifs.benefice` | Recevez un rappel pour sécuriser votre récupération, et des alertes de sécurité. | En contexte, bénéfice concret. |
| Fermer la feuille | `onboarding.notifs.fermer` | Fermer | Cible ≥ 44 px, simple appui (2.5.7). |
| Primaire notifs | `onboarding.notifs.activer` | Activer les rappels | Déclenche la demande OS. |
| Secondaire notifs | `onboarding.notifs.pasMaintenant` | Pas maintenant | Ré-proposable, sans pénalité. |
| Notifs activées | `onboarding.notifs.succes` | Rappels activés | Bannière brève. |
| Notifs refusées (OS) | `onboarding.notifs.refus` | Vous pouvez activer les rappels plus tard dans les réglages de votre téléphone. | Honnête, chemin de sortie. |
| Retour | `commun.retour` | Retour | Réutilisé ; libellé accessible = texte visible ; cible ≥ 44 px. |

**Erreurs (microcopie dédiée §12) :** voir la table du §12, chaque message possède sa clé `onboarding.erreur.*`.

---

## 8. Accessibilité — WCAG 2.2 AA (spécifique aux écrans)

| Critère (n°) | Comment il est satisfait ici |
|---|---|
| **Contraste texte — thème clair 1.4.3** | Textes en `color.ink` (16.9:1) / `ink-muted` (5.9:1) ; boutons primaires blanc sur `primary` (6.6:1). Toutes paires ≥ 4.5:1 (Charte §11.1). |
| **Contraste texte — thème sombre 1.4.3 / 1.4.11** | Le thème **suit le réglage système** (`prefers-color-scheme`). Tokens §11.2 : `ink` sombre `#F2F4F7` (~17:1 sur `#0E1116`), `primary` sombre `#6AA6FF` (7.7:1), surfaces `#171B21`. **Toute paire texte/fond ≥ 4.5:1**, chaque composant ≥ 3:1. **Encart d'avertissement O5 en sombre** : surface d'avertissement **dédiée sombre** (ambre assombri + texte clair, ≥ 4.5:1) — l'ambre clair `warning-surface` + `ink` foncé **n'est pas** réutilisé (illisible). **Icônes de statut** re-teintées pour ≥ 3:1 en sombre. |
| **Contraste non-textuel 1.4.11** | Bordure du champ code `border-strong` (4.6:1 clair ; équivalent validé en sombre), focus ≥ 3:1, icônes de statut ≥ 3:1. |
| **Couleur non seule 1.4.1** | Succès/erreur/avertissement portés par **icône + texte + couleur** (bandeaux O5, messages O4). Progression = texte « Étape n sur total » + barre. |
| **Reflow / zoom 1.4.10 / 1.4.4** | Contenu utilisable à 320 px et 400 % ; corps ≥ 16 px ; respect de la taille système jusqu'à 200 %. Aucune largeur figée sur les boutons (expansion FR/AR +40 %). |
| **Ordre & visibilité du focus 2.4.3 / 2.4.7 / 2.4.13** | Ordre de focus = ordre de lecture (miroir en RTL). **Focus visible** ≥ 3 px, offset 2 px, contraste ≥ 3:1 (visibilité = 2.4.7 ; apparence = 2.4.13). À l'ouverture de la feuille O6, focus déplacé dedans, restitué à la fermeture ; jamais piégé (2.1.2). |
| **Focus non masqué 2.4.11** | *(critère corrigé, distinct de la visibilité du focus)* L'élément focalisé — **champ code** et **bouton primaire ancré** — reste **au moins partiellement visible** quand le **clavier système** ou la **barre d'action ancrée** sont présents : **`scroll-into-view`** au focus + **marge réservée au clavier** (insets). Même garantie pour la feuille O6. |
| **Titre de page 2.4.2** | Chaque écran a un titre unique annoncé (`onboarding.accueil.titre`, `onboarding.pret.titre`, etc.). |
| **Langue de la page & des parties 3.1.1 / 3.1.2** | Au changement de langue, `lang` et `dir` posés sur la racine ; segments d'une autre langue marqués (§6). Garantit la **prononciation SR correcte en AR**. |
| **Nom accessible des contrôles 2.5.3 / 4.1.2** | Le nom accessible **contient le texte visible** (« Continuer », « Afficher », « Activer les rappels », « Retour »). Bascule afficher/masquer expose son état (`aria-pressed`). Emoji globe du sélecteur `aria-hidden`. |
| **Annonces d'état (région live) 4.1.3** | Région *live* **polie** pour « Création de votre coffre… », « Coffre prêt », passage hors ligne, progression d'étape ; **assertive** pour l'erreur « les deux codes sont différents » **et pour le message anti-capture** (`onboarding.erreur.captureBloquee`). |
| **Cibles tactiles 2.5.8 + norme interne** | Toutes les cibles ≥ 44 px (viser 48), espacement ≥ 8 px : boutons, bascule afficher, interrupteur biométrie, fermeture de la feuille, **chevron « Retour »**, **sélecteur de langue**, **liens texte** (« J'ai déjà un coffre », « Plus tard », « En savoir plus », « Pas maintenant »). |
| **Mouvement de glissement 2.5.7** | La fermeture de la feuille O6 dispose d'une **alternative au simple appui** (bouton/poignée « Fermer »), équivalente au glissement vers le bas (Charte §4). |
| **Aide cohérente 3.2.6** | Le mécanisme d'aide (« En savoir plus » / point d'aide) apparaît au **même emplacement relatif** sur tous les écrans qui en proposent un. |
| **Mouvement réduit 2.3.1 + prefers-reduced-motion** | Transitions d'étape **désactivées** sous `prefers-reduced-motion` ; aucune animation > 3 flashs/s ; illustrations décoratives sans mouvement imposé. |
| **Authentification accessible 3.3.8** | Le code est un **secret choisi par l'utilisateur**, pas une énigme. L'**alternative non cognitive** exigée est le **gestionnaire de mots de passe / remplissage automatique système** (Trousseau iOS, Autofill Android) — **disponible sur tous les appareils cibles**, donc c'est **elle** (et non la biométrie, simple accélérateur optionnel) qui **établit la conformité 3.3.8**. La ressaisie de confirmation (O4) dispose de cette même alternative (autofill), donc n'est pas un test cognitif sans échappatoire. Le **presse-papiers manuel** reste évité sur le champ secret, mais l'autofill l'emporte comme alternative garantie. |
| **Saisie aidée 3.3.7** | On ne redemande pas d'information déjà fournie (langue, écrans lus conservés au retour arrière). |
| **Clavier / vocal 2.1.1 / 2.2.1** | Tout est actionnable au clavier / commande vocale ; aucun délai imposé pour terminer l'onboarding (pas de minuterie). |
| **Orientation 1.3.4** | Pas de blocage portrait/paysage ; mise en page adaptative. |

---

## 9. Sécurité & confiance UX

Règles dérivées de la **Charte §10**, appliquées à ce parcours :

- **Auto-conservation explicite (O2, O5)** : on énonce clairement que les secrets **restent sur l'appareil** et que **TEL ONLINE ne peut pas récupérer à la place de l'utilisateur** → la sécurisation de la récupération est présentée comme cruciale, sans dramatiser. La microcopie O2 est **sans jargon** (« serveur » retiré) et **sans ambiguïté** (« Vos secrets ne quittent pas votre téléphone. »).

### 9.1 Plancher de sécurité (exigences minimales, non « ouvertes »)

> Ces exigences constituent un **plancher** posé par le Design ; leur **finalisation** revient à Produit/Sécurité (Q-A), mais elles ne sont **pas rouvertes**.

- **Le code ne chiffre jamais seul le secret maître.** La dérivation qui protège le secret maître est **ancrée matériellement** (Secure Enclave / StrongBox), avec **limitation de débit matérielle**. Un code à 6 chiffres (10⁶ combinaisons) **ne doit pas** rendre le secret maître **force-brutable hors ligne** si le stockage est compromis : le code **déverrouille** une clé à support matériel, il ne sert pas de seule clé de chiffrement.
- **Rejet des codes triviaux** dès O3 : séquences (`123456`), répétitions (`000000`, `111111`), motifs évidents → **refus calme** (`onboarding.erreur.codeTrivial`), sans reproche ni blocage brutal.
- **Verrouillage progressif à l'ouverture** : après **N échecs** de saisie du code (valeur exacte à fixer par Sécurité), **temporisation croissante puis verrouillage** progressif. Ce comportement s'applique à l'**écran d'ouverture** du coffre (hors périmètre écran de cette SPEC) mais son **existence est exigée ici** car elle conditionne la valeur du code. *(À distinguer du compteur d'essais **neutre** de la confirmation O4, qui n'est pas une surface d'attaque.)*

### 9.2 Champ code & affichage

- **Champ code secret (O3, O4)** : **masqué par défaut**, bascule « Afficher/Masquer » accessible. **Pas de suggestion clavier, pas d'autocorrection, pas de mémorisation de frappe** ; clavier numérique dédié. Le **presse-papiers manuel n'est pas proposé** ; en revanche l'**enregistrement/remplissage par le gestionnaire de mots de passe système** est **autorisé** (alternative 3.3.8, §8).
- **Re-masquage automatique** : le code révélé via « Afficher » est **re-masqué automatiquement** au **passage en arrière-plan**, au **verrouillage** de l'appareil et après une **courte inactivité** ; il ne reste pas visible après mise en veille.
- **Anti-capture — formulation honnête** : sur O3/O4, la capture d'écran est **bloquée sur Android** ; sur **iOS**, le blocage strict est impossible → **détection et masquage a posteriori** (le contenu sensible est occulté et l'utilisateur averti). Le message `onboarding.erreur.captureBloquee` est **annoncé via région live assertive** (§8). *(Le blocage détaillé sur l'affichage de la phrase de récupération relève de sa SPEC.)*

### 9.3 Confiance, anti-hameçonnage, journalisation

- **Aucune fuite serveur / log** : la **création du coffre est locale** ; **aucun envoi** du code, du secret maître ni des clés à un serveur ; **aucun secret ni donnée personnelle** dans les journaux ou l'analytique (événements anonymisés, sans contenu).
- **Confirmation avant de continuer (O4)** : la ressaisie du code prévient l'erreur de mémorisation **sans** être un test cognitif bloquant (alternative autofill/biométrie disponible).
- **Garde-fou anti-écrasement** : lancer une **création** alors qu'un **coffre existe déjà** exige une **modale de confirmation** décrivant la conséquence irréversible (§3, `onboarding.ecrasement.confirmation`).
- **Pas d'urgence artificielle ni de dark pattern** : le report de la récupération (O5 « Plus tard ») est **libre**, l'avertissement est **calme et factuel** ; aucun consentement pré-coché ; la biométrie et les notifications sont **opt-in**.
- **Anti-hameçonnage — contextuel, pas seulement enfoui** : un rappel **bref et contextuel** est **surfacé au moins une fois** dans l'onboarding (O3, `onboarding.securite.antiHameconnage`), la version complète restant dans l'aide (« TEL ONLINE ne vous demandera jamais votre code ou votre phrase par message ou par appel »). Ce parcours ne demande **jamais** de secret via un canal externe.

---

## 10. Offline-first & réseau

- **Fonctionne entièrement hors ligne** : O1 (langue), O2 (explication), O3 (code), **O4 (création locale du coffre — génération des clés sans réseau)**, O5 (coffre prêt + rappel), O6 (autorisation notifications = permission système, sans réseau). Aucune étape de ce parcours **n'exige** de connexion (`HYP-7`).
- **Indicateur de connexion honnête** : si l'appareil est hors ligne, **aucun bandeau d'erreur** n'apparaît sur ce parcours (rien n'est bloqué). Un indicateur discret « hors ligne » peut rester visible, mais **ne doit pas suggérer un échec**.
- **File d'attente** : ce parcours ne met **rien** en file (pas d'action serveur). Si une analytique anonymisée est prévue, elle est **différée et facultative**, jamais bloquante, et **sans contenu secret**.
- **Relais réseau reporté à l'autre SPEC** : la sécurisation de la récupération (phrase, canal eSIM/SMS, sauvegarde chiffrée) peut nécessiter du réseau ; ce besoin et ses messages honnêtes sont traités dans [`SPECS-phrase-recuperation.md`](./SPECS-phrase-recuperation.md), **pas ici**.
- **Aucune perte de saisie** : une coupure réseau ou une fermeture d'app ne perd **jamais** d'étape ; reprise selon §3 (jalons locaux, code non persisté avant confirmation).

---

## 11. Performance

- **Budgets** (Charte §9, mobile d'entrée de gamme) : premier contenu utile ≤ 2.5 s, réponse à l'interaction ≤ 200 ms, 60 fps sur les transitions d'étape.
- **Feedback < 100 ms** : à l'appui sur « Créer mon coffre » (O4), retour visuel immédiat (« Création… ») **avant** la génération complète des clés ; la génération locale ne doit pas figer l'interface.
- **Squelettes plutôt que spinners** : O1/O2/O3 affichent des squelettes de chargement, jamais de page blanche.
- **Poids initial léger** : illustrations d'onboarding **optimisées** ; respect de `prefers-reduced-data` (visuels allégés, aucun préchargement lourd) et de `prefers-reduced-motion` (transitions désactivées).
- **Aucune dépendance réseau bloquante** : l'onboarding ne doit **pas** attendre une réponse serveur pour progresser (cohérent avec §10).
- **Génération des clés** : perçue comme quasi instantanée ; si elle dépasse ~1 s sur appareil lent, un état « Création de votre coffre… » avec progression est affiché (annoncé en région live).

---

## 12. Cas limites & erreurs

| Situation | Comportement | Message (FR, langage clair) — clé |
|---|---|---|
| Code trop court / incomplet (O3) | Primaire `aria-disabled` (focalisable) avec explication liée ; pas de blocage brutal. | `Saisissez {longueur, plural, one {# chiffre} other {# chiffres}} pour continuer.` — `onboarding.code.ctaDesactive` |
| **Code trivial** (000000, 123456, répétitions) (O3) | **Refus calme** ; on invite à en choisir un autre, sans reproche (plancher §9). | « Ce code est trop facile à deviner. Choisissez-en un autre. » — `onboarding.erreur.codeTrivial` |
| Caractère non numérique saisi | Clavier numérique empêche la saisie ; sinon filtrage silencieux. | (préventif, pas de message d'erreur) |
| Les deux codes diffèrent (O4) | Message inline relié au champ, annoncé **assertif** ; on redemande la **confirmation seule** ; **compteur neutre**. | « Les deux codes sont différents. Saisissez à nouveau le même code. » — `onboarding.erreur.codeDifferent` |
| Biométrie indisponible sur l'appareil (O3) | Option **masquée** ; on continue avec le code seul + **autofill disponible**, sans cul-de-sac. | (option simplement absente) |
| Biométrie refusée au niveau OS | On reste sur code seul ; réactivable plus tard dans les réglages. | « Vous pourrez activer l'ouverture par empreinte ou visage plus tard dans les réglages. » — `onboarding.erreur.biometrieRefusee` |
| Notifications refusées au niveau OS (O6) | Aucune pénalité ; chemin vers les réglages système. | « Vous pouvez activer les rappels plus tard dans les réglages de votre téléphone. » — `onboarding.notifs.refus` |
| App fermée avant O4 confirmé | Reprise **au début de la protection (O3)** ; code jamais conservé. | (reprise silencieuse, pas de message d'échec) |
| App fermée après création (avant récupération sécurisée) | Reprise sur O5 / entrée app avec **rappel persistant** non bloquant. | « Récupération non sécurisée — protégez votre coffre. » — `onboarding.rappel.recuperation` |
| **Création lancée alors qu'un coffre existe déjà** | **Modale de confirmation** décrivant la conséquence irréversible ; sans confirmation → redirection vers l'ouverture du coffre existant. | « Un coffre existe déjà sur cet appareil. En créer un nouveau remplacera l'actuel et effacera son contenu. Cette action est irréversible. » — `onboarding.ecrasement.confirmation` |
| Hors ligne pendant tout l'onboarding | Tout fonctionne ; **aucun** message d'erreur réseau. | (pas de message ; indicateur « hors ligne » discret seulement) |
| Échec de chargement du catalogue i18n | Repli sur la **locale supportée la plus proche disponible**, puis **EN**, puis FR ; l'onboarding continue (§6). | (repli silencieux ; **jamais** un retour forcé sur FR) |
| Tentative de capture d'écran sur O3/O4 | **Android** : bloquée. **iOS** : détectée + contenu masqué. Message **annoncé en région live assertive**. | « Pour votre sécurité, la capture d'écran est protégée sur cet écran. » — `onboarding.erreur.captureBloquee` |
| Code affiché puis app en arrière-plan / veille | Code **re-masqué automatiquement** (§9). | (comportement silencieux) |
| Report répété de la récupération | Le rappel persiste sans harceler ; jamais de blocage de l'app. | « Récupération non sécurisée — protégez votre coffre. » — `onboarding.rappel.recuperation` |

---

## 13. Critères d'acceptation (testables)

- [ ] Le **choix de langue** est accessible **dès l'écran O1** (cible ≥ 44 px, atteignable à une main) et à tout moment ensuite ; changer de langue met à jour **tous** les textes **sans redémarrage** et **pose `lang`/`dir`** sur la racine.
- [ ] **Langue initiale = langue de l'appareil** si supportée ; à défaut, repli **EN puis FR** — **jamais** un basculement forcé sur FR pour un utilisateur AR/EN. En cas d'échec de catalogue, repli sur la **locale supportée la plus proche** puis EN.
- [ ] L'onboarding se déroule en **≤ 5 écrans jusqu'à O5**, **une décision par écran**, avec **progression visible et cohérente** (« Étape n sur 3 » identique sur O3, O4 **et O5**, annoncée aux lecteurs d'écran, précédée de l'annonce « Encore 3 étapes » sur O2).
- [ ] La **création du coffre réussit hors ligne**, sans aucun appel serveur (vérifiable en mode avion).
- [ ] Le **code** est masqué par défaut, sans suggestion/autocorrection ; le **presse-papiers manuel** est évité mais le **remplissage automatique (gestionnaire de mots de passe)** est disponible **sur tous les appareils cibles** (alternative 3.3.8) ; la bascule Afficher/Masquer expose son état ; le code **se re-masque au passage en arrière-plan**.
- [ ] **Codes triviaux rejetés** (000000, 123456, répétitions) avec message calme (plancher §9).
- [ ] La **biométrie** est **optionnelle** ; **le parcours sans biométrie** (refus ou appareil sans capteur) va **jusqu'à O5** et l'**ouverture reste possible via l'alternative non cognitive** (autofill), vérifié.
- [ ] La **confirmation du code** échoue proprement si les codes diffèrent, avec message clair, reprise de la seule confirmation et **compteur d'essais neutre**.
- [ ] Un **garde-fou de confirmation** s'affiche si une **création** est lancée alors qu'un **coffre existe déjà** (conséquence irréversible décrite).
- [ ] L'écran **« coffre prêt »** propose **clairement** de sécuriser la récupération via un **lien** vers `SPECS-phrase-recuperation.md`, **sans dupliquer** ce contenu, et permet un **report** non bloquant.
- [ ] Les **notifications** ne sont **jamais** demandées au lancement ; permission sollicitée **en contexte** ; refus non bloquant ; feuille **fermable au simple appui** (2.5.7).
- [ ] La **reprise** après fermeture de l'app respecte les règles du §3 (aucune saisie utile perdue ; code non persisté avant confirmation).
- [ ] Les **7 états** applicables sont implémentés pour chaque écran (les non pertinents sont justifiés).
- [ ] **Checklist WCAG 2.2 AA** renseignée : contraste, **focus visible (2.4.7/2.4.13) ET focus non masqué (2.4.11)** par le clavier/la barre ancrée, noms accessibles, `lang`/`dir` (3.1.2), région live, cibles ≥ 44 px (y compris retour, sélecteur de langue, liens), **glissement avec alternative (2.5.7)**, **aide cohérente (3.2.6)**, mouvement réduit, authentification accessible (3.3.8).
- [ ] **Rendu et contrastes validés en thème clair ET sombre** : toute paire texte/fond ≥ 4.5:1, chaque composant ≥ 3:1, **y compris l'encart d'avertissement O5** (surface sombre dédiée) et les icônes de statut.
- [ ] **Transitions désactivées sous `prefers-reduced-motion`**, vérifié.
- [ ] **Toute chaîne** provient d'une **clé i18n**, sans concaténation, avec **pluriels ICU** (catégories complètes en AR pour `onboarding.code.aide`, `onboarding.code.ctaDesactive`, `onboarding.valeur.etapesAvenir`) et **nombres localisés** (chiffres arabes-indiens en AR pour la progression et les libellés), testée en **FR / EN / AR (RTL)** et avec **+40 %** de longueur (aucun bouton tronqué).
- [ ] **Prononciation lecteur d'écran correcte en AR** (voix/règles AR grâce à `lang=ar`), vérifiée.
- [ ] **Anti-capture** : bloquée (Android) / détectée et masquée (iOS) sur O3/O4, message **annoncé en région live assertive**.
- [ ] **Rappel anti-hameçonnage contextuel** surfacé au moins une fois dans l'onboarding.
- [ ] **Aucun secret** (code, clés, secret maître) ni donnée personnelle dans les journaux/analytique ; **le code ne chiffre pas seul le secret maître** (dérivation ancrée matériellement, limitation de débit) — vérifié au niveau exigences.
- [ ] Rendu **RTL** correct (mise en miroir, progression et flèches inversées) validé en AR.

---

## 14. Métriques de succès (KPIs UX)

| KPI | Définition | Cible indicative |
|---|---|---|
| **Taux de complétion onboarding** | % d'utilisateurs atteignant O5 « coffre prêt » après O1. | ≥ 85 % |
| **Temps jusqu'à « coffre prêt »** | Durée médiane O1 → O5 (hors lecture volontaire de l'aide). | ≤ 90 s (médiane) |
| **Taux d'erreur de confirmation du code** | % de sessions avec ≥ 1 échec « codes différents » en O4. | ≤ 15 %, sans abandon induit |
| **Taux de code trivial refusé** | % de saisies O3 rejetées comme triviales (suivi qualité, sans contenu). | Suivi, pas de cible bloquante |
| **Abandon par étape** | % de sorties à chaque écran (O1→O5) pour localiser la friction. | Aucun écran > 10 % d'abandon |
| **Sécurisation de la récupération** | % d'utilisateurs lançant la sécurisation (O5 → SPEC phrase) sous 7 jours. | ≥ 60 % (à confirmer avec l'autre SPEC) |

> Toutes les métriques sont mesurées **sans contenu secret** ni donnée personnelle (événements anonymisés, Charte §10).

---

## 15. Questions ouvertes

- `Q-A` (lie `HYP-6`) : **Finalisation de la politique de code**, dans le **respect du plancher posé au §9** (dérivation ancrée matériellement + limitation de débit ; rejet des codes triviaux ; verrouillage progressif à l'ouverture). À trancher avec Produit/Sécurité : **longueur fixe 6 vs variable ≥ 6**, alphanumérique optionnel, **valeur exacte de N** (seuils de temporisation/verrouillage). **Le plancher n'est pas rouvert** ; seule sa finalisation l'est.
- `Q-B` (lie `HYP-8`) : **Placement exact** de la sécurisation de la récupération — immédiatement après O5, ou report par défaut avec rappel ? Interface du rappel persistant (fréquence, ton).
- `Q-C` : **Politique biométrique** — la biométrie complète le code à l'ouverture (accélérateur) ; comportement si la biométrie de l'appareil change (nouvelle empreinte/visage) ? (Elle **ne remplace pas** l'alternative non cognitive garantie.)
- `Q-D` (lie **Q6** charte) : Liste **définitive des langues** de lancement et responsabilité de la traduction/relecture, notamment la relecture **AR (RTL)** et la **validation des catégories de pluriel** par locale.
- `Q-E` (lie **Q7** charte) : **Contraintes réglementaires** (RGPD, mentions légales, éventuel KYC) à afficher pendant l'onboarding et leur emplacement.
- `Q-F` (lie **Q1** charte) : Confirmation des hypothèses produit `HYP-1`, `HYP-2`, `HYP-7` sous-jacentes à ce parcours.
- `Q-G` : **Analytique d'onboarding** — quels événements anonymisés sont autorisés pour mesurer les KPIs §14 sans jamais capter de secret ?
- `Q-H` : **Token de surface d'avertissement en thème sombre** (encart O5) — définir avec le design visuel un ambre sombre + texte clair validé ≥ 4.5:1 (texte) et ≥ 3:1 (icône/bordure), la charte §11.2 ne fournissant pas ce token.

---

## 6bis (rappel wireframes — cf. §6 pour les règles i18n)

## 6. Wireframes textuels

> Actions primaires ancrées dans le **tiers inférieur** (zone du pouce), **maintenues visibles** malgré le clavier/la barre ancrée (2.4.11). Cibles ≥ 44 px (retour, sélecteur de langue et liens **inclus**). En **AR (RTL)**, toute la mise en page est **mise en miroir** (propriétés logiques début/fin) : progression, flèches et alignements suivent la lecture droite-à-gauche ; **chiffres localisés**.

**O1 — Accueil & langue**
```
┌───────────────────────────────┐
│  TEL ONLINE      [ 🌐 Langue ▾]│  ← sélecteur ≥ 44px, globe aria-hidden
│                               │
│      (illustration sobre)     │  décorative, alt=""
│                               │
│  Votre coffre numérique,      │  titre (h1)
│  à vous seul.                 │
│                               │
│  Gardez vos secrets en        │  phrase de valeur
│  sécurité, sur votre          │
│  téléphone.                   │
│                               │
│ ─── zone du pouce ─────────── │
│  [      Commencer          ]  │  ← action primaire (bas)
│  J'ai déjà un coffre          │  lien secondaire (cible ≥ 44px)
└───────────────────────────────┘
```

**O2 — Auto-conservation**
```
┌───────────────────────────────┐
│ ‹ Retour            (Aide)     │  point d'aide à emplacement constant
│                               │
│ Vos secrets restent sur       │  titre (h1)
│ votre appareil.               │
│                               │
│ 🔒  Chiffré, lisible par vous │  icône + texte (couleur non seule)
│     seul.                     │
│ 📵  Vos secrets ne quittent   │  reformulé, sans jargon ni ambiguïté
│     pas votre téléphone.      │
│ ⚠️  Personne ne peut récupérer│
│     à votre place — la        │
│     sauvegarde compte.        │
│                               │
│ Encore 3 étapes pour créer    │  annonce la progression à venir
│ votre coffre.                 │
│ ─── zone du pouce ─────────── │
│  [   Créer mon coffre      ]  │  ← action primaire
└───────────────────────────────┘
```

**O3 — Protection locale (code + biométrie)**
```
┌───────────────────────────────┐
│ ‹ Retour        Étape 1 sur 3 │  progression annoncée (nombres localisés)
│ ▓▓▓▓▓▓░░░░░░░░░░░░             │
│                               │
│ Choisissez un code            │  titre (h1)
│ Un code à 6 chiffres que      │  aide ICU pluralisée (aria-describedby)
│ vous seul connaissez.         │
│                               │
│ ⓘ TEL ONLINE ne vous          │  rappel anti-hameçonnage contextuel
│   demandera jamais ce code.   │
│                               │
│ Code                          │  libellé au-dessus
│ [ • • • • • •        ]  Afficher│ champ secret ; autofill autorisé
│                               │
│ ┌───────────────────────────┐ │
│ │ Ouvrir avec l'empreinte / │ │  option biométrie (après code valide)
│ │ le visage           [ ◯ ] │ │  bascule, non imposée
│ └───────────────────────────┘ │
│ ─── zone du pouce ─────────── │
│  [        Continuer        ]  │  aria-disabled tant que < 6 chiffres,
│                               │  explication liée et restituée
└───────────────────────────────┘
```

**O4 — Confirmation du code**
```
┌───────────────────────────────┐
│ ‹ Retour        Étape 2 sur 3 │
│ ▓▓▓▓▓▓▓▓▓▓▓░░░░░░              │
│                               │
│ Confirmez votre code          │  titre (h1)
│ Saisissez à nouveau le même   │
│ code.                         │  (remplissage automatique autorisé)
│                               │
│ Code                          │
│ [ • • • • • •        ]  Afficher│
│                               │
│ (erreur éventuelle ici,       │  message inline relié, annoncé assertif
│  reliée au champ)             │
│ ─── zone du pouce ─────────── │
│  [     Créer mon coffre    ]  │  → génération locale, hors ligne OK
└───────────────────────────────┘
```

**O5 — Coffre prêt**
```
┌───────────────────────────────┐
│                 Étape 3 sur 3 │  progression pleine, cohérente avec le flux
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓             │
│           ✓                   │  bannière succès (icône + texte)
│  Votre coffre est prêt        │  titre (h1)
│                               │
│  Pour ne jamais le perdre,    │
│  sécurisez votre récupération.│
│                               │
│ ┌───────────────────────────┐ │
│ │ ⚠️ Sans récupération, un   │ │  avertissement calme (report)
│ │ téléphone perdu = coffre   │ │  clair 14:1 / sombre : surface dédiée
│ │ perdu. Personne ne peut    │ │  (texte clair ≥ 4.5:1)
│ │ le restaurer à votre place.│ │
│ └───────────────────────────┘ │
│ ─── zone du pouce ─────────── │
│  [ Sécuriser ma récupération] │  ← primaire (lien vers SPEC phrase)
│  Plus tard                    │  secondaire (report + rappel), ≥ 44px
└───────────────────────────────┘
```

**O6 — Notifications (feuille inférieure, en contexte)**
```
┌───────────────────────────────┐
│            (contenu de l'app)  │
│                               │
│ ╔═══════════════════════════╗ │
│ ║  ═══ (poignée)      [ ✕ ] ║ │  fermeture au simple appui (2.5.7) ≥44px
│ ║ Rester informé            ║ │  bottom sheet, focus déplacé dedans
│ ║ Recevez un rappel pour    ║ │  bénéfice concret
│ ║ sécuriser votre           ║ │
│ ║ récupération, et des      ║ │
│ ║ alertes de sécurité.      ║ │
│ ║                           ║ │
│ ║ [   Activer les rappels ] ║ │  primaire → demande système OS
│ ║ Pas maintenant            ║ │  secondaire, sans pénalité
│ ╚═══════════════════════════╝ │
└───────────────────────────────┘
```
