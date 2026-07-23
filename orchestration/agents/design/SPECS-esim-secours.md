# SPEC — Carte SIM numérique de secours

## 0. Métadonnées

| Champ | Valeur |
|---|---|
| **Titre** | Carte SIM numérique de secours — activation, états réseau et gestion |
| **Statut** | **v0.2 — proposition corrigée (post-audit)** (pour revue en atelier) |
| **Identifiant mission** | `esim-secours` |
| **Écrans couverts** | E1 Explication · E2 Vérification de compatibilité · E3 Activation/installation guidée · E4 État réseau (bascule, indicateur, quota) · E5 Usage pendant une récupération · E6 Gestion (désactivation, quota épuisé, erreurs) |
| **Charte de référence** | `orchestration/agents/design/CHARTE-UX.md` (v0.1). En cas de conflit, **la charte fait foi**. |
| **SPECS liées** | `SPECS-recuperation-zero-perte.md` (usage du canal de secours, presse-papiers **et protection anti-capture d'un code affiché**), `SPECS-abonnement-freemium.md` (quota gratuit vs premium), `SPECS-onboarding-coffre.md` (point d'entrée proactif) |
| **Terme imposé à l'écran** | **« carte SIM numérique de secours »** — **jamais** « eSIM », « profil » ou « provisioning » (glossaire charte §16). **« connexion de secours »** est autorisé **uniquement** comme libellé fonctionnel de l'état réseau (sous-titre / indicateur), tracé comme tel (voir §13). |
| **Hypothèses produit utilisées** | `HYP-1` (coffre auto-conservé), `HYP-3` (récupération « zéro perte » multi-facteurs), `HYP-4` (carte SIM numérique = connectivité minimale garantie + canal de récupération indépendant du réseau habituel), `HYP-5` (modèle freemium : quota de volume gratuit + option premium) |
| **Questions produit ouvertes bloquantes** | `Q4` (incluse au gratuit ou strictement premium ?), `Q5` (volume gratuit vs premium + règle de renouvellement), `Q7` (contraintes réglementaires opérateur/KYC) |

---

## 1. Objectif & enjeux

### 1.1 Rôle du parcours
La carte SIM numérique de secours est un **filet de connectivité de dernier recours** (`HYP-4`). Elle a deux fonctions :

1. **Rester joignable / connecté** quand le réseau habituel (carte SIM physique, Wi-Fi) est absent, saturé ou coupé.
2. **Fournir un canal de récupération indépendant** : si l'utilisateur perd l'accès à son coffre, ce canal permet de recevoir un code ou de relancer une récupération **sans dépendre de l'opérateur habituel** (`HYP-3`, lien avec `SPECS-recuperation-zero-perte.md`).

Ce parcours couvre : comprendre à quoi ça sert, vérifier que l'appareil est compatible, installer/activer la carte, comprendre l'état du réseau au quotidien, s'en servir pendant une récupération, et la gérer (désactiver, gérer le volume, corriger une erreur).

### 1.2 Pourquoi ce parcours est SENSIBLE
- **Identité & joignabilité :** ce canal peut servir à **prouver que c'est bien l'utilisateur** lors d'une récupération. Une usurpation ou une désactivation malveillante affaiblit tout le dispositif « zéro perte ».
- **Argent :** le **volume de données** de secours peut être **payant** au-delà d'un quota (`HYP-5`, `Q4`/`Q5`). Toute consommation doit être **honnête, prévisible et sans surprise de facturation**. **Deux garde-fous sont posés en dur** (voir §5 E4, §9) : le **mode de bascule par défaut est Manuel** ; le mode **Automatique** n'engage **aucune consommation payante sans confirmation** (seuil).
- **Disponibilité du canal de contrôle (invariant HYP-4) :** la **réception d'un code de récupération** par ce canal reste **toujours possible, même à quota nul**. Ce canal de contrôle est **prioritaire et hors quota** : il ne dépend ni du volume restant ni d'une consommation payante (voir §9, §12, §13).
- **Secrets (indirect) :** le canal participe à l'accès au coffre auto-conservé (`HYP-1`). Sa désactivation est une **action à conséquence** qui doit être expliquée et confirmée. Un **code de secours** peut transiter/s'afficher pendant une récupération (E5) → **écran protégé** (anti-capture + masquage par défaut, §9).
- **Paradoxe critique de disponibilité :** on a le plus besoin de ce secours **précisément quand on n'a pas de réseau** — or l'installation initiale peut, elle, exiger une connexion. Ce paradoxe est traité explicitement (§4.0 et §10).

---

## 2. Personas & cas d'usage concernés

Personas de la charte §3. Priorité décroissante pour ce parcours :

