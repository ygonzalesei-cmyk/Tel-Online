# SPEC — Phrase de récupération (sauvegarde & vérification)

## 0. Métadonnées

| Champ | Valeur |
|---|---|
| **Titre** | Parcours « Phrase de récupération » — pédagogie, affichage sécurisé, sauvegarde, vérification, report |
| **Statut** | **v0.2 — proposition** (corrigée après audit adversarial ; pour revue en atelier design) |
| **Identifiant** | `SPECS-phrase-recuperation.md` |
| **Écrans couverts** | E1 Pourquoi sauvegarder · E2 Affichage sécurisé de la phrase · E3 Conseils de sauvegarde · E4 Vérification · E5 Succès · E6 Reporter (feuille inférieure) · + bannière « pas encore sauvegardée » |
| **Charte de référence** | `orchestration/agents/design/CHARTE-UX.md` (v0.1). En cas de conflit, la charte fait foi ; toute exception est justifiée et tracée dans cette SPEC. |
| **Langues** | FR (défaut) · EN · ES · AR (RTL) — architecture extensible |
| **Hypothèses produit utilisées** | `HYP-1` (coffre auto-conservé), `HYP-2` (phrase de récupération = secret maître), `HYP-3` (récupération « zéro perte » multi-facteurs), `HYP-5` (freemium — sans impact ici) |
| **Parcours liés** | `SPECS-onboarding-coffre.md` (point d'entrée principal), `SPECS-recuperation-zero-perte.md` (la phrase est **un** des facteurs), `SPECS-esim-secours.md` (proposée si réseau requis ailleurs — pas ici) |

> Ce parcours ne redéfinit pas le produit. Les points `HYP-#` sont des hypothèses à confirmer par le pôle Produit (voir §15).

---

## 1. Objectif & enjeux

**Rôle du parcours.** Permettre à une personne non technique de **sauvegarder elle-même** la phrase de récupération de son coffre, de **comprendre pourquoi** c'est vital, puis de **vérifier** que sa copie **papier** est correcte — ou de **reporter** en connaissance de cause. C'est le geste qui rend le coffre réellement récupérable en cas de perte du téléphone.

**Pourquoi c'est sensible.**
- **Secret maître.** La phrase donne accès à **tout** le coffre. Qui la détient détient les secrets.
- **Argent & identité.** Le coffre peut contenir des clés, mots de passe, documents, contacts de confiance : sa compromission a des conséquences concrètes.
- **Irréversibilité de l'auto-conservation (`HYP-1`).** Les secrets restent sur l'appareil ; **personne, pas même TEL ONLINE, ne peut restaurer le coffre à la place de l'utilisateur**. Une phrase perdue = coffre perdu. Une phrase volée = coffre volé.
- **Cible d'hameçonnage.** Ce type de secret est activement ciblé par la fraude. Le parcours doit **immuniser** l'utilisateur par un message clair et répété : TEL ONLINE ne demande jamais la phrase.

**Conséquence UX.** Le parcours doit être **calme, expliqué, sans urgence artificielle**, mais **protégé techniquement** (anti-capture, masquage, anti-exfiltration presse-papiers, aucune fuite serveur/log) et **reprenable** sans perte. La vérification (E4) doit confirmer une **transcription papier indépendante** du secret affiché : elle ne doit jamais pouvoir être satisfaite par un simple copier-coller (cf. §9).

---

## 2. Personas & cas d'usage concernés

Personas de la charte (§3). Ce parcours est validé quand il fonctionne pour **Robert, Amina et Marc**.

| Persona | Pertinence ici | Ce que ça impose |
|---|---|---|
| **Robert, 71 ans — prudent** | Central. Peur de « tout casser », doit écrire sur papier, a besoin de réassurance et de repères. | Gros texte, étapes courtes, **progression visible**, aucun jargon, report possible sans culpabilisation, aide accessible. |
| **Amina, 28 ans — multilingue (FR/AR, RTL)** | Central. Lit en RTL ; la phrase et sa numérotation doivent rester lisibles. | Mise en miroir correcte, numéros de mots selon la locale, aucune concaténation, liste de mots localisée (Q2), **accès langue dès E1**. |
| **Marc, 45 ans — lecteur d'écran** | Central. L'écran secret et la vérification doivent s'annoncer proprement. | Nom accessible des mots, région live pour afficher/masquer et pour le résultat, vérification **non cognitive bloquante** (3.3.8), focus jamais piégé, **rappel regard ET écoute** (fuite audio du haut-parleur / Bluetooth). |
| **Léa, 22 ans — nomade, réseau instable** | Élevée. Doit pouvoir tout faire **hors ligne** et reprendre après coupure. | Offline-first total, reprise à l'étape exacte, aucun blocage réseau. |
| **Camille, 34 ans — pragmatique** | Moyenne. Risque de vouloir « passer » l'étape. | Report clair et honnête, rappel ultérieur, friction minimale mais garde-fou de vérification. |

**Cas d'usage.** (a) Fin de création du coffre → première sauvegarde. (b) Rappel ultérieur (« pas encore sauvegardée »). (c) Reprise après report. (d) Depuis Réglages > Sécurité, « Revoir / sauvegarder ma phrase ».

---

## 3. Pré-requis, points d'entrée et de sortie

**Pré-requis.**
- Le coffre existe et la phrase de récupération a été **générée localement sur l'appareil** (voir `SPECS-onboarding-coffre.md`). Elle **n'a jamais été envoyée à un serveur** et n'est **jamais persistée en clair**.
- Longueur de la phrase = **hypothèse ouverte** (12 vs 24 mots, cf. `Q2`). La SPEC est indépendante du nombre : le compteur est paramétré (ICU).

**Points d'entrée.**
1. Depuis l'onboarding du coffre (enchaînement direct après création).
2. Depuis la **bannière persistante** « Votre phrase n'est pas encore sauvegardée » (accueil / coffre).
3. Depuis un **rappel** local (notification différée, cf. §10) après un report.
4. Depuis **Réglages > Sécurité > Phrase de récupération**.

**Points de sortie.**
- **Succès (E5)** → retour au flux appelant : suite de l'onboarding, ou `SPECS-recuperation-zero-perte.md` (facteur suivant), ou Réglages. La bannière « pas encore sauvegardée » disparaît.
- **Report (E6)** → retour au flux appelant, bannière persistante activée + rappel programmé.

**Reprise après interruption (Rien ne se perd — charte §2.5, §8).**
- Quitter à E1/E2/E3/E4 **ne perd rien** : la phrase étant dérivée du coffre présent sur l'appareil, elle peut être **ré-affichée** à la reprise. Aucune progression de vérification n'est persistée avec le secret.
- À la reprise, on revient à **E1** (rappel du pourquoi) puis on ré-affiche E2. Jamais de reprise « au milieu » d'un écran secret sans repasser par le masquage par défaut.

---

## 4. Parcours utilisateur (flux)

```
[Entrée] ─► E1 Pourquoi sauvegarder (Étape 1/4) ──"Plus tard"──►  E6 Reporter (feuille)
                │ "Voir ma phrase"                                  │ "Reporter" ─► [Sortie] + bannière + rappel
                ▼                                                   │ "Sauvegarder maintenant" ─► retour E1
        E2 Affichage sécurisé (Étape 2/4, masqué par défaut)        │ "Fermer"/voile/Échap ─► retour E1 (neutre, rien reporté)
                │ Afficher ⇄ Masquer  (copie DÉSACTIVÉE par défaut — cf. §9/Q7)
                │ "J'ai noté ma phrase"
                ▼
        E3 Conseils de sauvegarde (Étape 3/4) ──"Revoir ma phrase"──► E2
                │ "Continuer"
                ▼
        E4 Vérification (Étape 4/4 — re-saisir/choisir ; presse-papiers vidé à l'entrée, collage interdit)
                │ "Revoir ma phrase" ──► E2 (autant que voulu)
                │ "Vérifier toute ma phrase" (option) ──► vérification complète
                │ erreur ─► rester sur E4, réessayer (illimité)
                │ succès de vérification
                ▼
        E5 Succès ──► [Sortie] (bannière retirée ; microcopie honnête : « quelques mots vérifiés »)
```

**Règles de flux.**
- **Retour arrière** disponible à chaque étape (E2→E1, E3→E2, E4→E2) sans perte.
- **Indicateur d'étape** visible et annoncé (« Étape X sur Y ») sur E1→E4 (cf. §6, §8). E5 (succès) et E6 (feuille de report) sont hors séquence numérotée.
- **Report** accessible depuis E1 (et depuis la bannière). À partir de E2, quitter renvoie la personne vers la sortie **avec** la bannière « pas encore sauvegardée » si la vérification n'a pas réussi.
- **Vérification non bloquante cognitivement** : on peut **toujours** revoir la phrase (E2) pendant E4 ; nombre d'essais **illimité** ; aucune pénalité, aucun minuteur.
- **Intégrité de la vérification (garde-fou anti-collage)** : à l'entrée de E4, le presse-papiers est **vidé** ; le **collage est interdit** dans les champs de E4. Le statut « sauvegardée » n'est **jamais** accordé sur la base d'un collage (cf. §9).

---

## 5. Écrans & états

> Convention : **une seule action primaire par écran**, ancrée en bas (zone du pouce). Pour chaque écran, les **7 états** de la charte §13 sont couverts ou justifiés « N/A ».

### E1 — Pourquoi sauvegarder (pédagogie + anti-hameçonnage) — Étape 1/4
- **But.** Faire comprendre l'enjeu en < 30 s et immuniser contre l'hameçonnage avant de montrer quoi que ce soit.
- **Éléments.** **Accès au choix de langue** (en-tête) ; **indicateur d'étape** (« Étape 1 sur 4 ») ; titre ; chapô (1 phrase) ; 3 points clés (à quoi sert la phrase / seule façon de récupérer / personne ne peut le faire à votre place) ; **encart anti-hameçonnage** (icône + texte) ; durée estimée (« au calme, comptez 3 minutes »).
- **Action primaire.** « Voir ma phrase ».
- **Actions secondaires.** « Plus tard » (→ E6) ; **une seule affordance d'aide** dans l'en-tête (`commun.aide.lien`, hors ligne).
- **États.** Chargement (N/A — contenu éditorial statique local, affichage immédiat) · Vide (N/A — pas de donnée utilisateur) · Contenu (✔ nominal) · Erreur (N/A — aucune opération faillible ici) · Hors ligne (✔ bandeau discret : tout fonctionne) · Succès (N/A — porté par E5) · Permission requise (N/A — aucune permission demandée).

### E2 — Votre phrase de récupération (affichage sécurisé) — Étape 2/4
- **But.** Montrer la phrase de façon **contrôlée** : masquée par défaut, capture bloquée, aucune fuite, aucune exfiltration presse-papiers.
- **Éléments.** Indicateur d'étape ; titre ; **bandeau de protection** (« capture désactivée pour vous protéger ») ; **zone phrase masquée** (mots remplacés par des points, numérotés) ; bouton **Afficher / Masquer** ; compteur « X mots à recopier dans l'ordre » ; rappel « personne ne regarde votre écran ? » (variante **regard + écoute** si lecteur d'écran actif, cf. §7/§8). **La copie de la phrase n'est PAS proposée par défaut** (secret maître ; risque d'exfiltration hors appareil — cf. §9). Elle n'apparaît que si une décision produit/réglementaire explicite l'autorise (Q7), avec les garde-fous et limites documentés au §9.
- **Action primaire.** « J'ai noté ma phrase » (activable même masqué ; on ne force pas l'affichage).
- **Actions secondaires.** Afficher/Masquer ; retour (← E1) ; « Copier » **uniquement si activée par Q7** (encadrée, avertie).
- **États.** Chargement (✔ **squelette de la grille de mots** en cas d'attente exceptionnelle de dérivation — cf. §11 ; jamais de page blanche) · Vide (N/A — la phrase existe toujours ; si indisponible → Erreur) · Contenu (✔ masqué / affiché) · Erreur (✔ phrase indisponible → reprise, cf. §12) · Hors ligne (✔) · Succès (N/A — porté par E5) · Permission requise (N/A — aucune permission système ; la capture est bloquée sans demande).

### E3 — Mettez votre phrase à l'abri (conseils de sauvegarde) — Étape 3/4
- **But.** Guider un stockage papier sûr et prévenir les erreurs (photo, cloud, partage).
- **Éléments.** Indicateur d'étape ; titre ; liste de conseils positifs (écrire sur papier, ranger dans deux endroits sûrs et secs, ne pas photographier, ne pas stocker en ligne, ne jamais partager) ; rappel « chiffré, lisible par vous seul ».
- **Action primaire.** « Continuer ».
- **Actions secondaires.** « Revoir ma phrase » (→ E2).
- **États.** Chargement (N/A — contenu statique local) · Vide (N/A — contenu éditorial) · Contenu (✔) · Erreur (N/A) · Hors ligne (✔) · Succès (N/A) · Permission requise (N/A).

### E4 — Vérifions ensemble (vérification) — Étape 4/4
- **But.** Confirmer que la copie **papier** est correcte, **sans test de mémoire bloquant**, en validant une **transcription indépendante** du secret affiché (ni collage, ni presse-papiers).
- **Éléments.** Indicateur d'étape ; titre ; consigne (« recopiez depuis votre feuille ») ; **N champs secrets** demandant quelques mots à des positions données (ex. « mot n° 3 », « mot n° 7 »…, N réduit — cf. QP-1), **avec collage interdit** ; **alternative accessible** « Choisir dans une liste » (reconnaissance) par champ ; lien **« Revoir ma phrase »** ; **option « Vérifier toute ma phrase »** (vérification complète facultative, même alternative « liste ») ; aide reliée aux champs.
- **Action primaire.** « Valider ».
- **Actions secondaires.** « Revoir ma phrase » (→ E2) ; « Vérifier toute ma phrase » (optionnel) ; basculer saisie ⇄ liste ; retour.
- **États.** Chargement (N/A — validation locale synchrone) · Vide (✔ champs non remplis) · Contenu (✔ saisie) · Erreur (✔ un mot ne correspond pas — non bloquant, réessai illimité) · Hors ligne (✔) · Succès (✔ transition vers E5) · Permission requise (N/A).

### E5 — C'est enregistré (succès)
- **But.** Confirmer **honnêtement** (sans sur-promettre), rassurer, rappeler la règle anti-hameçonnage, proposer la suite.
- **Éléments.** Icône succès + titre ; message **précis** (« nous avons vérifié quelques mots — relisez bien toute votre feuille ») ; **rappel de sécurité** (« TEL ONLINE ne vous demandera jamais cette phrase ») ; prochaine étape. Si l'utilisateur a fait « Vérifier toute ma phrase », le message le reflète.
- **Action primaire.** « Continuer » (ou « Terminer », selon flux appelant).
- **Actions secondaires.** « En savoir plus sur la récupération » (→ `SPECS-recuperation-zero-perte.md`) ; « Vérifier toute ma phrase » reste accessible si la vérification était partielle.
- **États.** Chargement (N/A) · Vide (N/A) · Contenu (N/A — l'état nominal est le Succès) · Erreur (N/A — un échec de synchro du **statut** reste silencieux côté utilisateur, cf. §10) · Hors ligne (✔ le statut « sauvegardée » est local) · Succès (✔ nominal) · Permission requise (N/A).

### E6 — Reporter la sauvegarde (feuille inférieure)
- **But.** Laisser reporter **en connaissance de cause**, sans dark pattern ni culpabilisation.
- **Éléments.** **Poignée** + **bouton « Fermer » visible** (équivalent single-pointer au glissement) ; titre ; **rappel explicite du risque** (« sans cette phrase, personne ne pourra récupérer votre coffre — pas même TEL ONLINE ») ; info « nous vous le rappellerons plus tard ».
- **Action primaire.** « Sauvegarder maintenant » (revient au parcours — choix par défaut sûr).
- **Action secondaire (à risque).** « Reporter » (placée en fin, jamais bouton par défaut).
- **Sortie neutre.** Fermeture par le **bouton « Fermer »**, l'**appui sur le voile** ou la touche **Échap/clavier** ⇒ ferme la feuille et **revient à E1**, **sans reporter ni sauvegarder** : aucun changement d'état, aucun rappel programmé, bannière inchangée. Le glissement de la poignée est **une alternative**, jamais le seul moyen (WCAG 2.5.1 / 2.5.7).
- **États.** Chargement (N/A) · Vide (N/A) · Contenu (✔) · Erreur (N/A) · Hors ligne (✔ le rappel est local) · Succès (N/A — le report est une sortie, pas un succès d'écran) · Permission requise (✔ **applicable au sous-parcours rappel** : si le rappel passe par une notification système, la permission de notification est demandée **au moment utile**, à la sélection de « Reporter », avec explication — cf. §10, QP-2).

---

## 6. Wireframes textuels

> Portrait, une main. Titre en haut, contenu au centre, **actions ancrées dans le tiers inférieur**. Flèches directionnelles en **propriétés logiques** (miroir en RTL). Les emphases se font par le **gras / la typographie**, **jamais par des capitales** (nuisent au FALC et aux lecteurs d'écran).

### E1 — Pourquoi sauvegarder
```
┌──────────────────────────────┐
│ ←     [🌐 Français ▾]    Aide  │  (44px) langue + aide (1 seule affordance)
│  Étape 1 sur 4                │  progression (visible + annoncée)
│                               │
│  Sauvegardez votre phrase     │  H1
│  de récupération              │
│                               │
│  C'est la seule façon de      │  chapô
│  retrouver votre coffre si    │
│  vous perdez ce téléphone.    │
│                               │
│  • Elle ouvre tout votre      │
│    coffre                     │
│  • Vous êtes la seule         │
│    personne à devoir la garder│
│  • Personne ne peut la        │
│    retrouver à votre place,   │
│    pas même TEL ONLINE        │
│                               │
│ ┌──────────────────────────┐ │
│ │ ⚠ TEL ONLINE ne vous     │ │  encart anti-hameçonnage
│ │ demandera **jamais**     │ │  (fond warning-surface,
│ │ cette phrase par message,│ │   emphase en gras, pas de capitales)
│ │ appel ou courriel.       │ │
│ └──────────────────────────┘ │
│                               │
│  ⏱ Au calme, comptez 3 minutes│
│ ─────── zone du pouce ─────── │
│ [   Voir ma phrase        ]   │  primaire
│ [   Plus tard             ]   │  secondaire (→ E6)
└──────────────────────────────┘
```

### E2 — Affichage sécurisé (masqué par défaut)
```
┌──────────────────────────────┐
│ ←   Votre phrase              │
│  Étape 2 sur 4                │
│ ┌──────────────────────────┐ │
│ │ 🔒 Capture d'écran        │ │  bandeau protection
│ │ désactivée ici pour       │ │
│ │ vous protéger.            │ │
│ └──────────────────────────┘ │
│  12 mots à recopier          │  compteur (ICU)
│  dans l'ordre.               │
│ ┌──────────────────────────┐ │
│ │ 1 ••••••   2 ••••••       │ │  MASQUÉ par défaut
│ │ 3 ••••••   4 ••••••       │ │  (points = color.ink, cf. §8)
│ │ 5 ••••••   6 ••••••       │ │
│ │      … (2 col.)           │ │
│ └──────────────────────────┘ │
│        [ 👁 Afficher ]        │  bascule (44px)
│  Personne ne regarde votre    │  (variante regard + écoute
│  écran ?                      │   si lecteur d'écran actif)
│                               │  (pas de « Copier » par défaut)
│ ─────── zone du pouce ─────── │
│ [   J'ai noté ma phrase   ]   │  primaire
└──────────────────────────────┘
```

### E4 — Vérification
```
┌──────────────────────────────┐
│ ←   Vérifions ensemble        │
│  Étape 4 sur 4                │
│  Recopiez les mots demandés   │
│  depuis votre feuille.        │
│                               │
│  Mot n° 3                     │  label au-dessus
│  [ ______________  👁 ]       │  champ secret (collage interdit)
│      ↳ Choisir dans une liste │  alternative accessible
│                               │
│  Mot n° 7                     │
│  [ ______________  👁 ]       │
│      ↳ Choisir dans une liste │
│                               │
│  ↩ Revoir ma phrase           │  (→ E2, illimité)
│  ✓ Vérifier toute ma phrase   │  (option — vérification complète)
│ ─────── zone du pouce ─────── │
│ [   Valider               ]   │  primaire
└──────────────────────────────┘
```

### E6 — Reporter (feuille inférieure)
```
┌──────────────────────────────┐
│            ▂▂▂          ✕      │  poignée + bouton Fermer (44px)
│  Reporter la sauvegarde ?     │
│                               │
│  Sans cette phrase, personne  │
│  ne pourra récupérer votre    │
│  coffre si vous perdez ce     │
│  téléphone. Pas même          │
│  TEL ONLINE.                  │
│                               │
│  Nous vous le rappellerons    │
│  plus tard.                   │
│ ─────── zone du pouce ─────── │
│ [ Sauvegarder maintenant  ]   │  primaire (choix sûr)
│ [ Reporter                ]   │  secondaire, en fin
└──────────────────────────────┘
   (voile tapable / Échap = fermeture neutre → retour E1)
```

*(E3 et E5 suivent la même trame : indicateur d'étape le cas échéant, titre haut, contenu centre, action primaire ancrée bas.)*

---

## 7. Microcopie (FR)

> Aucune chaîne en dur, aucune concaténation. Pluriels/insertions en **ICU**. **Clés en ASCII snake_case** (pas de caractère accentué dans l'identifiant). Namespace `phrase.*` ; éléments partagés en `commun.*`.

| Élément | Clé i18n | Texte FR | Note i18n / traduction |
|---|---|---|---|
| Sélecteur de langue | `commun.langue.selecteur` | Langue | Nom accessible : « Choisir la langue ». Libellé de la langue courante issu de la locale. Présent sur E1 (charte §6). |
| Indicateur d'étape | `phrase.commun.etape` | Étape {courante} sur {total} | Nombres selon la locale ; annoncé aux lecteurs d'écran ; propriétés logiques en RTL. |
| Aide (en-tête, unique) | `commun.aide.lien` | Aide | Nom accessible : « Aide : comment ça marche ? ». **Affordance d'aide unique** (fusion en-tête + corps). Hors ligne. |
| Titre E1 | `phrase.intro.titre` | Sauvegardez votre phrase de récupération | +40 % en DE/FR ; ne pas tronquer. |
| Chapô E1 | `phrase.intro.chapo` | C'est la seule façon de retrouver votre coffre si vous perdez ce téléphone. | Phrase courte, FALC. |
| Point 1 | `phrase.intro.point1` | Elle ouvre tout votre coffre. | — |
| Point 2 | `phrase.intro.point2` | Vous êtes la seule personne à devoir la garder. | Éviter tournure négative complexe. |
| Point 3 | `phrase.intro.point3` | Personne ne peut la retrouver à votre place, pas même TEL ONLINE. | Nom de marque non traduit. |
| Anti-hameçonnage | `phrase.intro.antihameconnage` | TEL ONLINE ne vous demandera jamais votre phrase de récupération, ni par message, ni par appel, ni par courriel. | Répété en E5. Ne pas concaténer les canaux : liste dans la chaîne. **Emphase par le gras, jamais par des capitales.** |
| Durée | `phrase.intro.duree` | Au calme, comptez {minutes, plural, one {# minute} other {# minutes}}. | ICU : chiffre et pluriel **localisés** (AR a plusieurs formes). |
| CTA primaire E1 | `phrase.intro.cta_voir` | Voir ma phrase | Verbe d'action. |
| CTA report E1 | `phrase.intro.cta_plus_tard` | Plus tard | Neutre, non culpabilisant. |
| Titre E2 | `phrase.affichage.titre` | Votre phrase de récupération | — |
| Bandeau capture | `phrase.affichage.capture_bloquee` | La capture d'écran est désactivée sur cet écran pour protéger votre phrase. | Doit être **lisible par lecteur d'écran** (pas image de texte). |
| Capture détectée (masquage) | `phrase.affichage.capture_detectee_masquee` | Pour votre sécurité, votre phrase a été masquée. | Plateformes **détectant** (sans bloquer) la capture. Annoncé en région live **assertive**. +40 % ; RTL. |
| Compteur mots | `phrase.affichage.compteur` | {count, plural, one {# mot à recopier dans l'ordre} other {# mots à recopier dans l'ordre}} | ICU obligatoire ; AR a plusieurs formes plurielles. |
| Bouton afficher | `phrase.affichage.bouton_afficher` | Afficher | Nom accessible complet : « Afficher ma phrase ». |
| Bouton masquer | `phrase.affichage.bouton_masquer` | Masquer | « Masquer ma phrase ». |
| Garde regard | `phrase.affichage.garde_regard` | Assurez-vous que personne ne regarde votre écran. | — |
| Garde regard + écoute | `phrase.affichage.garde_regard_ecoute` | Utilisez un casque et assurez-vous que personne ne regarde ni n'écoute votre écran. | Affichée/annoncée notamment **quand un lecteur d'écran est actif** (la phrase est lue à voix haute). +40 % ; RTL. |
| Numéro de mot (affiché) | `phrase.affichage.mot_position` | Mot n° {position} : {mot} | Pour lecteur d'écran ; `{position}` selon locale. **Format homogène** avec `phrase.verif.champ_label`. |
| Numéro de mot (masqué) | `phrase.affichage.mot_masque` | Mot n° {position} : masqué | Lu quand la phrase est masquée. |
| Copier (option, conditionnelle) | `phrase.affichage.copier` | Copier (déconseillé) | **Affiché uniquement si copie autorisée (Q7). Désactivé par défaut.** |
| Avert. copie (conditionnelle) | `phrase.affichage.copier_avert` | Copier votre phrase est risqué : d'autres apps ou une synchronisation entre appareils peuvent la conserver. Ne la copiez que si vous savez ce que vous faites. | Ne pas présenter l'effacement comme protection suffisante (cf. §9). |
| Copie confirmée (conditionnelle) | `phrase.affichage.copier_efface` | {seconds, plural, one {Copié. Le presse-papiers sera vidé dans # seconde.} other {Copié. Le presse-papiers sera vidé dans # secondes.}} | ICU ; annoncé en région live. **N'annule pas** l'historique/cloud presse-papiers (cf. §9). |
| CTA primaire E2 | `phrase.affichage.cta_note` | J'ai noté ma phrase | Activable même masqué. |
| Titre E3 | `phrase.conseils.titre` | Mettez votre phrase à l'abri | — |
| Conseil 1 | `phrase.conseils.papier` | Écrivez-la sur papier, à la main. | Impératif positif. |
| Conseil 2 | `phrase.conseils.ranger` | Rangez-la dans deux endroits sûrs et secs. | — |
| Conseil 3 | `phrase.conseils.pas_photo` | Ne la prenez pas en photo. | Affirmatif simple. |
| Conseil 4 | `phrase.conseils.pas_en_ligne` | Ne la stockez pas en ligne. | — |
| Conseil 5 | `phrase.conseils.pas_partage` | Ne la partagez avec personne. | — |
| Rassurance E3 | `phrase.conseils.chiffre` | Votre coffre reste chiffré, lisible par vous seul. | Glossaire : « chiffré (lisible par vous seul) ». |
| CTA primaire E3 | `phrase.conseils.cta_continuer` | Continuer | — |
| Revoir phrase | `phrase.commun.revoir` | Revoir ma phrase | Réutilisé E3/E4/E5. |
| Titre E4 | `phrase.verif.titre` | Vérifions ensemble | — |
| Consigne E4 | `phrase.verif.consigne` | Recopiez les mots demandés depuis votre feuille de papier. | Souligne la source **papier** (transcription indépendante). |
| Label champ | `phrase.verif.champ_label` | Mot n° {position} | `{position}` localisé. Format homogène partout. |
| Aide champ | `phrase.verif.champ_aide` | Recopiez ce mot depuis votre feuille de papier. Le collage est désactivé. | Relié via description ; explique l'interdiction de collage (3.3.8). |
| Alternative | `phrase.verif.alt_liste` | Choisir dans une liste | Alternative non-saisie (3.3.8). |
| Vérif. complète (option) | `phrase.verif.cta_complete` | Vérifier toute ma phrase | Vérification facultative de l'intégralité, avec l'alternative « liste » pour ne pas surcharger. |
| Erreur mot | `phrase.verif.erreur_mot` | Ce mot ne correspond pas. Vérifiez sur votre feuille et réessayez. | Cause + solution ; jamais bloquant. |
| Succès inline | `phrase.verif.ok` | C'est le bon mot. | Annonce polie. |
| CTA primaire E4 | `phrase.verif.cta_valider` | Valider | — |
| Titre E5 | `phrase.succes.titre` | Votre sauvegarde est enregistrée | Ne promet pas une intégrité totale (vérification partielle par défaut). |
| Message E5 | `phrase.succes.message` | Nous avons vérifié quelques mots. Relisez bien toute votre feuille : chaque mot compte pour récupérer votre coffre. | Honnête ; adapté si vérification complète effectuée. |
| Rappel sécu E5 | `phrase.succes.rappel_secu` | Rappel : TEL ONLINE ne vous demandera jamais cette phrase. | Réutilise le ton de E1. |
| CTA primaire E5 | `phrase.succes.cta_continuer` | Continuer | Libellé « Terminer » possible selon flux (clé distincte si besoin). |
| Lien suite E5 | `phrase.succes.lien_zeroperte` | En savoir plus sur la récupération | → `SPECS-recuperation-zero-perte.md`. |
| Titre E6 | `phrase.report.titre` | Reporter la sauvegarde ? | — |
| Risque E6 | `phrase.report.risque` | Sans cette phrase, personne ne pourra récupérer votre coffre si vous perdez ce téléphone. Pas même TEL ONLINE. | Honnête, sans dramatiser. |
| Rappel E6 | `phrase.report.rappel_info` | Nous vous le rappellerons plus tard. | — |
| Fermer E6 (sortie neutre) | `phrase.report.cta_fermer` | Fermer | Nom accessible : « Fermer sans reporter ». Équivalent single-pointer au glissement. |
| CTA primaire E6 | `phrase.report.cta_maintenant` | Sauvegarder maintenant | Choix sûr = primaire. |
| CTA report E6 | `phrase.report.cta_reporter` | Reporter | En fin, jamais bouton par défaut. |
| Bannière persistante | `phrase.banniere.non_sauvegardee` | Votre phrase de récupération n'est pas encore sauvegardée. | Action associée `phrase.banniere.action` = « Sauvegarder ». |
| Action bannière | `phrase.banniere.action` | Sauvegarder | — |
| Phrase indisponible | `phrase.erreur.indispo` | Votre coffre finit de se préparer. Réessayez dans un instant. | Cas rare (coffre non prêt) ; action de reprise, renvoi `SPECS-onboarding-coffre.md`. +40 % ; RTL. |
| Hors ligne | `commun.horsligne.ok` | Vous êtes hors ligne. Cette étape fonctionne sans connexion. | Message honnête (charte §8). |

---

## 8. Accessibilité — WCAG 2.2 AA (spécifique à ces écrans)

| Critère (n°) | Comment il est satisfait ici |
|---|---|
| **1.4.3 / 1.4.11 Contraste — thème clair** (fond `#FFFFFF`) | Texte sur `color.ink` (16.9:1) ; boutons `color.primary` (blanc 6.6:1) ; encart via `color.warning-surface` + `ink` (14.0:1) ; bordures de champs secrets `color.border-strong` (4.6:1, ≥ 3:1). **Points de masquage `••••` = `color.ink`** : 16.9:1 sur blanc, ~15:1 sur `surface-alt` — ≥ 3:1 vérifié. Le sens « masqué » est **doublé** par le libellé du bouton, pas par la seule couleur (1.4.1). |
| **1.4.3 / 1.4.11 Contraste — thème sombre** (§11.2, fond `#0E1116`, suit le réglage système) | Texte `color.ink` sombre `#F2F4F7` = **17.2:1** (AAA). Bandeau de protection et carte sur `color.surface` `#171B21` avec `ink` `#F2F4F7` = **15.7:1**. Liens / `color.primary` sombre `#6AA6FF` = **7.7:1**. **Points de masquage `color.ink` `#F2F4F7`** = 17.2:1 sur fond / 15.7:1 sur carte — ≥ 3:1. **Contour de focus `color.primary` `#6AA6FF`** = 7.7:1 sur fond — ≥ 3:1. **Toute paire texte/fond ≥ 4.5:1 ; chaque composant / focus ≥ 3:1**, vérifié pour texte, bandeau, points masqués et focus (écrans consultés le soir). |
| **1.4.1 Couleur seule** | Erreur de vérification = icône + texte + bordure, pas seulement rouge. État affiché/masqué = libellé de bouton + icône œil. |
| **1.4.4 / 1.4.10 Zoom & reflow** | Grille de mots à 2 colonnes repasse à 1 colonne ; utilisable à 320 px et 200 % sans défilement à deux dimensions. |
| **1.3.4 Orientation** | Aucun blocage portrait/paysage ; la grille de mots s'adapte. |
| **2.4.3 Ordre de focus** | Ordre : indicateur d'étape → titre → bandeau protection → bouton Afficher → zone phrase → action primaire. En RTL, ordre suivant la lecture droite-à-gauche (propriétés logiques). |
| **2.4.7 / 2.4.11 Focus visible** | Contour ≥ 3px, offset 2px, contraste ≥ 3:1 (clair et sombre) sur tous les contrôles, y compris la bascule Afficher/Masquer, les champs secrets et le bouton « Fermer » de E6. Le focus n'est jamais masqué par le clavier ni la feuille inférieure. |
| **2.4.2 Titre de page** | Chaque écran a un titre unique et parlant (« Votre phrase de récupération », « Vérifions ensemble »…). |
| **2.5.1 / 2.5.7 Gestes** | E6 : le glissement de la poignée a une **alternative visible** (bouton « Fermer », appui sur le voile) et un équivalent **clavier** (Échap). Aucun geste n'est l'unique moyen. |
| **2.5.3 Nom dans le libellé** | Le nom accessible contient le texte visible (« Afficher », « Valider », « Revoir ma phrase », « Fermer »). |
| **2.5.8 / cible tactile** | Toutes cibles ≥ 44px (bascule œil, champs, boutons, poignée, bouton Fermer), espacement ≥ 8px. Norme interne 44px > minimum 24px. |
| **4.1.2 / 1.3.1 Rôles & états** | Bascule Afficher = bouton avec état « pressé » ; indicateur d'étape = texte associé au titre, **annoncé** (« Étape 2 sur 4 ») ; champs secrets = champs de saisie avec label programmé et aide reliée ; compteur de mots en texte, pas en image. |
| **Région live (état)** | Annonces **polies** : « Phrase affichée » / « Phrase masquée », « C'est le bon mot », changement d'étape. Annonce **assertive** pour les évènements de sécurité : « Pour votre sécurité, votre phrase a été masquée » (capture détectée) et « phrase indisponible ». |
| **1.4.13 Contenu au survol/focus** | Tout avertissement conditionnel (copie, si activée) reste affiché, dismissable, ne disparaît pas au déplacement du pointeur. |
| **2.2.1 Timing** | Aucun minuteur sur la lecture ou la vérification. L'action « J'ai noté » n'a pas de limite de temps. (Le vidage du presse-papiers, s'il s'applique, n'est pas un délai d'interaction : il ne fait rien perdre et est annoncé.) |
| **2.3.1 / prefers-reduced-motion** | Transitions afficher/masquer et passage d'écran respectent `prefers-reduced-motion` (fondu réduit à un changement instantané). Aucune animation clignotante. |
| **3.3.7 Aides persistantes** | On ne redemande pas un mot déjà validé ; « Revoir ma phrase » ne réinitialise pas les champs déjà corrects. |
| **3.3.8 Authentification accessible** | La vérification **n'est pas un test cognitif bloquant** : (a) on peut **revoir la phrase** autant de fois que voulu ; (b) **alternative « Choisir dans une liste »** (reconnaissance, pas rappel) couvre le critère **sans exiger de saisie** ; (c) **le collage est interdit** dans les champs de E4 et le presse-papiers est vidé à l'entrée de E4 — l'intégrité repose sur une transcription papier indépendante, pas sur le presse-papiers ; (d) essais illimités, sans énigme ni recopie de code à retenir. |
| **1.3.5 Identification de la saisie** | Champs de vérification : sans autocomplétion d'identifiant, sans type deviné, mode de saisie texte ; collage désactivé. |
| **Lecteur d'écran & fuite audio** | Quand un lecteur d'écran est actif, afficher/annoncer la variante `phrase.affichage.garde_regard_ecoute` (casque recommandé) : la phrase lue à voix haute peut être **entendue** à proximité (haut-parleur / Bluetooth). Chaque mot n'est lu que lorsque la phrase est **affichée** (`mot_position`), sinon « masqué » (`mot_masque`). |

---

## 9. Sécurité & confiance UX

Dérivé de la charte §10.

- **Anti-capture (E2, et E4 pendant « Revoir ma phrase »).** Capture d'écran et enregistrement d'écran **bloqués** sur les écrans affichant la phrase, **sans demande de permission**. Un **message accessible** explique pourquoi (`phrase.affichage.capture_bloquee`) — texte réel, lisible par lecteur d'écran, jamais image de texte. Sur plateformes ne pouvant que **détecter** la capture, masquer immédiatement et afficher/annoncer `phrase.affichage.capture_detectee_masquee`.
- **Masquage par défaut.** La phrase est **masquée à l'ouverture** de E2 et re-masquée à chaque retour sur l'écran (reprise, retour depuis E4). Bouton **Afficher/Masquer** accessible. On ne force jamais l'affichage.
- **Aucun envoi serveur / aucune persistance en clair.** La phrase est **dérivée localement** et **jamais transmise**. Elle n'est écrite **nulle part en clair** (ni fichier, ni cache, ni sauvegarde système). Les champs de saisie de E4 sont comparés **en mémoire** puis effacés.
- **Aucune trace en clair dans les journaux/analytique.** Événements anonymisés et **sans contenu** : on peut journaliser « phrase_affichée », « vérification_réussie », « sauvegarde_reportée » — **jamais** un mot, une position, ni un fragment. Pas de capture d'écran de diagnostic sur ces écrans.
- **Copie de la phrase — désactivée par défaut (secret maître).** Par défaut, **aucune option « Copier »** n'est proposée en E2. L'auto-effacement du presse-papiers **ne protège PAS** contre l'**historique de presse-papiers** (ex. Gboard) ni contre le **presse-papiers cloud / inter-appareils** (Android, Windows Cloud Clipboard, macOS Universal Clipboard), qui peuvent recopier la phrase **hors de l'appareil avant l'effacement** — ce qui contredirait « jamais transmise / aucune trace en clair ». Bloquer la capture tout en autorisant la copie serait incohérent (le presse-papiers est un vecteur d'exfiltration plus puissant). **Si** la copie est maintenue par décision produit/réglementaire (Q7) : (1) désactiver la **synchro cloud** du presse-papiers quand l'OS le permet ; (2) **exclure de l'historique** quand l'OS le permet ; (3) effacement automatique après un court délai (valeur à trancher) **présenté comme une mitigation partielle, pas suffisante** (limites documentées dans `phrase.affichage.copier_avert`) ; (4) annonce en région live ; (5) effacement immédiat si l'app passe en arrière-plan. Aucune suggestion « coller la phrase » ailleurs.
- **Intégrité de la vérification (anti-collage).** E4 valide une **transcription indépendante** du secret : le **collage est interdit** dans les champs de E4 et le **presse-papiers est vidé à l'entrée** de E4. Le statut « sauvegardée » n'est **jamais** accordé sur la base d'un collage. L'alternative accessible « Choisir dans une liste » (reconnaissance) couvre 3.3.8 sans saisie ni collage.
- **Portée honnête du statut « sauvegardée ».** La vérification par défaut ne contrôle que **quelques positions** (QP-1) : une erreur de transcription sur une position **non vérifiée** peut passer inaperçue. En conséquence : (a) la microcopie E5 est **honnête** et n'affirme pas l'intégrité totale (« nous avons vérifié quelques mots — relisez bien toute votre feuille ») ; (b) une **vérification complète optionnelle** (`phrase.verif.cta_complete`) est offerte, avec l'alternative « liste » pour ne pas surcharger Robert/Marc. Le retrait de la bannière signifie « une sauvegarde a été faite et un échantillon vérifié », pas « intégrité garantie ». Risque résiduel documenté en QP-1.
- **Champs secrets.** Masqués par défaut, bascule « afficher » accessible, **pas de suggestion clavier / autocorrection / dictionnaire / cache** (autocapitalisation off, autocorrection off, correcteur off, autocomplétion off). **L'aperçu du dernier caractère saisi est DÉSACTIVÉ par défaut** sur tous les champs secrets ; il ne peut être activé que par **choix explicite** de l'utilisateur (anti surprise-épaule).
- **Anti-hameçonnage.** Message répété (E1 et E5) : « TEL ONLINE ne vous demandera jamais votre phrase par message, appel ou courriel. »
- **Pas d'urgence artificielle / pas de dark pattern.** Report toujours possible (E6) ; dans E6, le choix **sûr** (« Sauvegarder maintenant ») est le bouton primaire, « Reporter » est en fin, jamais pré-sélectionné ; la **fermeture neutre** ne reporte ni ne sauvegarde.
- **Aucune action irréversible sans compréhension.** Rien n'est supprimé ici ; le seul « saut » (report) est réversible et rappelé.

---

## 10. Offline-first & réseau

- **Tout le parcours fonctionne hors ligne.** Pédagogie (E1), génération/affichage (E2), conseils (E3), vérification (E4), succès (E5), report (E6) : **aucun** ne dépend du réseau. La phrase et sa vérification sont **locales**.
- **État de connexion honnête.** Bandeau discret `commun.horsligne.ok` si hors ligne, sans alarmer, puisque rien n'est bloqué.
- **Statut de sauvegarde.** Le passage à « sauvegardée » est un **état local** qui retire la bannière. Si une synchronisation du **statut** (booléen « sauvegardée oui/non ») est prévue entre appareils, elle est **mise en file** et envoyée au retour du réseau — **jamais la phrase**, jamais un fragment. Message : « Statut à jour » au retour réseau, sans contenu.
- **Rappel après report.** Programmé **localement** ; fonctionne sans réseau. Aucune donnée sensible dans la notification (`phrase.banniere.non_sauvegardee`). Si le rappel emprunte une notification système, la **permission de notification** est demandée **au moment utile** (à la sélection de « Reporter »), avec explication — cf. état « Permission requise » de E6, QP-2.
- **Reprise sans perte.** Une coupure ou une mise en arrière-plan ne fait rien perdre : à la reprise, retour à E1 puis ré-affichage masqué de E2 (cf. §3).
- **Pas d'erreur technique brute.** Jamais de message réseau cru ; si une synchro de statut échoue, on reste silencieux côté utilisateur (le statut local suffit).

---

## 11. Performance

- **Contenu 100 % local** : E1–E6 se chargent instantanément (aucun appel réseau bloquant). Cible FCP ≪ 2.5 s, TTI ≪ 4 s largement tenue.
- **Feedback < 100 ms** sur les interactions clés : bascule Afficher/Masquer, focus des champs, validation d'un mot correct, ouverture de la feuille E6.
- **Poids maîtrisé.** Icônes vectorielles légères ; **aucune image lourde** ni police décorative sur ces écrans (police système). Respect de `prefers-reduced-data`.
- **Squelette** en cas d'attente exceptionnelle (dérivation de la phrase en E2) : afficher un **squelette de la grille de mots**, jamais une page blanche (cohérent avec l'état « Chargement » de E2, §5).
- **Fluidité 60 fps** sur les transitions afficher/masquer, réduites si `prefers-reduced-motion`.
- **INP ≤ 200 ms** garanti par des interactions synchrones locales.

---

## 12. Cas limites & erreurs

| Situation | Comportement | Message (FR, langage clair) |
|---|---|---|
| Tentative de capture d'écran (blocage) | Rien n'est capturé ; message d'explication affiché en bandeau. | `phrase.affichage.capture_bloquee` — « La capture d'écran est désactivée sur cet écran pour protéger votre phrase. » |
| Capture seulement détectable (plateforme) | Phrase re-masquée immédiatement + avertissement (région live assertive). | `phrase.affichage.capture_detectee_masquee` — « Pour votre sécurité, votre phrase a été masquée. » |
| App passe en arrière-plan pendant E2/E4 | Phrase re-masquée ; presse-papiers vidé (si copie était activée). | (aucun message intrusif ; état re-masqué au retour) |
| Un mot de vérification ne correspond pas | Rester sur E4 ; erreur reliée au champ ; réessai illimité ; possibilité de revoir la phrase. | `phrase.verif.erreur_mot` — « Ce mot ne correspond pas. Vérifiez sur votre feuille et réessayez. » |
| Tentative de collage dans un champ E4 | Collage refusé (champ inchangé) ; rappel de la source papier. | `phrase.verif.champ_aide` — « Recopiez ce mot depuis votre feuille de papier. Le collage est désactivé. » |
| Personne utilise l'alternative « liste » | Choix multiple équivalent ; même validation ; aucune pénalité. | `phrase.verif.alt_liste` |
| Vérification complète demandée | Toutes les positions vérifiées (saisie ou liste) ; message E5 adapté. | `phrase.verif.cta_complete` |
| Report choisi | Sortie + bannière persistante + rappel local programmé. | `phrase.report.risque` puis `phrase.banniere.non_sauvegardee` |
| Feuille E6 fermée sans choisir (voile / Échap / « Fermer ») | Retour à E1, **rien reporté ni sauvegardé**, aucun rappel programmé, bannière inchangée. | `phrase.report.cta_fermer` — « Fermer » |
| Reprise après report | Bannière → E1 → E2 (masqué). Rien n'est perdu. | `phrase.banniere.action` — « Sauvegarder » |
| Hors ligne | Tout fonctionne ; bandeau honnête. | `commun.horsligne.ok` |
| Phrase indisponible (cas rare : coffre non prêt) | Erreur avec action de reprise ; on ne bloque pas dans un cul-de-sac ; renvoi vers `SPECS-onboarding-coffre.md`. | `phrase.erreur.indispo` — « Votre coffre finit de se préparer. Réessayez dans un instant. » |
| Copie autorisée (Q7) puis délai écoulé | Presse-papiers vidé ; annonce polie (mitigation partielle, cf. §9). | `phrase.affichage.copier_efface` |
| Lecteur d'écran actif sur E2 | Rappel regard + écoute ; chaque mot lisible via `phrase.affichage.mot_position` uniquement quand la phrase est **affichée** ; masqué sinon. | `phrase.affichage.garde_regard_ecoute` ; « Mot n° {position} : {mot} » / `mot_masque` |

---

## 13. Critères d'acceptation (testables)

- [ ] Pour **chaque écran**, les **7 états** (§13 charte) sont **spécifiés ou justifiés N/A** (voir matrice par écran §5) ; en particulier E2 spécifie l'état **Chargement** (squelette de dérivation, cohérent avec §11).
- [ ] Un **indicateur d'étape accessible** (« Étape X sur Y », texte + annonce lecteur d'écran) est présent sur **E1–E4**.
- [ ] Un **accès au choix de langue** est présent sur **E1** (charte §6) et disponible ensuite via Réglages.
- [ ] Une **seule affordance d'aide** (clé unique) est exposée dans l'en-tête ; pas de doublon non traçable.
- [ ] E1 affiche l'**avertissement anti-hameçonnage** avant tout affichage de la phrase, et il est **répété en E5** ; aucune **capitale d'emphase** (emphase par le gras).
- [ ] En E2, la phrase est **masquée par défaut** et **re-masquée** à chaque retour/reprise.
- [ ] La **capture d'écran est bloquée** sur E2 (et E4 quand la phrase est revue), avec un **message accessible** lisible par lecteur d'écran ; détection seule → re-masquage + avertissement assertif.
- [ ] La **copie de la phrase est désactivée par défaut** ; si activée (Q7) : synchro cloud et historique du presse-papiers désactivés quand l'OS le permet, l'auto-effacement **documenté comme mitigation partielle** (pas suffisante), avertissement explicite, annonce, vidage à la mise en arrière-plan.
- [ ] Le **collage est interdit** dans les champs de E4 et le **presse-papiers est vidé à l'entrée** de E4 ; le statut « sauvegardée » ne peut **jamais** résulter d'un collage.
- [ ] La phrase **n'est jamais envoyée à un serveur** ni **écrite en clair** (fichier, cache, sauvegarde, journal, analytique) — vérifié par revue technique.
- [ ] Aucune entrée de journal/analytique ne contient un mot, un fragment ou une position.
- [ ] Les **champs secrets** (E2 bascule, E4) sont sans autocorrection/suggestion/dictionnaire/cache, et l'**aperçu du dernier caractère est désactivé par défaut** (activable seulement sur choix explicite).
- [ ] La **vérification** confirme quelques mots par défaut, **n'est pas bloquante cognitivement** : revoir la phrase possible, **alternative « liste »** présente, **collage interdit** en E4, essais illimités, aucun minuteur (3.3.8) ; une **vérification complète optionnelle** est disponible.
- [ ] L'écran **E5 ne sur-promet pas** : sa microcopie indique que seuls quelques mots ont été vérifiés et invite à relire toute la feuille.
- [ ] Les **contrastes sont vérifiés en thème clair ET sombre** (§8) pour texte, bandeau de protection, points de masquage et contour de focus : texte ≥ 4.5:1, composants/focus ≥ 3:1 ; le **token des points de masquage** est spécifié (`color.ink`) avec ratios.
- [ ] Toutes les cibles tactiles sont **≥ 44px**, espacement ≥ 8px, actions primaires dans le **tiers inférieur**.
- [ ] **Focus visible ≥ 3px** et **ordre de focus logique** sur tous les écrans, y compris la feuille E6 (focus déplacé dedans, restitué à la fermeture).
- [ ] E6 offre une **alternative visible au glissement** (bouton « Fermer », appui sur le voile) **et** une fermeture **clavier** (Échap) ; la **fermeture neutre** ne reporte ni ne sauvegarde (retour E1, état inchangé).
- [ ] Un **rappel regard + écoute** (casque) est présenté/annoncé quand un lecteur d'écran est actif.
- [ ] **Toute chaîne** possède une **clé i18n en ASCII snake_case**, testée en **FR / EN / AR (RTL)** et avec **+40 %** de longueur, **sans concaténation**, pluriels et **durées en ICU** (`phrase.intro.duree` paramétrée).
- [ ] Les deux chaînes `phrase.affichage.capture_detectee_masquee` et `phrase.erreur.indispo` figurent au tableau §7 avec leurs notes de traduction.
- [ ] La **mise en page RTL** est correcte (numéros de mots au format homogène « Mot n° {position} », indicateur d'étape, flèches en propriétés logiques).
- [ ] **Report** disponible avec **rappel explicite du risque** ; « Sauvegarder maintenant » est le bouton **primaire**, « Reporter » en fin (pas de dark pattern).
- [ ] Un **rappel ultérieur** est programmé après report et **fonctionne hors ligne**, sans donnée sensible ; la permission de notification, si requise, est demandée **au moment utile**.
- [ ] **Tout le parcours fonctionne hors ligne** ; les messages réseau sont honnêtes et sans jargon.
- [ ] **Zéro jargon** à l'écran (validé contre le glossaire §16 de la charte).
- [ ] `prefers-reduced-motion` respecté ; aucune animation clignotante.

---

## 14. Métriques de succès (KPIs UX)

> Mesures **anonymisées, sans contenu** (cf. §9).

1. **Taux de complétion de la sauvegarde** = personnes atteignant E5 / personnes entrant en E1. **Cible ≥ 70 %** au premier passage (le report est acceptable, pas un échec).
2. **Taux de vérification réussie au 1er essai** (E4) — proxy de la clarté de E2/E3 et de la qualité de copie. **Attention : mesure uniquement les positions échantillonnées** (2–3 mots par défaut) ; ce n'est **pas** une garantie d'intégrité de toute la phrase (cf. §9, QP-1). **Cible ≥ 85 %** ; un taux bas signale une consigne à revoir.
3. **Taux d'usage de la vérification complète optionnelle** (`Vérifier toute ma phrase`) — indicateur du besoin de réassurance et de la robustesse réelle de la sauvegarde.
4. **Temps médian E1 → E5** (hors report). **Cible ≤ 3 min** (cohérent avec la promesse « au calme »).
5. **Taux de report puis reprise** = personnes reportant qui reviennent sauvegarder après rappel. **Cible ≥ 50 %** — mesure l'efficacité du rappel honnête.
6. **Taux d'usage de l'alternative accessible** (« Choisir dans une liste ») et taux d'abandon en E4 — surveiller pour garantir que la vérification n'exclut personne.

---

## 15. Questions ouvertes

- **`Q2` (charte)** — Longueur de la phrase (**12 vs 24 mots**) et **liste de mots localisée** (FR/EN/ES/AR). Impacte le compteur (déjà paramétré ICU) et la grille.
- **`Q7` (charte)** — Contraintes réglementaires (RGPD, mentions) sur l'affichage/stockage d'un secret ; **copie presse-papiers autorisée ou interdite** ? **Par défaut interdite** dans cette SPEC (§9). Si autorisée : **délai d'effacement** exact **et** confirmation que la synchro cloud/historique peut être neutralisée sur les plateformes visées.
- **QP-1** — **Combien de mots** re-vérifier en E4 (ex. 2–3) et à quelles positions (aléatoires ?), pour équilibrer sécurité et charge cognitive (compatibilité Robert/Marc). **Documenter le risque de transcription silencieuse** sur les positions non vérifiées (cf. §9) ; arbitrer entre échantillon + microcopie honnête + vérification complète optionnelle (choix actuel) et vérification intégrale par défaut.
- **QP-2** — **Cadence des rappels** après report (délai initial, répétition, arrêt), **canal** (in-app vs notification système) et **moment de demande de la permission** de notification.
- **QP-3** — **Synchronisation du statut** « sauvegardée » entre appareils : souhaitée ? Confirmer qu'aucun fragment de phrase ne transite (`HYP-3` récupération « zéro perte »).
- **QP-4** — Comportement exact sur plateformes **ne pouvant pas bloquer** la capture (détection seule) : re-masquage + avertissement suffisent-ils ?
- **QP-5** — Articulation avec `SPECS-recuperation-zero-perte.md` : cette phrase est **un** facteur ; faut-il enchaîner directement vers les autres facteurs après E5 ?

*Lien de parcours : entrée depuis `SPECS-onboarding-coffre.md` ; suite possible vers `SPECS-recuperation-zero-perte.md`. Aucun contenu de ces SPECS n'est dupliqué ici.*

---

## Alignement modèle de menace (T-010)

> Ajout T-010 : traçage explicite de cet écran (le plus sensible) vers `../securite/MODELE-MENACE.md`. **SEC-004 (P0)** — phishing de la phrase — est le constat central. On **spécifie la mesure UX** ; les mécanismes sont **routés** (R12). **WCAG 2.2 AA maintenu**. Traçabilité : `ALIGNEMENT-MENACE.md`.

### Capture d'écran (par plateforme) — SEC-025 (P1) · VETO-V14 · VETO-V6
- Déjà spécifié (affichage E2, revue en E4). **Tracé** vers SEC-025/VETO-V14 : **Android** bloque capture/enregistrement/aperçu multitâche ; **iOS** ne peut que **détecter** → **re-masquage immédiat** + avertissement, et **occultation de l'aperçu multitâche**.
- **Aucun SDK tiers** (session-replay/crash) sur l'écran de phrase (VETO-V6).

### Overlay / tapjacking — SEC-004 (P0) · angle mort D.1 (partage d'écran / RAT)
- **Ajout T-010 :** pendant l'affichage (E2) et la vérification (E4), **détection d'une sur-couche (overlay) ou d'un partage/enregistrement d'écran** → **masquage immédiat** de la phrase + avertissement.
- **Anti-tapjacking :** les **appuis obscurcis sont ignorés** sur les commandes « Afficher » et de validation (empêche un overlay de capter l'action ou de faire révéler la phrase à l'insu de l'utilisateur).

### Phishing de la seed — SEC-004 (P0) · VETO-V15 · VETO-V11
- La phrase **n'est JAMAIS saisie en ligne** ni **demandée par le support** ni **envoyée au serveur** ; la **vérification (E4) est 100 % locale** (re-dérivation cliente — **aucune API n'accepte la phrase**).
- **Pédagogie anti-phishing persistante** : « Personne — pas même TEL ONLINE — ne vous demandera jamais votre phrase. »
- **Notifications hors-bande signées** sur toute opération de récupération/rotation ultérieure (renvoi `SPECS-recuperation-zero-perte.md`) ; **jamais de SMS/appel** comme canal de confiance (VETO-V11).

### Autofill / presse-papiers — SEC-026 (P1) · VETO-V14 · angle mort D.1 (autofill)
- Déjà spécifié : **copie désactivée par défaut**, presse-papiers **cloud/historique/inter-appareils exclus**, clavier sécurisé, aperçu du dernier caractère désactivé.
- **Ajout T-010 :** le **champ de saisie** de vérification (E4) est **exclu de l'autofill** (aucune proposition de remplissage de la phrase), **pas de fuzzy-match** ; le collage y est **interdit** (l'intégrité repose sur une transcription papier indépendante).

### Accessibilité maintenue (WCAG 2.2 AA)
- **Divulgation privée** au lecteur d'écran déjà spécifiée (avertissement « casque », lecture mot à mot à la demande, pas de région live sur le secret) — cohérente avec SEC-025.
- L'avertissement d'overlay est **annoncé en région live assertive** ; l'alternative de vérification **« choisir dans une liste »** (reconnaissance, conforme **3.3.8**) reste disponible.

### Traçabilité (extrait)
| SEC-### / VETO | Mesure UX ici | Statut |
|---|---|---|
| **SEC-004 (P0)** · V15 · V11 | Jamais en ligne, vérif locale, pédagogie, notif hors-bande | ✅ + tracé |
| SEC-025 · V14 · V6 | Anti-capture par plateforme, occultation, pas de SDK tiers | ✅ + tracé |
| SEC-004 · D.1 | Détection overlay/partage d'écran + anti-tapjacking | ➕ T-010 |
| SEC-026 · V14 · D.1 | Copie off, autofill exclu, clavier sécurisé, pas de fuzzy-match | ✅ + ➕ |