| Persona | Pourquoi ce parcours la/le concerne | Exigence dominante |
|---|---|---|
| **Léa — nomade connectée** | Cas d'usage central : voyage, réseau instable, coupures. C'est **la** persona cible de la carte de secours. | Fonctionne hors ligne ; bascule fiable ; volume/quota lisible ; installation **proactive** avant le besoin. |
| **Robert — prudent (71 ans)** | Sujet perçu comme « technique » et donc anxiogène. Peur de « tout casser » ou de payer sans le vouloir. | Langage clair (FALC, sans « data »), réassurance, cibles larges, coût affiché sans ambiguïté, **jamais de facturation sans confirmation**, désactivation réversible expliquée. |
| **Amina — multilingue (FR/AR-RTL)** | Nom d'opérateur, volumes et unités varient selon la locale et le sens de lecture. | Formats volume/quota localisés, **accord/pluriel piloté par la valeur**, RTL correct, aucune concaténation. |
| **Marc — déficient visuel (lecteur d'écran)** | L'**état réseau** (connecté au secours / volume restant / bascule) est une information dynamique à annoncer. | Régions *live*, indicateur non porté par la seule couleur, focus géré, mode de bascule = **groupe de boutons radio** correctement annoncé. |
| **Camille — pragmatique** | Veut activer vite et oublier. | Peu d'étapes, valeur montrée d'emblée, feedback immédiat. |

**Règle de validation (charte §3) :** l'écran est validé quand il marche pour **Robert, Amina et Marc**, pas seulement pour Léa/Camille.

---

## 3. Pré-requis, points d'entrée et de sortie

### 3.1 Pré-requis
- Appareil physiquement compatible avec une carte SIM numérique (vérifié en E2).
- Coffre créé / session active (`SPECS-onboarding-coffre.md`).
- Statut d'offre connu (gratuit / premium) pour afficher le bon quota — **dépend de `Q4`/`Q5`**, renvoi à `SPECS-abonnement-freemium.md`.
- **Une connexion disponible au moins une fois** pour l'installation (Wi-Fi ou réseau mobile). Si absente : parcours d'alternatives (§10).

### 3.2 Points d'entrée
1. **Proactif (recommandé, charte §14 « valeur avant effort ») :** proposé pendant l'onboarding, **pendant que l'utilisateur a encore du réseau**. Message-clé : « installez-la maintenant pour être protégé plus tard ».
2. **Depuis « Récupération zéro perte » :** présentée comme un facteur à activer (`SPECS-recuperation-zero-perte.md`).
3. **Depuis les Réglages > Carte SIM numérique de secours** (état affiché : « connexion de secours ») : entrée permanente pour installer/gérer.
4. **Contextuel :** bannière lors d'une **perte de réseau prolongée** proposant d'activer le secours (si déjà installé).

### 3.3 Points de sortie
- **Succès :** carte installée et prête (E4 en état « prête, en veille »).
- **Report :** l'utilisateur remet à plus tard → rappel non intrusif + risque expliqué (charte §14).
- **Abandon compatibilité :** appareil non compatible → alternatives de secours proposées (autre canal de récupération), renvoi `SPECS-recuperation-zero-perte.md`.
- **Gestion :** retour aux Réglages après désactivation / recharge de volume.

### 3.4 Reprise après interruption (charte §2 pt.5)
Tout état est **persisté localement**. Une coupure réseau, un appel entrant ou une fermeture d'app **ne perd jamais l'étape** : on reprend E3 exactement où on en était (« Reprendre l'installation »). Aucune information déjà fournie n'est redemandée (WCAG 3.3.7).

---

## 4. Parcours utilisateur (flux)

### 4.0 Traitement explicite du paradoxe offline (transversal)
> **Le paradoxe :** la carte de secours sert quand il n'y a pas de réseau, mais son **installation initiale** a besoin d'une connexion une seule fois.

Réponse UX, en trois lignes de défense :

1. **Anticipation (défaut) :** on pousse l'installation **tôt et en ligne** (onboarding), avec un message honnête : « À installer une fois, pendant que vous avez du réseau. Elle vous protégera plus tard, même sans réseau. »
2. **Alternatives si hors ligne au moment d'installer** (§10) : se connecter à un **Wi-Fi**, utiliser le **réseau mobile habituel** le temps de l'installation, ou **reprendre plus tard** avec rappel. On n'affiche jamais un cul-de-sac.
3. **Séparation installation / utilisation :** une fois installée, l'**activation d'urgence** (bascule vers le secours) fonctionne **sans nouvelle connexion**. Ce point est répété à l'utilisateur pour le rassurer.

### 4.1 Étapes numérotées

1. **Entrée** (proactif / réglages / récupération) → **E1 Explication**.
   - 1a. « Pas maintenant » → sortie « report » + rappel.
2. **E1 → « Vérifier mon téléphone »** → **E2 Vérification de compatibilité**.
3. **E2 :**
   - 3a. **Compatible** → **E3 Activation**.
   - 3b. **Non compatible** → écran « alternatives » (autre canal de secours) → renvoi `SPECS-recuperation-zero-perte.md`. Retour arrière possible vers E1.
   - 3c. **Indéterminé (hors ligne / info manquante)** → message honnête + « Réessayer » ou « Continuer quand même » avec avertissement.
4. **E3 Activation guidée** (permissions **en contexte**, charte §14) :
   - 4a. Demande de la permission réseau/installation **au moment utile**, avec explication.
   - 4b. **Si hors ligne →** branchement §10 (alternatives), sans perte d'état.
   - 4c. **Erreur d'activation →** E6 (message clair + reprise).
   - 4d. **Succès →** confirmation + **E4**.
5. **E4 État réseau** (nominal, permanent dans Réglages) : indicateur de connexion honnête, mode de bascule **Manuel (défaut) / Automatique**, **volume restant**.
   - 5a. Basculer manuellement vers le secours / revenir au réseau habituel.
   - 5b. Ouvrir la gestion → **E6**.
6. **E5 Usage pendant une récupération :** déclenché depuis `SPECS-recuperation-zero-perte.md` ; la carte fournit le canal indépendant. **Écran protégé** (anti-capture + masquage par défaut du code). Retour au flux de récupération une fois le code reçu / la connexion établie.
7. **E6 Gestion :** désactiver (confirmation + ré-authentification), quota épuisé (recharge → `SPECS-abonnement-freemium.md`), corriger une erreur d'activation.

**Retours arrière :** disponibles à chaque écran sauf pendant l'écriture atomique de l'installation (E3, étape « installation en cours ») où l'on propose « Annuler » qui nettoie proprement sans état corrompu.

---

## 5. Écrans & états

> Convention : **une seule action primaire** par écran (charte §12). Les 7 états (charte §13) sont traités pour chaque écran clé ; leur absence est justifiée.

### E1 — Explication (langage clair)
- **But :** faire comprendre en un coup d'œil *à quoi ça sert* et *pourquoi c'est utile*, avant tout effort (charte §14).
- **Éléments :** titre ; 2 bénéfices illustrés par icône + libellé (« Rester connecté sans réseau habituel » / « Un moyen de récupérer votre compte indépendant ») ; 1 phrase sur le coût (« Un volume internet offert, puis rechargeable » — renvoi offre) ; note honnête « À installer une fois, tant que vous avez du réseau ».
- **Action primaire :** « Vérifier mon téléphone ».
- **Actions secondaires :** « En savoir plus » (aide, dispo hors ligne) ; « Pas maintenant » (zone tactile ≥ 44 px, §8).
- **États :**
  - *Contenu :* nominal.
  - *Chargement :* squelette si le statut d'offre (gratuit/premium) est en cours de lecture.
  - *Vide :* N/A (écran purement explicatif — justifié).
  - *Erreur :* si le coût/volume ne se charge pas → afficher le bénéfice sans le chiffre + « Détails du volume indisponibles pour l'instant ».
  - *Hors ligne :* l'écran s'affiche **entièrement** (contenu local) ; encart honnête « Vous êtes hors ligne. L'installation demandera une connexion — voici comment faire » (renvoi §10).
  - *Succès :* N/A.
  - *Permission requise :* aucune à ce stade (différée en E3).

### E2 — Vérification de compatibilité de l'appareil
- **But :** confirmer que l'appareil peut recevoir une carte SIM numérique, **avant** d'engager l'installation.
- **Éléments :** libellé de l'appareil, indicateur de résultat (icône + texte, pas la couleur seule), explication du résultat.
- **Action primaire :** « Continuer l'installation » (si compatible) / « Voir les autres solutions » (si non).
- **Actions secondaires :** « Réessayer la vérification » ; « Aide » (zones tactiles ≥ 44 px, §8).
- **États :**
  - *Chargement :* squelette « Vérification en cours… » + annonce *live* polie.
  - *Contenu :* résultat compatible (icône ✔ + « Votre téléphone est compatible »).
  - *Vide :* N/A.
  - *Erreur / Indéterminé :* « Impossible de vérifier pour l'instant » + causes probables + « Réessayer ». Option « Continuer quand même » avec avertissement clair.
  - *Hors ligne :* si la vérification requiert le réseau, l'indiquer honnêtement et proposer §10 ; sinon vérifier en local.
  - *Succès :* transition vers E3.
  - *Permission requise :* N/A.
  - *Cas non compatible :* écran dédié non bloquant → « Votre téléphone ne peut pas recevoir cette carte. Vous pouvez utiliser un autre moyen de secours. » → renvoi `SPECS-recuperation-zero-perte.md`.

### E3 — Activation / installation guidée
- **But :** installer et activer la carte, en demandant les permissions **en contexte** avec explication (charte §14).
- **Éléments :** barre de progression « Étape X sur N » (annoncée) ; explication de chaque permission *avant* de la demander ; état d'avancement honnête ; rappel anti-hameçonnage (« TEL ONLINE ne vous demandera jamais votre phrase de récupération pour installer cette carte »).
- **Action primaire :** « Installer maintenant » puis, par étape, l'action attendue (« Autoriser », « Terminer »).
- **Actions secondaires :** « Faire plus tard » (report, état sauvegardé) ; « Aide » (zones tactiles ≥ 44 px).
- **États :**
  - *Chargement :* squelette + « Installation en cours… » (jamais page blanche) ; feedback < 100 ms au tap.
  - *Contenu :* étapes guidées.
  - *Vide :* N/A.
  - *Erreur :* échec d'activation → message clair (cause + solution) + « Réessayer » + « Voir les autres solutions » ; **aucun code technique brut** (charte §7). Renvoi E6 pour les cas persistants.
  - *Hors ligne :* **branche paradoxe** → « L'installation a besoin d'une connexion, une seule fois. » + alternatives (§10) + « Reprendre quand j'aurai du réseau » (état persisté).
  - *Succès :* « Votre carte SIM numérique de secours est prête. » + prochaine étape (revenir à la récupération / voir l'état) ; annonce *live*.
  - *Permission requise :* écran/feuille expliquant **pourquoi** (installer la carte, gérer la connexion de secours), demandée **au moment utile** ; si refusée → chemin de repli expliqué, jamais de blocage sec.

### E4 — État réseau (bascule, indicateur, volume) — écran nominal permanent
- **But :** donner à tout moment un état **honnête** de la connexion de secours et du volume, et permettre la bascule **sans risque de facturation surprise**.
- **Titre :** « Carte SIM numérique de secours » (terme imposé) ; **sous-titre** fonctionnel « Connexion de secours ».
- **Éléments :**
  - **Indicateur de connexion honnête** : « Réseau habituel » / « Secours actif » / « Hors ligne » (icône + libellé + éventuel motif, jamais la couleur seule — WCAG 1.4.1). Rendu sur `surface-alt`/blanc + `border-strong`, **sans fond porteur de sens** (§8).
  - **Mode de bascule** : présenté comme un **groupe de boutons radio (contrôle segmenté)** intitulé « Mode de bascule », avec **deux options nommées** :
    - **Manuel** — **option par défaut (présélectionnée)** : l'utilisateur décide de chaque bascule ; aucune consommation involontaire.
    - **Automatique** — bascule seule quand le réseau habituel manque, **mais** n'engage **aucune consommation de volume payant sans confirmation** : un **seuil de confirmation** s'applique (« Au-delà de {seuil}, vous confirmez avant de continuer »). En deçà du volume offert (gratuit), la bascule auto ne facture rien.
    - Texte d'aide sous le groupe : « Par défaut, vous décidez quand basculer. »
    - > Ce sélecteur n'est **pas** un interrupteur on/off (éviterait une annonce trompeuse « activé/désactivé »).
  - **Volume restant** : volume restant formaté selon la locale + part du total + **date de renouvellement absolue localisée** ; distinction **offert (gratuit)** vs **rechargé (premium)** — renvoi `SPECS-abonnement-freemium.md`.
- **Action primaire (unique) :** contextuelle — « Basculer sur le secours » (si réseau habituel présent et mode manuel) **ou** « Revenir au réseau habituel » (si secours actif).
- **Actions secondaires (non primaires visuellement — liens/boutons tertiaires) :** « Recharger le volume » (→ offre, **présenté en action tertiaire/lien**, pas en bouton plein) ; « Gérer » (→ E6) ; changement de mode via le groupe radio ci-dessus. Toutes zones tactiles ≥ 44 px.
- **États :**
  - *Chargement :* squelette des tuiles (indicateur, volume).
  - *Contenu :* état nominal (3 sous-états : réseau habituel / secours actif / veille).
  - *Vide :* carte non installée → renvoi E1 (« Installer la carte de secours »), pas de cul-de-sac.
  - *Erreur :* volume illisible / statut inconnu → « Impossible de lire le volume restant. La connexion de secours reste utilisable. » + « Réessayer ».
  - *Hors ligne :* l'écran reste consultable (données locales) ; l'indicateur affiche « Hors ligne » honnêtement ; la bascule vers le secours reste possible **sans nouvelle connexion**.
  - *Succès :* après bascule → bannière « Vous êtes maintenant sur la connexion de secours. » (annonce *live*).
  - *Permission requise :* N/A (déjà accordée en E3).

### E5 — Usage pendant une récupération
- **But :** fournir le **canal indépendant** pendant une récupération « zéro perte ». **Cet écran ne duplique pas** `SPECS-recuperation-zero-perte.md` ; il en est un composant appelé.
- **Protection écran (charte §10) :** un **code de secours** peut transiter/s'afficher ici → **capture d'écran bloquée** avec **message accessible** expliquant pourquoi ; **code masqué par défaut** (bouton « Afficher ») ; l'écran **hérite** des protections de `SPECS-recuperation-zero-perte.md` (presse-papiers, effacement, absence de trace en clair).
- **Éléments :** rappel du contexte (« Nous utilisons votre connexion de secours pour continuer la récupération, même sans votre réseau habituel ») ; état de réception (attente du code / connexion établie), **code masqué par défaut** ; mention « capture d'écran désactivée pour votre sécurité » ; rappel anti-hameçonnage.
- **Action primaire :** « Continuer la récupération » (retour au flux appelant).
- **Actions secondaires :** « Utiliser un autre moyen » (→ autres facteurs, SPEC zéro-perte) ; « Aide ».
- **États :**
  - *Chargement :* « Connexion de secours en cours d'activation… » (*live* poli).
  - *Contenu :* canal actif, en attente d'action de récupération (code masqué par défaut).
  - *Vide :* N/A (toujours appelé avec un contexte).
  - *Erreur :* canal indisponible → bascule vers un autre facteur de récupération (renvoi SPEC zéro-perte), jamais de blocage.
  - *Hors ligne :* si aucun réseau du tout n'est captable → message honnête + alternatives (§10) + autres facteurs de récupération. **La réception d'un code de récupération reste prioritaire et hors quota** (invariant §1.2) : un quota nul ne bloque **jamais** la réception d'un code.
  - *Succès :* canal établi → retour au flux de récupération.
  - *Permission requise :* si non installée en amont → renvoi E1/E3 avec préservation du contexte de récupération.

### E6 — Gestion (désactivation, quota épuisé, erreurs d'activation)
- **But :** gérer le cycle de vie : désactiver, comprendre/recharger un quota épuisé, corriger une activation.
- **Éléments :** statut, volume, historique d'usage synthétique (sans donnée sensible), actions.
- **Action primaire :** dépend du sous-cas (voir ci-dessous). Un seul bouton primaire à la fois.
- **Sous-cas & actions :**
  - **Désactivation :** action à conséquence → **modale de confirmation** décrivant la conséquence (« Vous ne pourrez plus recevoir de code de secours par ce canal tant qu'elle est désactivée »), bouton destructif à la **fin** de lecture (jamais par défaut), + **ré-authentification accessible** (biométrie/PIN/gestionnaire, WCAG 3.3.8). Réversible : « Vous pourrez la réactiver plus tard. »
  - **Quota épuisé :** bannière honnête « Volume de secours épuisé » + ce qui reste possible + « Recharger » → `SPECS-abonnement-freemium.md`. **Invariant (HYP-4) :** même volume épuisé, **la réception d'un code de récupération reste possible** (canal de contrôle prioritaire, hors quota).
  - **Erreur d'activation persistante :** cause probable + « Réessayer » + « Voir les autres solutions » + contact aide.
- **États :**
  - *Chargement :* squelette.
  - *Contenu :* nominal (carte active, volume, actions).
  - *Vide :* aucune carte → renvoi E1.
  - *Erreur :* action de gestion échouée → message clair + reprise ; mise en file si hors ligne (§10).
  - *Hors ligne :* consultation possible ; désactivation mise en file avec mention « sera appliquée dès le retour du réseau » (et avertissement que le canal reste actif jusque-là).
  - *Succès :* confirmation de l'action (désactivée / rechargée) + prochaine étape.
  - *Permission requise :* ré-authentification pour désactiver (voir ci-dessus).

---

## 6. Wireframes textuels

> Portrait mobile, **zone du pouce respectée** : action primaire ancrée dans le **tiers inférieur** (charte §4). Descriptions textuelles, aucun code. Les valeurs affichées (volumes, dates) sont des **exemples formatés par la locale**, jamais des chaînes en dur.

**E1 — Explication**
```
┌───────────────────────────────┐
│ ‹ Retour            [Aide]     │  (haut : navigation secondaire)
│                               │
│  Carte SIM numérique          │  Titre (h1)
│  de secours                   │
│                               │
│  [icône] Rester connecté même │  Bénéfice 1 (icône + texte)
│          sans votre réseau    │
│  [icône] Un moyen de récupérer│  Bénéfice 2
│          votre compte à part  │
│                               │
│  ⓘ Un volume internet offert, │  Note coût (renvoi offre)
│    puis rechargeable          │
│  ⓘ À installer une fois, tant │  Note honnêteté / paradoxe
│    que vous avez du réseau     │
│                               │
│ ── zone du pouce ──────────── │
│  [ Vérifier mon téléphone ]   │  ● Action primaire (bas)
│   Pas maintenant              │  ○ Secondaire (lien ≥ 44px)
└───────────────────────────────┘
```

**E2 — Vérification de compatibilité**
```
┌───────────────────────────────┐
│ ‹ Retour            [Aide]     │
│  Votre téléphone est-il        │  Titre
│  compatible ?                  │
│                               │
│  [✔ icône]  Compatible         │  Résultat = icône + libellé
│  Votre téléphone peut recevoir │  (couleur JAMAIS seule)
│  cette carte.                  │
│                               │
│ ── zone du pouce ──────────── │
│  [ Continuer l'installation ] │  ● Primaire
│   Réessayer la vérification   │  ○ Secondaire (lien ≥ 44px)
└───────────────────────────────┘
   (variante non compatible : icône + « Voir les autres solutions »)
```

**E3 — Activation guidée (avec permission en contexte)**
```
┌───────────────────────────────┐
│ ‹ Retour        Étape 2 sur 3  │  Progression annoncée
│  Autoriser l'installation      │
│                               │
│  Pourquoi : pour installer la  │  Explication AVANT la demande
│  carte et gérer la connexion   │
│  de secours.                   │
│  🔒 Nous ne demandons jamais   │  Rappel anti-hameçonnage
│  votre phrase de récupération. │
│                               │
│  [barre de progression ▓▓▓░░] │  État honnête
│                               │
│ ── zone du pouce ──────────── │
│  [ Autoriser ]                │  ● Primaire
│   Faire plus tard             │  ○ Report (état sauvegardé)
└───────────────────────────────┘
   (variante hors ligne : encart « Besoin d'une connexion, une fois »
    + [ Voir comment faire ] → alternatives §10)
```

**E4 — État réseau (nominal)**
```
┌───────────────────────────────┐
│  Carte SIM numérique           │  Titre (terme imposé)
│  de secours                    │
│  Connexion de secours          │  Sous-titre (libellé d'état)
│                               │
│  ┌── État ──────────────────┐  │
│  │ [icône] Secours actif    │  │  Icône + libellé + (motif)
│  └──────────────────────────┘  │  (couleur JAMAIS seule)
│  ┌── Volume restant ────────┐  │
│  │ 1,2 Go sur 2 Go          │  │  Formaté par la locale (ICU)
│  │ Renouvelé le {date}      │  │  Date absolue localisée
│  │ [icône] Offert           │  │  Puce : icône + texte
│  │  Recharger le volume →    │  │  Action tertiaire (lien)
│  └──────────────────────────┘  │
│                               │
│  Mode de bascule               │  Libellé de groupe (radiogroup)
│  (•) Manuel — par défaut       │  Option présélectionnée
│  ( ) Automatique               │  (confirmation si volume payant)
│  Par défaut, vous décidez      │  Aide sous le groupe
│  quand basculer.               │
│                               │
│ ── zone du pouce ──────────── │
│  [ Revenir au réseau habituel]│  ● Action primaire (unique)
│   Gérer                       │  ○ Lien → E6 (≥ 44px)
└───────────────────────────────┘
```

**E6 — Gestion / Désactivation (modale de confirmation)**
```
┌───────────────────────────────┐
│         Désactiver la carte ?  │  Titre de la modale
│                               │
│  Vous ne pourrez plus recevoir │  Conséquence décrite
│  de code de secours par ce     │
│  canal tant qu'elle est        │
│  désactivée.                   │
│  Vous pourrez la réactiver     │  Réversibilité rassurante
│  plus tard.                    │
│                               │
│  🔒 Confirmez votre identité   │  Ré-auth accessible (3.3.8)
│                               │
│ ── zone du pouce ──────────── │
│   Annuler        [ Désactiver ]│  Destructif à la FIN, non défaut
└───────────────────────────────┘
```

---

## 7. Microcopie (FR)

> Aucune concaténation. Pluriels, **accords** et volumes via **format ICU piloté par la valeur**. Les **unités et volumes** sont **formatés par la locale** (FR « Mo/Go » avec virgule décimale ; EN « MB/GB » avec point), jamais assemblés à la main. Les **dates** sont **absolues et formatées par la locale** (ordinal FR « 1er »). Le **nom d'opérateur** est une variable injectée localisée. On évite « data » au profit de « volume internet » / « volume » (charte §2, §7).

| Élément | Clé i18n | Texte FR | Note i18n / traduction |
|---|---|---|---|
| Titre E1 | `simSecours.intro.titre` | Carte SIM numérique de secours | Terme imposé ; prévoir **+40 %** d'expansion (ES/FR longs) ; ne jamais tronquer sur bouton. |
| Bénéfice 1 | `simSecours.intro.benefice.connecte` | Rester connecté même sans votre réseau habituel | Voix active, FALC. |
| Bénéfice 2 | `simSecours.intro.benefice.recuperation` | Un moyen de récupérer votre compte, indépendant de votre réseau | « récupérer votre compte », pas « recovery flow ». |
| Note coût | `simSecours.intro.note.volume` | Un volume internet offert, puis rechargeable | Renvoi offre ; on dit « volume internet », **jamais « data »** (charte §2/§7). |
| Note paradoxe | `simSecours.intro.note.horsLigne` | À installer une fois, tant que vous avez du réseau | Message clé du paradoxe offline. |
| CTA E1 | `simSecours.intro.cta` | Vérifier mon téléphone | Verbe d'action. |
| Report | `simSecours.commun.plusTard` | Pas maintenant | Réutilisée ; jamais de honte ; zone ≥ 44 px. |
| Titre E2 | `simSecours.compat.titre` | Votre téléphone est-il compatible ? | — |
| Résultat OK | `simSecours.compat.ok` | Votre téléphone peut recevoir cette carte. | Doublé d'une icône (1.4.1). |
| Résultat KO | `simSecours.compat.ko` | Votre téléphone ne peut pas recevoir cette carte. Vous pouvez utiliser un autre moyen de secours. | Pas de cul-de-sac ; renvoi SPEC zéro-perte. |
| Résultat indéterminé | `simSecours.compat.inconnu` | Impossible de vérifier pour l'instant. | + action « Réessayer ». |
| CTA compat | `simSecours.compat.continuer` | Continuer l'installation | — |
| Alternatives | `simSecours.compat.autresSolutions` | Voir les autres solutions | Lien vers facteurs de récupération. |
| Progression | `simSecours.activation.etape` | Étape {n} sur {total} | ICU nombres ; annoncée aux lecteurs d'écran. |
| Explication permission | `simSecours.activation.permission.pourquoi` | Pour installer la carte et gérer la connexion de secours. | Affichée AVANT la demande (§14). |
| Anti-hameçonnage | `simSecours.commun.antiHameconnage` | Nous ne vous demanderons jamais votre phrase de récupération pour installer cette carte. | Reprise du principe charte §10. |
| CTA installer | `simSecours.activation.installer` | Installer maintenant | — |
| CTA autoriser | `simSecours.activation.autoriser` | Autoriser | Nom accessible = texte visible (2.5.3). |
| Installation en cours | `simSecours.activation.enCours` | Installation en cours… | Région *live* polie. |
| Succès activation | `simSecours.activation.succes` | Votre carte SIM numérique de secours est prête. | *Live* ; propose la suite. |
| Erreur activation | `simSecours.activation.erreur` | L'installation n'a pas abouti. Vérifiez votre connexion, puis réessayez. | Cause + solution ; aucun code brut. |
| Branche hors ligne | `simSecours.activation.horsLigne` | L'installation a besoin d'une connexion, une seule fois. Voici comment faire. | Traite le paradoxe ; mène à §10. |
| Alt. Wi-Fi | `simSecours.horsLigne.wifi` | Connectez-vous à un réseau Wi-Fi, puis reprenez. | Alternative 1. |
| Alt. réseau habituel | `simSecours.horsLigne.reseauHabituel` | Utilisez votre réseau mobile habituel le temps de l'installation. | Alternative 2. |
| Alt. reprendre | `simSecours.horsLigne.reprendre` | Reprendre quand j'aurai du réseau | État sauvegardé. |
| Titre E4 | `simSecours.etat.titre` | Carte SIM numérique de secours | Terme imposé (titre d'écran). |
| Sous-titre E4 | `simSecours.etat.sousTitre` | Connexion de secours | Libellé fonctionnel de l'état réseau (autorisé, tracé §13). |
| Indicateur — réseau habituel | `simSecours.etat.reseauHabituel` | Réseau habituel | Icône + libellé (jamais couleur seule). |
| Indicateur — secours | `simSecours.etat.secoursActif` | Secours actif | — |
| Indicateur — hors ligne | `simSecours.etat.horsLigne` | Hors ligne | Honnête. |
| Volume restant | `simSecours.quota.restant` | `{value, plural, one {# {unite} restant} other {# {unite} restants}} sur {total}` | **ICU piloté par la valeur** : `value` = volume restant (FR : 0 ≤ value < 2 → « restant » ; ≥ 2 → « restants ») ; `unite` = unité localisée (Go/Mo ; GB/MB) ; `total` = volume total pré-formaté par la locale. Testé FR/AR pour **value = 0**, **value < 2**, **value ≥ 2**. Pas de concaténation. |
| Volume — renouvellement | `simSecours.quota.renouvellement` | Renouvelé le {date} | `{date}` = **date absolue formatée par la locale** (ordinal FR « 1er août ») ; **format unique** (jamais de relatif ni de date en dur). Règle de renouvellement selon `Q5`. |
| Quota épuisé | `simSecours.quota.epuise` | Volume de secours épuisé. | + action recharger ; réception d'un code reste possible (invariant). |
| Recharger | `simSecours.quota.recharger` | Recharger le volume | → `SPECS-abonnement-freemium.md` ; **action tertiaire/lien**, pas bouton primaire. |
| Étiquette offert | `simSecours.quota.offert` | Offert | Puce icône + texte ; sens jamais porté par la couleur seule. |
| Étiquette rechargé | `simSecours.quota.premium` | Rechargé | Puce icône + texte. |
| Libellé groupe bascule | `simSecours.bascule.mode` | Mode de bascule | **Libellé de groupe** (radiogroup), pas un interrupteur. |
| Mode manuel | `simSecours.bascule.manuel` | Manuel | **Option par défaut (présélectionnée)** ; l'utilisateur décide. |
| Mode auto | `simSecours.bascule.auto` | Automatique | Bascule seule si le réseau manque ; **jamais de consommation payante sans confirmation**. |
| Aide groupe bascule | `simSecours.bascule.aide` | Par défaut, vous décidez quand basculer. | Texte d'aide sous le groupe. |
| Seuil de confirmation | `simSecours.bascule.seuilConfirmation` | Au-delà de {seuil}, nous vous demanderons de confirmer avant de continuer. | Garde-fou anti-facturation surprise (mode Auto) ; `{seuil}` = volume formaté locale ; **valeur X = QS-1**. |
| Basculer vers secours | `simSecours.bascule.versSecours` | Basculer sur le secours | Contextuel. |
| Revenir réseau habituel | `simSecours.bascule.retour` | Revenir au réseau habituel | Contextuel. |
| Succès bascule | `simSecours.bascule.succes` | Vous êtes maintenant sur la connexion de secours. | *Live*. |
| E5 contexte | `simSecours.recup.contexte` | Nous utilisons votre connexion de secours pour continuer la récupération, même sans votre réseau habituel. | Ne duplique pas la SPEC zéro-perte. |
| E5 capture bloquée | `simSecours.recup.captureBloquee` | Pour votre sécurité, la capture d'écran est désactivée sur cet écran. | **Message accessible** ; écran pouvant afficher un code (charte §10) ; code masqué par défaut ; règle détaillée dans `SPECS-recuperation-zero-perte.md`. |
| E5 continuer | `simSecours.recup.continuer` | Continuer la récupération | Retour au flux appelant. |
| Désactiver — titre | `simSecours.desactiver.titre` | Désactiver la carte ? | Modale. |
| Désactiver — conséquence | `simSecours.desactiver.consequence` | Vous ne pourrez plus recevoir de code de secours par ce canal tant qu'elle est désactivée. | Décrit la conséquence (§10). |
| Désactiver — réversible | `simSecours.desactiver.reversible` | Vous pourrez la réactiver plus tard. | Rassure. |
| Ré-auth | `simSecours.desactiver.confirmerIdentite` | Confirmez votre identité | 3.3.8 ; biométrie/PIN/gestionnaire acceptés. |
| Désactiver — confirmer | `simSecours.desactiver.confirmer` | Désactiver | Destructif ; à la fin, jamais par défaut. |
| Annuler | `simSecours.commun.annuler` | Annuler | — |
| Mise en file hors ligne | `simSecours.commun.enFile` | Pas de connexion. Votre demande sera envoyée dès le retour du réseau. | Message réseau honnête (charte §8). |

---

## 8. Accessibilité — WCAG 2.2 AA (spécifique aux écrans)

| Critère (n°) | Comment il est satisfait ici |
|---|---|
| **Contraste texte — thème clair (1.4.3)** | Tous les textes utilisent `color.ink` (16.9:1) / `color.ink-muted` (5.9:1) sur fond clair, valeurs validées charte §11.1. Volumes/quota en `color.ink` sur `surface-alt` (#F5F7FA) ou blanc. |
| **Contraste texte — thème sombre (1.4.3)** | Le thème sombre suit le réglage système (charte §11.2). Textes en `color.ink` sombre (#F2F4F7, ~17:1 sur #0E1116) ; actions/liens en `color.primary` sombre (#6AA6FF, **7.7:1**) ; surfaces `#171B21`. **Toute paire texte/fond ≥ 4.5:1.** |
| **Contraste non-texte — clair ET sombre (1.4.11)** | Indicateur de connexion, **bordures des boutons radio de bascule**, bordures de champ et puces en `border-strong` (≥ 3:1) ; **aucun fond coloré porteur de sens** (on reste sur `surface-alt`/blanc en clair, `#171B21` en sombre). Focus ≥ 3:1 vérifié **en clair et en sombre**. |
| **Couleur non seule (1.4.1)** | L'**indicateur réseau** (« Secours actif » / « Hors ligne ») combine **icône + libellé texte** ; les puces « Offert »/« Rechargé » portent leur sens dans **l'icône + le texte**, jamais la teinte ni un fond coloré. |
| **Reflow / zoom (1.4.4, 1.4.10)** | Tuiles volume et indicateur reflowent en colonne à 320 px et 200/400 % ; pas de défilement 2D ; libellés du groupe de bascule ne tronquent pas (+40 %). |
| **Ordre & visibilité du focus (2.4.3, 2.4.7, 2.4.11)** | Ordre de focus suit l'ordre de lecture (RTL inclus, propriétés logiques) ; contour focus ≥ 3px, offset 2px, contraste ≥ 3:1 en clair et en sombre. Dans la modale de désactivation, focus déplacé dedans et **restitué** à la fermeture (2.1.2). |
| **Nom accessible & sémantique du sélecteur (2.5.3, 4.1.2)** | Boutons « Autoriser », « Basculer sur le secours », « Désactiver » : nom accessible identique au libellé. Le **mode de bascule** est un **groupe de boutons radio** (`radiogroup`) intitulé « Mode de bascule », avec deux options nommées « Manuel » et « Automatique » ; l'option sélectionnée est annoncée (**pas** d'interrupteur on/off, qui produirait une annonce trompeuse). |
| **Annonces d'état — région live (4.1.3)** | « Installation en cours… », « Vous êtes maintenant sur la connexion de secours », passage « Hors ligne », « Volume épuisé » annoncés en *live* **poli** ; erreurs bloquantes en **assertif**. Une **bascule automatique survenue seule** est annoncée quand elle se produit ; **toute demande de confirmation de coût** (mode Auto, dépassement du seuil) est annoncée. |
| **Cibles tactiles (2.5.8 + norme interne 44px)** | Boutons radio de mode de bascule, boutons, « Recharger le volume » **et tous les liens/actions secondaires** (« Pas maintenant », « Gérer », « Réessayer la vérification », « En savoir plus », « Aide »…) ont une **zone tactile ≥ 44×44 px** (viser 48), espacement ≥ 8 px — la **zone tactile**, pas seulement le glyphe. |
| **Gestes & alternatives (2.5.1)** | La bascule et la désactivation se font par **bouton/contrôle visible**, jamais par un geste seul. |
| **Mouvement réduit (2.3.1, prefers-reduced-motion)** | La progression d'installation et les transitions de bascule respectent `prefers-reduced-motion` ; aucune animation clignotante > 3/s. |
| **Écrans affichant un code (charte §10)** | En E5 (et tout écran pouvant afficher un code), **capture d'écran bloquée** + **message accessible** (`simSecours.recup.captureBloquee`) expliquant pourquoi ; **code masqué par défaut** (bouton « Afficher ») ; hérite des protections de `SPECS-recuperation-zero-perte.md`. |
| **Authentification accessible (3.3.8)** | La désactivation accepte **biométrie / code / gestionnaire de mots de passe** ; **aucun test cognitif** (recopier, énigme) comme seul facteur ; une alternative est fournie si la biométrie échoue. |
| **Aides persistantes (3.3.7)** | À la reprise après interruption d'installation, aucune information déjà fournie n'est redemandée. |
| **Titre de page unique (2.4.2)** | Chaque écran a un titre parlant (« Carte SIM numérique de secours » en E4, « Désactiver la carte ? »…). |
| **Timing (2.2.1)** | L'effacement du presse-papiers (si copie) et tout délai sont paramétrables/annonçables ; aucune étape critique ne dépend d'un chrono non désactivable. |

---

## 9. Sécurité & confiance UX

Règles dérivées de la charte §10 :

- **Anti-hameçonnage récurrent :** rappel contextuel en E3 et E5 : « Nous ne vous demanderons jamais votre phrase de récupération pour installer/utiliser cette carte » (`simSecours.commun.antiHameconnage`).
- **Pas d'urgence artificielle :** même pendant une coupure réseau, aucun compte à rebours anxiogène ni consentement pré-coché ; la bascule automatique est **annoncée**, jamais subie silencieusement.
- **Honnêteté du coût — garde-fous en dur (résout le dark pattern) :**
  - le **mode de bascule par défaut est Manuel** (défaut sûr : aucune consommation involontaire) ;
  - le mode **Automatique** n'engage **aucune consommation de volume payant sans confirmation** : au-delà du volume offert (gratuit), un **seuil de confirmation** s'affiche (« Au-delà de {seuil}, vous confirmez avant de continuer », `simSecours.bascule.seuilConfirmation`) ;
  - avant toute consommation payante, le volume restant et le caractère payant d'un dépassement sont visibles (E4). **Jamais de facturation surprise.** (La **valeur exacte du seuil X** relève de QS-1/Produit ; le **comportement par défaut est, lui, fixé**.)
- **Invariant de disponibilité du canal de contrôle (HYP-4) :** la **réception d'un code de récupération** reste **toujours possible, même à quota nul** — canal **prioritaire et hors quota**, indépendant du volume payant. Ce n'est **pas** une question ouverte mais un **invariant de conception** (testé en §13).
- **Écrans affichant un code protégés :** en E5 (et partout où un code peut s'afficher), **capture d'écran bloquée + message accessible** expliquant pourquoi, **masquage par défaut** du code, en cohérence et sans duplication avec `SPECS-recuperation-zero-perte.md`.
- **Actions à conséquence confirmées :** la **désactivation** décrit sa conséquence, place l'action destructive à la fin, n'en fait jamais le bouton par défaut, et exige une **ré-authentification accessible** (3.3.8).
- **Aucune fuite serveur/log :** l'identifiant de la carte, le nom d'opérateur détaillé et la consommation **ne transitent pas** vers l'analytique en clair ; les événements sont anonymisés et sans contenu (charte §10). Aucun secret du coffre n'est impliqué dans l'affichage de l'état réseau.
- **Presse-papiers :** aucun identifiant sensible n'est copiable par défaut ; si un code de secours reçu via ce canal doit être copié (flux zéro-perte), effacement automatique après court délai + avertissement (règle définie dans `SPECS-recuperation-zero-perte.md`, référencée ici sans duplication).
- **Auto-conservation rappelée :** on rappelle, sans effrayer, que ce canal aide à **récupérer soi-même** l'accès — personne, pas même TEL ONLINE, ne le fait à la place (charte §10).

---

## 10. Offline-first & réseau

- **Consultable hors ligne :** E1 (explication), l'aide, et E4 (état + volume depuis données locales) fonctionnent **sans réseau**.
- **Le paradoxe, traité en face (§4.0) :** l'installation initiale (E3) peut exiger une connexion **une seule fois**. Message honnête + **alternatives systématiques** : (1) se connecter à un Wi-Fi ; (2) utiliser le réseau mobile habituel le temps de l'installation ; (3) reprendre plus tard (état persisté). **Jamais** d'écran sans issue.
- **Installation proactive encouragée :** on pousse l'installation **quand le réseau est là** (onboarding), pour que le secours soit prêt quand le réseau manquera.
- **Utilisation sans nouvelle connexion :** une fois installée, la **bascule vers le secours** (E4) et le **canal de récupération** (E5) fonctionnent **sans re-télécharger** quoi que ce soit — point répété pour rassurer. La **réception d'un code** reste garantie **même à quota nul** (invariant §9).
- **Mise en file honnête :** les actions nécessitant le serveur (désactivation, recharge) passées hors ligne sont **mises en attente** avec le message `simSecours.commun.enFile` et exécutées à la synchro. La désactivation en file avertit que **le canal reste actif** jusqu'à l'envoi.
- **Indicateur toujours honnête :** « Réseau habituel » / « Secours actif » / « Hors ligne » reflètent l'état réel, jamais un état optimiste faux (charte §8).
- **Synchro au retour du réseau :** volume et statut se resynchronisent automatiquement ; l'utilisateur est informé du résultat (succès/échec) sans jargon.

---

## 11. Performance

Budgets charte §9 (mobile d'entrée de gamme, réseau lent) :

- **E1/E2 quasi instantanés :** contenu majoritairement local ; **FCP ≤ 2,5 s** ; squelettes plutôt que spinners nus.
- **Écrans interactifs (E3/E4/E6) :** **TTI ≤ 4 s** et **INP ≤ 200 ms** (charte §9) — particulièrement suivis sur E4 (bascule, recharge, changement de mode) et E3/E6 (actions).
- **Feedback immédiat (< 100 ms)** au tap sur « Autoriser » / « Basculer », même si l'opération complète (installation, bascule) prend plus de temps : état « en cours » affiché sans délai.
- **E4 léger et fréquent :** l'écran d'état est consulté souvent → tuiles volume/indicateur en **chargement progressif**, données locales d'abord, rafraîchissement réseau ensuite.
- **`prefers-reduced-data` respecté :** pas de préchargement lourd ; illustrations des bénéfices (E1) allégées ou remplaçables par icônes.
- **Poids critique ≤ 200 Ko** sur les écrans du parcours ; police système, pas d'asset décoratif bloquant.
- **60 fps** sur la progression d'installation et les transitions de bascule ; dégradées proprement si `prefers-reduced-motion`.

---

## 12. Cas limites & erreurs

| Situation | Comportement | Message (FR, langage clair) |
|---|---|---|
| Hors ligne au moment d'installer (E3) | Branche paradoxe : alternatives + reprise, état sauvegardé | `simSecours.activation.horsLigne` : « L'installation a besoin d'une connexion, une seule fois. Voici comment faire. » |
| Appareil non compatible (E2) | Sortie non bloquante vers autres moyens de secours | `simSecours.compat.ko` : « Votre téléphone ne peut pas recevoir cette carte. Vous pouvez utiliser un autre moyen de secours. » |
| Vérification impossible (info manquante/hors ligne) | « Réessayer » + « Continuer quand même » avec avertissement | `simSecours.compat.inconnu` : « Impossible de vérifier pour l'instant. » |
| Échec d'activation | Cause probable + solution + reprise ; aucun code brut | `simSecours.activation.erreur` : « L'installation n'a pas abouti. Vérifiez votre connexion, puis réessayez. » |
| Permission refusée (E3) | Chemin de repli expliqué, pas de blocage sec ; ré-demande possible en contexte | Feuille : « Sans cette autorisation, la carte ne peut pas être installée. Vous pourrez réessayer à tout moment. » (clé `simSecours.activation.permission.refus`) |
| **Mode Automatique : dépassement imminent du volume gratuit** | **Confirmation demandée avant toute consommation payante** ; jamais de facturation silencieuse | `simSecours.bascule.seuilConfirmation` : « Au-delà de {seuil}, nous vous demanderons de confirmer avant de continuer. » |
| Quota épuisé (E4/E6) | Bannière + ce qui reste possible + recharge ; renvoi offre. **Réception d'un code de récupération toujours possible** (canal prioritaire hors quota) | `simSecours.quota.epuise` : « Volume de secours épuisé. » |
| **Quota = 0 pendant une récupération active (E5)** | **Invariant HYP-4 :** la réception d'un code de récupération reste garantie (hors quota, non facturée) | (aucun blocage ; le canal de contrôle reste prioritaire) |
| Volume illisible / statut inconnu | La connexion reste utilisable ; réessai proposé | « Impossible de lire le volume restant. La connexion de secours reste utilisable. » (clé `simSecours.quota.illisible`) |
| Bascule automatique déclenchée seule | Notifiée honnêtement, sans urgence ; annonce *live* ; **aucune consommation payante sans confirmation** | `simSecours.bascule.succes` : « Vous êtes maintenant sur la connexion de secours. » |
| Désactivation demandée hors ligne | Mise en file + avertissement que le canal reste actif jusqu'à l'envoi | `simSecours.commun.enFile` + note « La carte reste active jusqu'à l'envoi. » (clé `simSecours.desactiver.enFileNote`) |
| Interruption pendant installation (appel, fermeture) | Reprise exacte, rien redemandé (3.3.7) | « Reprendre l'installation » (clé `simSecours.activation.reprendre`) |
| Carte non installée mais récupération en cours (E5) | Renvoi E1/E3 en **préservant** le contexte de récupération + proposer autres facteurs | `simSecours.recup.nonInstallee` : « Cette carte n'est pas encore installée. Vous pouvez utiliser un autre moyen pour continuer. » |
| Offre gratuite sans volume de secours (selon `Q4`) | Afficher honnêtement la limite + option premium, sans dark pattern | À définir après `Q4`/`Q5` ; renvoi `SPECS-abonnement-freemium.md`. |

---

## 13. Critères d'acceptation (testables)

- [ ] Le terme **« carte SIM numérique de secours »** est employé partout, **titres d'écran inclus (dont E4)** ; **aucune** occurrence de « eSIM », « profil » ou « provisioning » à l'écran. **« connexion de secours »** n'apparaît **que** comme libellé fonctionnel de l'état réseau (sous-titre/indicateur) et est tracé comme tel.
- [ ] Les **7 états** (charte §13) sont spécifiés pour E1, E2, E3, E4, E6 (E5 justifie les états non applicables).
- [ ] **Une seule action primaire** par écran ; en E4, « Recharger le volume » est présenté en **action tertiaire/lien** (pas en bouton plein), ne concurrençant pas l'action primaire contextuelle.
- [ ] **Bascule — défaut sûr :** le mode par défaut est **Manuel** ; le mode **Automatique** n'engage **aucune consommation de volume payant sans confirmation** (seuil affiché). Testable : mode par défaut = Manuel ; en Auto, dépassement du volume gratuit → confirmation avant toute facturation.
- [ ] **Invariant disponibilité (HYP-4) :** **quota = 0 → réception d'un code de récupération toujours possible** (canal de contrôle prioritaire, hors quota, non facturé).
- [ ] Le **paradoxe offline** est traité : E3 hors ligne propose au moins **2 alternatives** + reprise sans perte d'état.
- [ ] L'**indicateur réseau** distingue « Réseau habituel / Secours actif / Hors ligne » par **icône + texte** (jamais la couleur seule, jamais un fond coloré porteur de sens).
- [ ] Le **volume restant** est formaté selon la locale (unités Mo/Go vs MB/GB, séparateur décimal) ; l'**accord « restant/restants » est piloté par la valeur via ICU plural** (testé FR/AR pour value = 0, value < 2, value ≥ 2) ; le **renouvellement** est une **date absolue formatée par la locale** (ordinal FR « 1er »), en **format unique** ; **aucune concaténation ni date en dur**.
- [ ] La **désactivation** affiche une confirmation décrivant la conséquence, action destructive à la fin, non par défaut, + **ré-authentification accessible** (3.3.8).
- [ ] Les **permissions** sont demandées **en contexte** en E3, avec explication **avant** la demande ; jamais au démarrage.
- [ ] Le **mode de bascule** est un **groupe de boutons radio** (`radiogroup`, libellé de groupe + 2 options nommées « Manuel » / « Automatique »), **pas** un interrupteur on/off ; l'option sélectionnée est correctement annoncée.
- [ ] Les **écrans pouvant afficher un code** (E5 en particulier) **bloquent la capture d'écran** avec **message accessible** + **masquent le code par défaut**, en cohérence avec `SPECS-recuperation-zero-perte.md`.
- [ ] Toutes les chaînes ont une **clé i18n** ; testées **FR / EN / ES / AR-RTL** avec **+40 %** de longueur (illustré par ES/EN long, pas DE), sans troncature de bouton, mise en miroir RTL correcte.
- [ ] Les **contrastes** sont vérifiés **en thème clair ET en thème sombre** (charte §11.2) pour texte, indicateur réseau, bordures des boutons radio de bascule, puces et focus.
- [ ] Les **cibles tactiles** — boutons radio de bascule, boutons, « Recharger le volume » **et tous les liens/actions secondaires** — ont une **zone tactile ≥ 44×44 px** (viser 48), espacement ≥ 8 px.
- [ ] Les **budgets de performance** sont respectés : FCP ≤ 2,5 s ; **TTI ≤ 4 s** et **INP ≤ 200 ms** sur les écrans interactifs (E3/E4/E6) ; feedback < 100 ms ; ≤ 200 Ko ; 60 fps.
- [ ] Les **annonces d'état** (installation, bascule, hors ligne, quota épuisé, demande de confirmation de coût) passent par une **région live** ; erreurs bloquantes en assertif.
- [ ] **Aucun** identifiant de carte / nom d'opérateur détaillé / consommation en clair dans les **logs ou l'analytique**.
- [ ] Les messages réseau sont **honnêtes** (mise en file explicite), sans erreur technique brute.
- [ ] Les renvois vers `SPECS-recuperation-zero-perte.md` et `SPECS-abonnement-freemium.md` sont présents et **ne dupliquent pas** leur contenu.
- [ ] Reprise après interruption d'installation **sans redemander** d'information (3.3.7).

---

## 14. Métriques de succès (KPIs UX)

1. **Taux d'installation proactive :** % d'utilisateurs ayant installé la carte **avant** d'en avoir besoin (cible haute — c'est le meilleur antidote au paradoxe offline).
2. **Taux de réussite d'activation :** % d'installations menées à bien / démarrées ; segmenter en ligne vs branche hors ligne.
3. **Taux d'échec / d'erreur d'activation :** % d'installations aboutissant à E6 erreur ; suivre les causes (compatibilité, réseau, permission).
4. **Taux de bascule réussie pendant coupure :** % de bascules (auto ou manuelle) aboutissant à une connexion effective, quand le réseau habituel manque.
5. **Taux d'abandon à la compatibilité (E2) :** % quittant après un résultat « non compatible » **sans** emprunter un autre moyen de secours (à minimiser via les alternatives).
6. **Taux de confirmation de coût respectée (mode Auto) :** part des consommations payantes précédées d'une confirmation (cible = 100 % ; indicateur anti-facturation surprise).

---

## 15. Questions ouvertes

> Les points de comportement critiques (défaut de bascule, disponibilité du canal de contrôle, protection anti-capture, contrastes sombres) sont désormais **tranchés dans la SPEC**. Les questions ci-dessous ne portent plus que sur des **valeurs/paramètres produit**, sans laisser d'invariant de sécurité non défini.

- `Q4` (charte) — **La carte de secours est-elle incluse au gratuit (avec quota) ou strictement premium ?** Impacte E1 (note coût), E4 (étiquette Offert/Rechargé) et E6 (quota épuisé). **Bloquant** pour finaliser la microcopie coût.
- `Q5` (charte) — **Volume exact gratuit vs premium** et **règle de renouvellement** (mensuel ? à date fixe ?). Impacte `simSecours.quota.*` et la valeur affichée de `simSecours.quota.renouvellement` (le **format** est déjà fixé : date absolue localisée).
- `Q7` (charte) — **Contraintes réglementaires opérateur / KYC** pour activer une carte SIM numérique (selon marché). Peut ajouter une étape d'identité en E3 → à spécifier alors avec 3.3.8.
- `Q3` (charte) — **Ordre de priorité des facteurs** de la récupération « zéro perte » : où se place ce canal par rapport à la phrase et aux contacts de confiance ? Impacte E5 et le repli en cas d'échec.
- **QS-1 (spécifique — comportement fixé, reste à paramétrer) —** Le **défaut est Manuel** et le mode **Automatique n'engage aucune consommation payante sans confirmation** (invariant posé). Reste à trancher avec Produit **la valeur du seuil X** (`{seuil}`) et son libellé exact. **Non bloquant pour le comportement par défaut.**
- **QS-2 (spécifique — invariant posé) —** La **réception d'un code de secours reste prioritaire et garantie même à quota nul** (canal de contrôle hors quota, `HYP-4`) : c'est un **invariant de conception**, pas une question ouverte. Reste à confirmer avec Produit/Opérateur (`Q7`) les **modalités techniques** de mise en œuvre.
- **QS-3 (spécifique) —** Périmètre exact du **format d'opérateur localisé** (afficher un nom d'opérateur ? le masquer pour raisons de confidentialité/log ?) — à arbitrer avec §9 (aucune fuite).
