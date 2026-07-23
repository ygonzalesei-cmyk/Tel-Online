# SPEC — Abonnement (offre gratuite / offre premium)

## 0. Métadonnées

| Champ | Valeur |
|---|---|
| **Titre** | Abonnement — offre gratuite & offre premium |
| **Identifiant** | `SPECS-abonnement-freemium.md` |
| **Statut** | **v0.2 — proposition révisée** (corrections post-audit ; pour revue en atelier) |
| **Mission** | Spécifier le parcours d'abonnement : modèle gratuit + premium (souscription, gestion, résiliation) |
| **Écrans couverts** | (1) Présentation des offres · (2) Détail premium · (3) Passage à premium (récapitulatif + consentement) · (4) Confirmation d'achat · (5) Gestion de l'abonnement (voir / changer / résilier) · (6) États particuliers (limites atteintes, échec de paiement, hors ligne) |
| **Charte de référence** | `CHARTE-UX.md` (v0.1) — en cas de conflit, la charte fait foi |
| **SPEC liée** | `SPECS-esim-secours.md` — **source unique** des quotas de data de la carte SIM numérique de secours. **Dépendance bloquante non résolue** (voir encadré ci-dessous). La présente SPEC **ne redéfinit pas** ces quotas, elle les **affiche**. |

> ### ⛔ Dépendance bloquante — à lever avant gel des écrans 1 et 2
>
> `SPECS-esim-secours.md` est déclarée **source unique** des quotas de data et du périmètre gratuit vs premium (écrans 1, 2, 4 ; §3 ; §10), **mais ce fichier n'existe pas encore** dans le dépôt (`orchestration/agents/design/` ne contient à ce jour que `CHARTE-UX.md`). Tant que ce fichier et les réponses `Q4`/`Q5` (charte) ne sont pas fournis :
> - les valeurs `{capacité}`, `{quota}`, `{data}` des cartes comparées **restent des placeholders non résolus** ;
> - les critères d'acceptation « mêmes rubriques, mêmes valeurs » (§13) **ne sont pas testables sur données réelles** ;
> - les **écrans 1 et 2 sont gelés** (structure validée, valeurs à confirmer) jusqu'à réception de `SPECS-esim-secours.md` **et** de `Q4`/`Q5`.
>
> **Jeu de valeurs de test provisoires** (⚠️ **provisoire — à confirmer**, non contractuel, sert uniquement au prototypage et aux tests d'expansion i18n ; à remplacer par `SPECS-esim-secours.md`) :
>
> | Rubrique | Offre gratuite (provisoire) | Offre premium (provisoire) |
> |---|---|---|
> | Capacité du coffre | 1 Go | 50 Go |
> | Data de la carte SIM numérique de secours | incluse à minima (voir `Q4`) | quota étendu (voir `Q4`) |
> | Nombre d'appareils | 1 | 5 |
> | Prix TTC | — | 4,99 €/mois · 49,90 €/an |
>
> Ces chiffres **ne doivent pas apparaître en production** : ils sont étiquetés « provisoire » dans les maquettes de prototypage.

**Hypothèses produit utilisées** (à confirmer par le pôle Produit — cf. charte §1 et §17). Numérotation **alignée sur la convention de la charte** (poursuite en `HYP-6`, `HYP-7`…) :

| Réf. | Hypothèse |
|---|---|
| `HYP-5` | Modèle économique : socle **gratuit** + options **premium** (capacité, data de la carte SIM de secours, fonctions avancées). *(rappel charte)* |
| `HYP-6` | Le paiement est encaissé par un **prestataire de paiement** intégré ; TEL ONLINE ne stocke pas les données de carte bancaire (elles ne transitent pas par nos journaux — cf. §9). |
| `HYP-7` | Périodicité premium : **mensuelle** et **annuelle** (l'annuelle affichée avec économie réelle, sans fausse urgence). À confirmer (`Q8`). |
| `HYP-8` | La résiliation est **immédiatement disponible en ligne**, sans étape hors-app, avec **date d'effet = date de la demande** ; l'accès premium reste actif jusqu'à la fin de la période déjà payée (pas de remboursement au prorata sauf obligation légale). À confirmer (`Q9`). |

> Le quota de data de la carte SIM de secours et son inclusion (gratuit vs premium) **ne font pas l'objet d'une hypothèse dupliquée** : ils relèvent exclusivement de `SPECS-esim-secours.md` et des questions `Q4`/`Q5` de la charte, pour éviter deux sources concurrentes sur la même question.

---

## 1. Objectif & enjeux

**Rôle du parcours.** Permettre à toute personne de **comprendre la différence** entre l'offre gratuite et l'offre premium, de **choisir librement** de passer au premium, d'en **profiter immédiatement**, puis de **gérer et résilier** son abonnement aussi simplement qu'elle l'a souscrit.

**Pourquoi c'est sensible.** Ce parcours touche les trois zones critiques du produit :

- **Argent.** On demande un **engagement financier récurrent**. La transparence des prix (TTC, périodicité, ce qui est inclus) et l'**absence de dark patterns** sont des exigences non négociables (charte §2.10, §10). Une case pré-cochée, une fausse urgence ou une résiliation compliquée seraient des **régressions bloquantes**.
- **Secrets & identité.** L'offre conditionne des capacités liées au coffre (capacité de stockage, data de la carte SIM numérique de secours qui sert de **canal de récupération**). Un échec de paiement ou une confusion sur ce qui est inclus ne doit **jamais** laisser croire à l'utilisateur qu'il risque de perdre ses secrets. Le coffre et la phrase de récupération restent la propriété de l'utilisateur, **quelle que soit l'offre**.
- **Confiance.** Le moment du paiement est celui où la sobriété et l'honnêteté de l'interface se prouvent. La confiance gagnée ici rejaillit sur tout le produit.

**Principe directeur du parcours :** *résilier doit être aussi facile que souscrire* (charte §2.10 ; exigence renforcée par le droit de la consommation FR, « résiliation en trois clics »). En corollaire, une résiliation **n'est jamais laissée dans un état ambigu** pouvant entraîner un prélèvement non voulu (voir §5 écran 5, §10, `Q9`).

---

## 2. Personas & cas d'usage concernés

Personas de la charte §3. Ce parcours doit être validé pour **Robert, Amina et Marc** (principe charte §3), pas seulement pour Camille.

| Persona | Pourquoi concerné | Ce que le parcours doit garantir |
|---|---|---|
| **Camille (pragmatique)** | Décide vite, veut comprendre l'offre en un coup d'œil et payer sans friction. | Comparaison lisible en un écran, récapitulatif clair, paiement rapide **sans piège**. |
| **Robert (prudent)** | Peur de « s'engager sans comprendre » ou de payer par erreur. Craint les prélèvements récurrents. | Prix TTC et périodicité explicites, **aucune case pré-cochée**, confirmation compréhensible, résiliation visible, rassurante et **effective à la date demandée**. |
| **Amina (multilingue, RTL)** | Doit voir **prix, devise et dates localisés** ; lit en arabe droite-à-gauche. | Devise/format selon la locale, aucune concaténation (« 4,99 € / mois » assemblé par ICU, période gérée par `select`), mise en page miroir correcte. |
| **Léa (nomade, réseau instable)** | Peut consulter les offres ou tenter un paiement / une résiliation hors ligne. | Comparaison consultable **hors ligne**, paiement jamais lancé sans réseau, résiliation **prise en compte à la date de la demande** sans double facturation. |
| **Marc (lecteur d'écran)** | Doit percevoir la différence gratuit/premium et l'état du paiement **sans dépendre de la couleur**. | Étiquettes « Gratuit »/« Premium » avec texte, annonces d'état (succès/erreur/hors ligne) en région live, ordre et **gestion de focus** logiques (modales comprises). |

**Cas d'usage couverts :** découvrir les offres · comparer · passer au premium · échouer un paiement et réessayer · atteindre une limite de l'offre gratuite · consulter l'offre en cours · **consulter l'écran 1 en étant déjà premium** · changer d'offre (mensuel↔annuel, monter/descendre) · **résilier**.

---

## 3. Pré-requis, points d'entrée et de sortie

**Pré-requis (doivent exister avant).**
- Un compte / coffre actif (parcours `SPECS-onboarding-coffre.md`). L'abonnement **n'exige pas** que le coffre soit finalisé, mais un moyen d'identifier la personne facturée.
- Le **catalogue d'offres** (libellés, prix TTC par locale/devise, ce qui est inclus) chargé depuis le serveur, avec **repli sur la dernière version connue en cache** (offline-first, §10).
- Les **quotas de data** proviennent de `SPECS-esim-secours.md` (**dépendance bloquante**, cf. §0 et `Q4`/`Q5`). La présente SPEC les **affiche uniquement**.

**Points d'entrée.**
- Écran d'accueil / réglages : bouton « Voir les offres ».
- **En contexte** : depuis un état « limite de l'offre gratuite atteinte » (§5, écran État-A) — présenté sans culpabiliser, jamais en pop-up bloquant surgissant sans raison.
- Depuis un rappel légitime (ex. e-mail de fin d'essai) menant à l'écran de gestion.

**Points de sortie.**
- **Succès** : confirmation d'achat → accès aux nouvelles fonctions (écran 4).
- **Abandon** : retour à l'écran précédent **sans perte** ni relance culpabilisante.
- **Gestion** : écran 5 (voir / changer / résilier).

**Reprise après interruption.** À tout moment (coupure réseau, appel entrant, fermeture app), l'utilisateur **revient à l'étape exacte** : l'offre sélectionnée et le récapitulatif sont conservés localement (charte §2.5, §8). Un paiement **en cours** n'est jamais dupliqué au retour (idempotence côté prestataire — cf. §12).

---

## 4. Parcours utilisateur (flux)

Étapes numérotées. Chaque étape autorise un **retour arrière sans perte** (`←`).

1. **Entrée** → écran **(1) Présentation des offres**. L'utilisateur voit côte à côte l'offre gratuite (actuelle) et l'offre premium. *(Si l'utilisateur est déjà premium, voir l'état « déjà premium » de l'écran 1.)*
2. Depuis (1), deux chemins :
   - 2a. « **En savoir plus sur premium** » → écran **(2) Détail premium**.
   - 2b. « **Choisir premium** » → écran **(3) Passage à premium** directement.
3. Depuis (2) Détail premium → « **Choisir cette offre** » → écran **(3) Passage à premium**. `←` retourne à (1).
4. **(3) Passage à premium** : récapitulatif (offre, périodicité, **prix TTC**, prochaine date de renouvellement), choix mensuel/annuel, **consentement explicite non pré-coché**, moyen de paiement. `←` retourne à (1) ou (2) sans perte.
5. Validation du paiement :
   - 5a. **Succès** → écran **(4) Confirmation d'achat** → accès aux nouvelles fonctions.
   - 5b. **Échec** → état **Erreur** de l'écran (3) / renvoi vers **État-B (échec de paiement)** : cause + solution + « Réessayer ». Aucune double tentative silencieuse.
   - 5c. **Hors ligne** → paiement **non lancé** ; message honnête (§10). Le choix reste mémorisé.
6. Depuis (4), l'utilisateur peut « **Découvrir mes nouvelles fonctions** » ou « **Terminé** ».
7. À tout moment (réglages) → écran **(5) Gestion de l'abonnement** : voir l'offre en cours, **changer** (mensuel↔annuel, monter/descendre) ou **résilier**.
   - 7a. **Changer** → écran (3) en mode « changement » (récapitulatif du delta + date d'effet). Un changement **impliquant un débit** suit la règle de paiement (réseau requis, §10).
   - 7b. **Résilier** → sous-parcours de résiliation : **1 confirmation** décrivant la conséquence (date de fin d'accès), **sans rétention agressive** ni obstacle. La confirmation **enregistre la date de la demande comme date d'effet** (§5, §10) ; l'abonnement est marqué « résilié à échéance ».
8. **États transverses** pouvant surgir dans le flux : **limites de l'offre gratuite atteintes** (État-A), **échec de paiement** (État-B), **hors ligne** (partout). Aucun n'est un cul-de-sac.

**Retours arrière garantis :** (2)→(1), (3)→(2)/(1), (4)→accueil, (5)→réglages. La résiliation confirmée est **réversible avant l'échéance** (« Réactiver mon offre premium »).

---

## 5. Écrans & états

> Règle transverse : **une seule action primaire par écran** (charte §12). Les étiquettes « Gratuit » / « Premium » ne portent **jamais** le sens par la seule couleur (icône + texte, charte §5.1). **Aucune** case pré-cochée, **aucune** urgence artificielle (charte §10). Tout bouton primaire désactivé reste **focusable** (`aria-disabled="true"`) et sa **raison est reliée** au contrôle (`aria-describedby`), afin d'être annoncée et pas seulement affichée (voir §8).

### Écran 1 — Présentation des offres (gratuite vs premium)

- **But.** Comprendre en un coup d'œil la différence entre les deux offres et décider.
- **Éléments.** Titre « Nos offres » ; **badge « Offre en cours »** sur la carte correspondant à l'offre active de l'utilisateur ; deux cartes comparables (mêmes rubriques, même ordre) : *capacité du coffre*, *carte SIM numérique de secours (data)*, *fonctions* ; pour chaque rubrique, la valeur gratuite **et** premium alignées ; **prix premium TTC + périodicité** visible sur la carte premium ; lien « Comparer en détail » (tableau accessible) ; **aucun compte à rebours, aucun « plus que X places »**.
- **Action primaire (contextuelle).**
  - Utilisateur **sur l'offre gratuite** : « Choisir premium ».
  - Utilisateur **déjà premium** (cas explicitement couvert) : **pas de CTA d'upsell** ; le badge « Offre en cours » est porté par la **carte premium** ; l'action primaire devient neutre « Gérer mon abonnement » (→ écran 5). Aucune incitation commerciale n'est présentée à un utilisateur déjà premium.
- **Actions secondaires.** « En savoir plus » (→ écran 2) ; « Rester sur l'offre gratuite » (fermeture non culpabilisante, masquée si déjà premium) ; « Comparer en détail ».
- **États applicables :**
  - **Chargement** : squelette des deux cartes (pas de spinner nu).
  - **Vide** : sans objet (il y a toujours ≥ 1 offre) → si le catalogue est indisponible, basculer en **Hors ligne/Erreur**.
  - **Contenu** : nominal, deux offres comparées (variante « déjà premium » ci-dessus).
  - **Erreur** : « Impossible d'afficher les offres pour l'instant. » (`abo.offres.erreur`) + « Réessayer » (`abo.cta.reessayer`). La navigation reste possible.
  - **Hors ligne** : affiche la **dernière version connue** du catalogue avec mention « Tarifs vus le {date} — reconnectez-vous pour confirmer » ; bouton « Choisir premium » **désactivé (`aria-disabled`) avec raison reliée** (le paiement exige le réseau) ; annonce polie du passage hors ligne en région live (voir §8).
  - **Succès** : sans objet ici.
  - **Permission requise** : sans objet.

### Écran 2 — Détail premium

- **But.** Détailler **précisément** ce qu'inclut l'offre premium avant tout engagement.
- **Éléments.** Titre « Offre premium » ; **prix TTC en gros**, mention « toutes taxes comprises » ; **sélecteur périodicité** (mensuel / annuel) avec, pour l'annuel, l'économie réelle exprimée en clair (« soit {prixMois}/mois, {économie} par an ») **sans barrer un faux prix** ; liste **de ce qui est inclus** : capacité du coffre, **data de la carte SIM numérique de secours** (valeur issue de `SPECS-esim-secours.md`, avec lien « Comment fonctionne la data de secours »), fonctions avancées ; mention « Sans engagement — résiliable à tout moment » ; rappel « Vos secrets restent à vous, sur votre appareil, quelle que soit l'offre ».
- **Action primaire.** « Choisir cette offre ».
- **Actions secondaires.** Bascule mensuel/annuel ; « Comparer avec l'offre gratuite » (→ écran 1) ; retour `←`.
- **États :**
  - **Chargement** : squelette prix + liste.
  - **Vide** : sans objet.
  - **Contenu** : nominal.
  - **Erreur** : « Détails indisponibles pour l'instant. » (`abo.premium.erreur`) + « Réessayer » (`abo.cta.reessayer`).
  - **Hors ligne** : détails en cache + « Choisir cette offre » **désactivé (`aria-disabled`) avec raison reliée** ; annonce polie du passage hors ligne.
  - **Succès** : sans objet.
  - **Permission requise** : sans objet.

### Écran 3 — Passage à premium (récapitulatif + consentement)

- **But.** Récapituler **avant paiement** et recueillir un **consentement libre et éclairé** (prévention des erreurs financières, WCAG 3.3.4 — voir §8).
- **Éléments.** Titre « Récapitulatif » ; ligne offre (premium, périodicité choisie) ; **prix TTC** ; **« Prélevé aujourd'hui : {prix} »** puis **« Renouvellement automatique le {date}, {prix}/période — modifiable ou résiliable à tout moment »** (info neutre, pas une menace) ; moyen de paiement (via prestataire) ; **case à cocher NON pré-cochée** : « J'accepte l'abonnement premium et le renouvellement automatique décrits ci-dessus » reliée aux **conditions** (lien) ; **rappel anti-hameçonnage en texte statique persistant** sous le contrôle (§9, non superposé, non dépendant du survol). Le bouton primaire reste **désactivé tant que le consentement n'est pas donné**, avec **texte d'aide persistant relié** par `aria-describedby` (pas seulement grisé).
- **Action primaire.** « Confirmer et payer {prix} » (le montant est **dans** le libellé, assemblé par ICU, jamais concaténé).
- **Actions secondaires.** Changer de périodicité ; changer de moyen de paiement ; retour `←` (sans perte).
- **États :**
  - **Chargement** : squelette du récapitulatif ; bouton désactivé (raison reliée).
  - **Vide** : sans objet.
  - **Contenu** : nominal.
  - **Erreur** (paiement refusé / interrompu) : bannière **assertive** « Le paiement n'a pas abouti. » + cause connue + « Réessayer » ; **aucun** débit fantôme (§12). Renvoi possible vers **État-B**.
  - **Hors ligne** : bouton « Confirmer et payer » **désactivé (`aria-disabled`, raison reliée)** ; message « Le paiement a besoin d'une connexion. Votre choix est gardé. » ; annonce polie du passage hors ligne. Rien n'est mis en file côté débit (on **ne débite jamais** en différé à l'insu de l'utilisateur, §10).
  - **Succès** : transition vers écran 4 (respecte `prefers-reduced-motion`).
  - **Permission requise** : si le prestataire exige une authentification forte (3-D Secure), l'écran **annonce la redirection** (« Votre banque va vous demander de confirmer ») **avant** de rediriger, et garantit un **retour propre dans l'app** (voir §8 3.3.8 et `Q11`).

### Écran 4 — Confirmation d'achat + accès aux nouvelles fonctions

- **But.** Confirmer clairement et **ouvrir immédiatement** l'accès aux nouvelles capacités.
- **Éléments.** Icône + titre « C'est fait — vous êtes en offre premium » ; récapitulatif : offre, **montant TTC payé**, **prochaine date de renouvellement**, **où trouver la facture** ; liste courte « Ce que vous pouvez faire maintenant » (capacité augmentée, data de secours, fonctions) ; rappel « Résiliable à tout moment depuis Réglages ».
- **Action primaire.** « Découvrir mes nouvelles fonctions ».
- **Actions secondaires.** « Voir ma facture » ; « Terminé » (retour accueil).
- **États :**
  - **Chargement** : bref squelette pendant l'activation des fonctions ; message « Activation en cours… » (`abo.confirm.activationEnCours`).
  - **Vide** : sans objet.
  - **Contenu** : sans objet (l'état nominal **est** le succès).
  - **Erreur** : cas rare « Paiement reçu mais activation en attente » → « Votre paiement est bien reçu. L'activation peut prendre un instant — nous vous prévenons dès que c'est prêt. » (`abo.confirm.activationAttente`) (jamais alarmant ; pas de re-paiement).
  - **Hors ligne** : la confirmation déjà obtenue s'affiche depuis le cache ; « Facture disponible au retour du réseau » (`abo.confirm.factureAuRetour`).
  - **Succès** : état nominal — région live polie « Abonnement premium activé » (`abo.a11y.succes`).
  - **Permission requise** : sans objet.

### Écran 5 — Gestion de l'abonnement (voir / changer / résilier)

- **But.** Voir l'offre en cours, la **changer**, et **résilier aussi facilement que souscrire**.
- **Éléments.** Titre « Mon abonnement » ; **offre en cours** (nom, prix TTC, périodicité) ; **prochaine date de renouvellement** ou **« Se termine le {date} »** si résilié ; moyen de paiement (masqué, §9) ; **factures** (liste, téléchargement) ; trois actions de même poids visuel : *Changer d'offre*, *Changer de moyen de paiement*, **« Résilier mon abonnement »** (visible, non enterré, contraste suffisant).
- **Action primaire.** Contextuelle : si offre gratuite → « Passer à premium » ; si premium → **pas** de primaire unique commerciale mais un accès clair à **chaque** action (gérer = neutre).
- **Actions secondaires.** Voir / télécharger une facture ; contacter l'aide.
- **Sous-parcours résiliation (avec gestion de focus, §8 2.1.2).** « Résilier » ouvre **une** modale de confirmation :
  - **À l'ouverture** : le focus est **déplacé dans la modale** (titre ou premier contrôle) et **piégé** tant qu'elle est ouverte ; la modale est **fermable au clavier** (Échap → équivaut à « Garder premium ») ; à la fermeture, le focus est **restitué au déclencheur « Résilier »**.
  - **Contenu** : conséquence explicite (« Votre offre premium restera active jusqu'au {date}. Après, vous repassez à l'offre gratuite. Vos secrets restent à vous. »), **sans** offre de rétention imposée, **sans** étape supplémentaire artificielle.
  - Bouton destructif « Résilier » à droite/fin, **jamais** bouton par défaut ; focus par défaut sur « Garder premium » (non destructif) (charte §12).
  - **Enregistrement** : la confirmation **horodate la demande** ; **la date de la demande est la date d'effet légale** de la résiliation (voir §10 et `Q9`). Après résiliation : bannière « Offre premium active jusqu'au {date} » (`abo.resil.banniereActive`) + « Réactiver mon offre premium ».
- **États :**
  - **Chargement** : squelette de la fiche.
  - **Vide** : si aucun abonnement premium → montre l'offre gratuite en cours + « Passer à premium » (pas un cul-de-sac).
  - **Contenu** : nominal.
  - **Erreur** : « Impossible d'afficher votre abonnement. » (`abo.gestion.erreur`) + « Réessayer » (`abo.cta.reessayer`) ; la résiliation reste tentable / un canal d'aide est offert (on ne **bloque jamais** une résiliation par une erreur d'affichage).
  - **Hors ligne** : affiche l'état connu en cache ; annonce polie du passage hors ligne.
    - **Résiliation hors ligne** : **jamais refusée ni ambiguë**. Elle est **enregistrée localement de façon durable avec la date de la demande comme date d'effet** et affichée sans ambiguïté : « Résiliation prise en compte le {date}. Elle reste valable même sans reconnexion ; nous la confirmons dès le retour du réseau. » (`abo.resil.horsLigne`). Elle est **confirmée de façon idempotente** à la reconnexion, sans jamais laisser un prélèvement survenir sur une résiliation déjà demandée. *(Position de conception par défaut — à valider juridiquement `Q9`/`Q7` ; à défaut de validation, l'alternative est de refuser la résiliation hors ligne avec explication honnête et renvoi vers un canal joignable, mais jamais un état intermédiaire pouvant entraîner un débit.)*
    - **Changement d'offre hors ligne** : un changement **sans débit** (ex. bascule descendante prenant effet à échéance) peut être mis en file « Sera appliqué au retour du réseau » (`abo.offline.misEnFile`) **si et seulement si** l'opération est confirmée et non ambiguë ; un changement **impliquant un débit** suit la règle de paiement (désactivé avec raison reliée, aucun débit en file).
  - **Succès** : après changement → région live « Modification enregistrée » (`abo.a11y.modifie`) ; après résiliation → région live « Résiliation enregistrée » (`abo.a11y.resilie`).
  - **Permission requise** : re-authentification possible avant résiliation **seulement si** justifiée sécurité ; alors accessible (§8, 3.3.8) et jamais un test cognitif, **jamais plus difficile que la souscription**.

### Écran 6 — États particuliers (transverses)

**État-A — Limite de l'offre gratuite atteinte (message NON culpabilisant).**
- **But.** Informer sans faire honte et proposer une issue.
- **Message.** Factuel et neutre : « Vous avez atteint la limite de l'offre gratuite pour {ressource}. » + ce qui reste possible + choix « Voir mes options » **ou** « Continuer avec l'offre gratuite ». **Jamais** de ton punitif, **jamais** de blocage sans alternative, **jamais** de pop-up récurrent harcelant.
- **Action primaire.** Contextuelle et **non forcée** : « Voir mes options ».

**État-B — Échec de paiement.**
- **But.** Expliquer la cause (si connue), rassurer sur l'absence de débit, proposer une reprise.
- **Message.** « Le paiement n'a pas abouti. Aucun montant n'a été prélevé. » + cause grand public (« carte expirée », « refusée par la banque ») + « Réessayer » / « Changer de moyen de paiement » / « Plus tard ». Aucun code technique brut (charte §7).

**État Hors ligne (global).** Voir §10. Le passage hors ligne / le retour en ligne sont **annoncés en région live** (poli) en plus de l'indicateur visuel (§8 4.1.3).

---

## 6. Wireframes textuels

> Portrait, une main, portrait. **Actions primaires ancrées dans le tiers inférieur** (zone du pouce, charte §4). Descriptions, pas de code. Les valeurs chiffrées ci-dessous sont **provisoires** (cf. §0) et seront remplacées par `SPECS-esim-secours.md`.

**Écran 1 — Présentation des offres**
```
┌───────────────────────────────┐
│ ←  Nos offres                 │  titre écran (H1)
├───────────────────────────────┤
│ ┌───────────┐  ┌───────────┐  │
│ │ [✓]Offre  │  │ ★ Offre   │  │  étiquettes texte+icône
│ │  gratuite │  │  premium  │  │
│ │ (en cours)│  │ 4,99 €/mois│ │  badge "en cours" sur
│ │───────────│  │───────────│  │  l'offre ACTIVE ; prix TTC
│ │ Coffre: X │  │ Coffre: Y │  │  mêmes rubriques
│ │ Data secours: … │ Data: … │  │  (← SPEC eSIM, provisoire)
│ │ Fonctions:…│ │ Fonctions:…│ │
│ └───────────┘  └───────────┘  │
│  Comparer en détail  ›        │  lien secondaire
│                               │
│  (zone de défilement)         │
├───────────────────────────────┤
│  [ Choisir premium ]          │  ACTION PRIMAIRE (bas)
│  Rester sur l'offre gratuite  │  secondaire, neutre
└───────────────────────────────┘

Variante « déjà premium » : badge « Offre en cours » porté par
la carte premium ; PAS de « Choisir premium » ; action primaire
neutre « Gérer mon abonnement » → écran 5 ; ligne « Rester… » masquée.
```

**Écran 2 — Détail premium**
```
┌───────────────────────────────┐
│ ←  Offre premium              │
├───────────────────────────────┤
│   4,99 € / mois               │  prix TTC (gros)
│   toutes taxes comprises      │
│  ( Mensuel )( Annuel )        │  sélecteur période
│   Annuel : soit 4,16 €/mois   │  économie réelle,
│           49,90 €/an          │  PAS de faux prix barré
│───────────────────────────────│
│  Inclus dans premium :        │
│  • Coffre : {capacité}        │  ← provisoire (SPEC eSIM)
│  • Data de secours : {quota}  │  ← lien SPEC eSIM
│  • {fonctions…}               │
│  Sans engagement · résiliable │
│  Vos secrets restent à vous.  │
├───────────────────────────────┤
│  [ Choisir cette offre ]      │  ACTION PRIMAIRE
│  Comparer avec l'offre gratuite│
└───────────────────────────────┘
```

**Écran 3 — Passage à premium (récap + consentement)**
```
┌───────────────────────────────┐
│ ←  Récapitulatif              │
├───────────────────────────────┤
│  Offre premium — Mensuel      │
│  Prélevé aujourd'hui : 4,99 € │  TTC, explicite
│  Renouvellement auto le       │
│  23/08/2026, 4,99 €/mois —    │
│  modifiable/résiliable        │  info neutre
│  Moyen de paiement : ····1234 │  masqué (§9)
│───────────────────────────────│
│  [ ] J'accepte l'abonnement   │  CASE NON PRÉ-COCHÉE
│      premium et le renouvel-  │
│      lement (voir conditions) │
│  TEL ONLINE ne demande jamais │  anti-hameçonnage :
│  vos codes par message/appel. │  TEXTE STATIQUE persistant
├───────────────────────────────┤
│  [ Confirmer et payer 4,99 € ]│  désactivé (aria-disabled)
│  Cochez la case pour continuer.│  aide persistante reliée
└───────────────────────────────┘
```

**Écran 4 — Confirmation**
```
┌───────────────────────────────┐
│        ✓  C'est fait          │  succès (icône+texte)
│   Vous êtes en offre premium  │
├───────────────────────────────┤
│  Payé : 4,99 € TTC            │
│  Renouvellement : 23/08/2026  │
│  Ce que vous pouvez faire :   │
│  • {capacité} • {data} • …    │
│  Résiliable dans Réglages.    │
├───────────────────────────────┤
│  [ Découvrir mes fonctions ]  │  ACTION PRIMAIRE
│  Voir ma facture · Terminé    │
└───────────────────────────────┘
```

**Écran 5 — Gestion / résiliation**
```
┌───────────────────────────────┐
│ ←  Mon abonnement             │
├───────────────────────────────┤
│  Offre premium — 4,99 €/mois  │
│  Renouvellement : 23/08/2026  │
│  Moyen de paiement : ····1234 │  masqué
│  Factures  ›                  │
│───────────────────────────────│
│  Changer d'offre           ›  │  même poids visuel
│  Changer de moyen de paiement› │
│  Résilier mon abonnement   ›  │  VISIBLE, pas enterré
├───────────────────────────────┤
│  (modale résiliation — focus  │
│   déplacé dedans, piégé,      │
│   Échap = Garder premium)     │
│  « Active jusqu'au {date}.    │
│   Ensuite : offre gratuite.   │
│   Vos secrets restent à vous.»│
│   [Garder premium] [Résilier] │  destructif à droite ;
│                               │  focus défaut = Garder
└───────────────────────────────┘
   Focus restitué au bouton « Résilier » à la fermeture.
```

---

## 7. Microcopie (FR)

> Aucune concaténation. Montants, devises, dates : **injectés** via ICU/format localisé (`{prix}`, `{date}`). **La période n'est jamais injectée comme mot-variable** : elle est gérée par un `select` ICU (`{periode, select, month {…} year {…} other {…}}`) pour laisser chaque locale gérer l'ordre et l'accord (y compris AR-RTL). Pluriels via ICU. Clés dans l'espace `abo.*`.

| Élément | Clé i18n | Texte FR | Note i18n / traduction |
|---|---|---|---|
| Titre écran offres | `abo.offres.titre` | Nos offres | H1, +40 % en DE. |
| Erreur affichage offres | `abo.offres.erreur` | Impossible d'afficher les offres pour l'instant. | Écran 1, état Erreur. |
| Étiquette offre gratuite | `abo.offre.gratuite.label` | Offre gratuite | Jamais « freemium/tier ». Sens porté par texte + icône. |
| Badge offre en cours | `abo.offre.enCours.badge` | Offre en cours | Statut, pas couleur seule. Porté par la carte de l'offre active (gratuite **ou** premium). |
| Étiquette offre premium | `abo.offre.premium.label` | Offre premium | Jamais « SKU/premium tier ». |
| Prix + période | `abo.prix.periode` | `{periode, select, month {{prix}/mois} year {{prix}/an} other {{prix}}}` | **ICU select** sur la période ; `{prix}` déjà formaté (devise+TTC) selon locale. Pas de « € » ni de « mois »/« an » codés en dur ni injectés comme variable. |
| Mention TTC | `abo.prix.ttc` | toutes taxes comprises | Adapter la mention fiscale au marché (localisation ≠ traduction). |
| Économie annuelle | `abo.prix.annuel.economie` | soit {prixMois}/mois, {economie} par an | Assemblage ICU, pas de concaténation. Aucun faux prix barré. |
| Bouton choisir premium | `abo.cta.choisirPremium` | Choisir premium | Verbe d'action. |
| Bouton gérer (déjà premium) | `abo.cta.gerer` | Gérer mon abonnement | Écran 1, utilisateur déjà premium (pas d'upsell). |
| Bouton en savoir plus | `abo.cta.enSavoirPlus` | En savoir plus | — |
| Rester gratuit | `abo.cta.resterGratuit` | Rester sur l'offre gratuite | Neutre, non culpabilisant. |
| Comparer en détail | `abo.cta.comparer` | Comparer en détail | Ouvre tableau accessible. |
| Rubrique capacité | `abo.inclus.capacite` | Capacité du coffre | Glossaire : « coffre ». |
| Rubrique data secours | `abo.inclus.dataSecours` | Data de la carte SIM numérique de secours | Valeur = `SPECS-esim-secours.md`. Jamais « eSIM/profil ». |
| Lien fonctionnement data | `abo.lien.dataSecours` | Comment fonctionne la data de secours | Renvoie SPEC eSIM. |
| Rubrique fonctions | `abo.inclus.fonctions` | Fonctions avancées | — |
| Erreur détail premium | `abo.premium.erreur` | Détails indisponibles pour l'instant. | Écran 2, état Erreur. |
| Sans engagement | `abo.premium.sansEngagement` | Sans engagement · résiliable à tout moment | Réassurance honnête. |
| Rappel secrets | `abo.premium.secrets` | Vos secrets restent à vous, sur votre appareil, quelle que soit l'offre. | Voix rassurante (charte §7). |
| Sélecteur mensuel | `abo.periode.mensuel` | Mensuel | Libellé du contrôle (pas injecté dans une phrase). |
| Sélecteur annuel | `abo.periode.annuel` | Annuel | Idem. |
| Choisir cette offre | `abo.cta.choisirCette` | Choisir cette offre | — |
| Titre récapitulatif | `abo.recap.titre` | Récapitulatif | — |
| Prélevé aujourd'hui | `abo.recap.aujourdhui` | Prélevé aujourd'hui : {prix} | `{prix}` TTC localisé. |
| Renouvellement | `abo.recap.renouvellement` | Renouvellement automatique le {date}, {periode, select, month {{prix}/mois} year {{prix}/an} other {{prix}}} — modifiable ou résiliable à tout moment | ICU + `select` période ; `{date}` localisée ; ton neutre. |
| Moyen de paiement masqué | `abo.paiement.moyenMasque` | Carte se terminant par {4chiffres} | Jamais le numéro complet (§9). |
| Case consentement | `abo.consentement.case` | J'accepte l'abonnement premium et le renouvellement automatique décrits ci-dessus. | **Non pré-cochée**. Lien conditions séparé. |
| Lien conditions | `abo.consentement.conditions` | Voir les conditions | Localisation légale par marché. |
| Aide bouton désactivé | `abo.cta.payer.desactive.aide` | Cochez la case pour continuer. | Aide **persistante** reliée par `aria-describedby` (charte §12). Formulation **unique** — utilisée aussi dans le wireframe. |
| Bouton payer | `abo.cta.payer` | Confirmer et payer {prix} | Montant dans le libellé, ICU. |
| Anti-hameçonnage | `abo.securite.antiHameconnage` | TEL ONLINE ne vous demandera jamais vos codes par message ou par appel. | Charte §10. Rendu en **texte statique persistant** (§9, non superposé). |
| Annonce 3-D Secure | `abo.paiement.banque.info` | Votre banque va vous demander de confirmer le paiement. | Avant redirection ; retour propre garanti. |
| Vérification paiement | `abo.paiement.verification` | Vérification du paiement… | Après coupure pendant le paiement (§12) ; suivie de l'état réel. |
| Titre confirmation | `abo.confirm.titre` | C'est fait — vous êtes en offre premium | Succès. |
| Activation en cours | `abo.confirm.activationEnCours` | Activation en cours… | Écran 4, état Chargement. |
| Facture au retour du réseau | `abo.confirm.factureAuRetour` | Facture disponible au retour du réseau. | Écran 4, état Hors ligne. |
| Montant payé | `abo.confirm.paye` | Payé : {prix} | TTC localisé. |
| Prochaine date | `abo.confirm.prochaineDate` | Prochain renouvellement : {date} | Localisée. |
| Découvrir fonctions | `abo.cta.decouvrir` | Découvrir mes nouvelles fonctions | — |
| Voir facture | `abo.cta.voirFacture` | Voir ma facture | — |
| Terminé | `abo.cta.termine` | Terminé | — |
| Annonce succès (live) | `abo.a11y.succes` | Abonnement premium activé. | Région live polie. |
| Annonce hors ligne (live) | `abo.a11y.horsLigne` | Hors ligne — le paiement est indisponible. | Région live polie (charte §5.3). |
| Annonce retour en ligne (live) | `abo.a11y.enLigne` | De retour en ligne. | Région live polie. |
| Annonce modification (live) | `abo.a11y.modifie` | Modification enregistrée. | Région live polie (écran 5, changement d'offre). |
| Activation en attente | `abo.confirm.activationAttente` | Votre paiement est bien reçu. L'activation peut prendre un instant — nous vous prévenons dès que c'est prêt. | Jamais alarmant ; pas de re-paiement. |
| Titre gestion | `abo.gestion.titre` | Mon abonnement | Titre d'écran (2.4.2). |
| Erreur affichage abonnement | `abo.gestion.erreur` | Impossible d'afficher votre abonnement. | **Message** (distinct du titre) — écran 5, état Erreur. |
| Offre en cours | `abo.gestion.offreEnCours` | Offre premium — {periode, select, month {{prix}/mois} year {{prix}/an} other {{prix}}} | **ICU select** période (plus d'injection du mot période). |
| Se termine le | `abo.gestion.finLe` | Se termine le {date} | Après résiliation. |
| Changer d'offre | `abo.gestion.changer` | Changer d'offre | — |
| Changer paiement | `abo.gestion.changerPaiement` | Changer de moyen de paiement | — |
| Résilier | `abo.gestion.resilier` | Résilier mon abonnement | Visible, poids égal. |
| Modale résiliation | `abo.resil.confirme` | Votre offre premium restera active jusqu'au {date}. Ensuite, vous repassez à l'offre gratuite. Vos secrets restent à vous. | Conséquence explicite (charte §12). |
| Garder premium | `abo.resil.garder` | Garder premium | Choix par défaut du focus, non destructif. Cible d'Échap. |
| Confirmer résiliation | `abo.resil.confirmer` | Résilier | Destructif, à droite/fin. |
| Bannière premium actif | `abo.resil.banniereActive` | Offre premium active jusqu'au {date}. | Après résiliation (écran 5). |
| Résiliation hors ligne | `abo.resil.horsLigne` | Résiliation prise en compte le {date}. Elle reste valable même sans reconnexion ; nous la confirmons dès le retour du réseau. | Date d'effet = date de la demande ; **jamais ambigu** (§10, `Q9`). |
| Réactiver | `abo.resil.reactiver` | Réactiver mon offre premium | Réversibilité avant échéance. |
| Annonce résiliation (live) | `abo.a11y.resilie` | Résiliation enregistrée. | Région live polie. |
| Limite atteinte (titre) | `abo.limite.titre` | Limite de l'offre gratuite atteinte | Neutre, non culpabilisant. |
| Limite atteinte (corps) | `abo.limite.corps` | Vous avez atteint la limite de l'offre gratuite pour {ressource}. | ICU ; jamais punitif. |
| Limite — voir options | `abo.limite.cta` | Voir mes options | Non forcé. |
| Limite — continuer gratuit | `abo.limite.continuer` | Continuer avec l'offre gratuite | Alternative toujours offerte. |
| Échec paiement (titre) | `abo.echec.titre` | Le paiement n'a pas abouti | Pas de code brut. |
| Échec — pas de débit | `abo.echec.pasDeDebit` | Aucun montant n'a été prélevé. | Réassurance. |
| Échec — carte expirée | `abo.echec.carteExpiree` | Votre carte semble expirée. | Cause grand public. |
| Échec — refus banque | `abo.echec.refusBanque` | Votre banque a refusé le paiement. | — |
| Réessayer | `abo.cta.reessayer` | Réessayer | — |
| Plus tard | `abo.cta.plusTard` | Plus tard | Sortie non culpabilisante. |
| Hors ligne (offres) | `abo.offline.tarifs` | Tarifs vus le {date}. Reconnectez-vous pour confirmer. | Honnête (charte §8). |
| Hors ligne (paiement) | `abo.offline.paiement` | Le paiement a besoin d'une connexion. Votre choix est gardé. | Rien mis en file côté débit. |
| Mise en file (changement sûr) | `abo.offline.misEnFile` | Sera appliqué au retour du réseau. | Changement **sans débit** confirmé et non ambigu (§10). |
| Nombre de jours restants | `abo.premium.joursRestants` | {n, plural, one {# jour restant} other {# jours restants}} | Pluriel ICU. |
| Nombre d'appareils | `abo.inclus.appareils` | {n, plural, one {# appareil} other {# appareils}} | Pluriel ICU. |
| Carte bientôt expirée | `abo.renouv.carteExpireBientot` | Votre carte expire bientôt. Mettez-la à jour pour garder l'offre premium. | Rappel neutre (à cadrer, `Q10`). |

---

## 8. Accessibilité — WCAG 2.2 AA (spécifique à ces écrans)

| Critère (n°) | Comment il est satisfait ici |
|---|---|
| **1.4.3 / 1.4.11 Contraste** | Prix, libellés, boutons utilisent `color.ink`/`color.primary` (charte §11) ≥ 4.5:1 (texte) et ≥ 3:1 (bordures de la case à cocher, focus, séparateurs porteurs de sens). |
| **1.4.1 Couleur non seule** | Étiquettes « Gratuit »/« Premium » et succès/erreur portent **icône + texte** ; le badge « Offre en cours » est textuel. |
| **1.4.4 / 1.4.10 Zoom & reflow** | Cartes d'offres et récapitulatif restent lisibles à 200 % et 320 px sans défilement 2D ; les deux cartes passent en **empilé** sur très petits écrans. |
| **1.4.12 Espacement du texte** | Récapitulatif et listes « inclus » supportent interligne 1.5×, sans troncation ni chevauchement. |
| **1.4.13 Contenu au survol / focus** | Le **rappel anti-hameçonnage** (écran 3) et l'**aide du bouton désactivé** sont rendus en **texte statique persistant** sous le contrôle (non superposé, non dépendant du survol). Si une variante infobulle est un jour introduite, elle doit être **survolable, persistante et masquable** (Échap) conformément à 1.4.13. |
| **2.1.2 Pas de piège au clavier / focus modale** | À l'ouverture de la **modale de résiliation** (écran 5), le focus est **déplacé dans la modale** (titre ou premier contrôle) et **piégé** tant qu'elle est ouverte ; elle est **fermable au clavier** (Échap → « Garder premium ») ; à la fermeture, le focus est **restitué au déclencheur « Résilier »**. Le focus n'est **jamais piégé** hors modale. |
| **2.4.2 Titre de page (les 6 écrans)** | Titre unique par écran : (1) « Nos offres », (2) « Offre premium », (3) « Récapitulatif », (4) « C'est fait — vous êtes en offre premium », (5) « Mon abonnement ». Les **États particuliers** (écran 6 : État-A « Limite de l'offre gratuite atteinte », État-B « Le paiement n'a pas abouti ») sont des **sous-états annoncés** rattachés à leur écran parent (bannière/région live), non des pages distinctes. |
| **2.4.3 Ordre de focus** | Ordre logique : titre → contenu → consentement → action primaire ; en **RTL** (Amina) l'ordre suit la lecture droite-à-gauche via propriétés logiques. |
| **2.4.7 / 2.4.11 Focus visible** | Contour ≥ 3px, offset 2px, contraste ≥ 3:1 sur cartes, sélecteur période, case à cocher, boutons ; **jamais masqué** par la barre d'action ancrée. |
| **2.4.6 / 4.1.2 Nom, rôle, valeur** | Case à cocher = rôle checkbox avec état `coché/non coché` annoncé ; sélecteur mensuel/annuel = groupe de boutons radio étiqueté ; **tout bouton primaire désactivé** (payer, « Choisir premium » hors ligne, « Choisir cette offre » hors ligne, « Confirmer et payer » hors ligne) utilise **`aria-disabled="true"`** (contrôle **resté focusable**) + **raison reliée par `aria-describedby`**, jamais un `disabled` natif qui le retirerait du parcours de focus. |
| **2.5.3 Nom accessible = texte visible** | Le bouton « Confirmer et payer {prix} » a un nom accessible incluant le montant visible. |
| **2.5.8 / cible 44px** | Cartes, sélecteur, case à cocher, boutons ≥ 44×44 px (norme interne), espacement ≥ 8 px ; la case à cocher a une zone tactile élargie au libellé. |
| **2.5.1 Gestes** | Aucune action (choisir, résilier) uniquement au glissé : tout est atteignable par bouton. |
| **3.3.4 Prévention des erreurs (juridique, financière, données)** | Sur ce parcours d'**engagement financier récurrent**, l'action est : **vérifiable** (récapitulatif complet — prix TTC, date de renouvellement, périodicité — avant validation), **confirmée** (consentement explicite non pré-coché, obligatoire pour activer le bouton) et **réversible** (résiliation à tout moment, date d'effet = date de la demande, réactivation avant échéance). *(Objectif AAA visé sur ce parcours critique — cf. **3.3.6** : mécanismes de vérification/confirmation étendus à tout changement d'offre payant.)* |
| **3.3.7 Aides persistantes** | Le moyen de paiement déjà saisi n'est pas redemandé lors d'un changement d'offre. |
| **3.3.8 Authentification accessible (obligation de moyens)** | Pour la re-authentification éventuelle avant résiliation : **jamais** de test cognitif comme seul facteur ; collage / gestionnaire de mots de passe autorisé ; la résiliation n'est **jamais** rendue plus difficile que la souscription. Le défi **3-D Secure est rendu par la banque/le prestataire** et échappe au contrôle de TEL ONLINE : la SPEC **ne garantit pas** son accessibilité côté banque, mais **annonce la redirection**, **garantit un retour propre dans l'app**, et **impose la conformité 3.3.8 comme critère de sélection du prestataire** (voir `Q11`). |
| **4.1.3 Messages d'état (région live)** | Annoncés : succès « Abonnement premium activé », modification « Modification enregistrée », résiliation « Résiliation enregistrée » (**polis**) ; échec bloquant « Le paiement n'a pas abouti » (**assertif**) ; **passage hors ligne / retour en ligne** « Hors ligne — le paiement est indisponible » / « De retour en ligne » (**polis**, charte §5.3), en plus de l'indicateur visuel. |
| **2.3.1 / prefers-reduced-motion** | La transition vers l'écran de confirmation respecte `prefers-reduced-motion` ; aucune animation clignotante. |
| **1.3.4 Orientation** | Aucun écran d'abonnement bloqué en portrait/paysage. |

---

## 9. Sécurité & confiance UX

Dérivé de la charte §10.

- **Aucune fuite serveur/journal.** Les **données de carte bancaire** ne transitent **jamais** par les journaux ni l'analytique de TEL ONLINE (HYP-6) ; l'app n'enregistre que des événements **anonymisés sans contenu** (ex. « paiement_tenté », sans montant nominatif ni PAN). La **phrase de récupération** et les secrets **n'apparaissent nulle part** dans ce parcours.
- **Masquage.** Le moyen de paiement est affiché **tronqué** (« Carte se terminant par 1234 ») ; jamais le numéro complet, jamais le cryptogramme.
- **Capture d'écran — décision tracée.** Le **blocage de capture d'écran n'est pas requis** sur l'écran de paiement (écran 3) ni sur les écrans de gestion : contrairement aux écrans de phrase de récupération (charte §10), **aucun secret ni PAN complet n'y est affiché** — seulement les **4 derniers chiffres** de la carte, une information non sensible et non réutilisable. La restriction de capture reste **réservée aux écrans de secret** (phrase de récupération). Décision explicitement documentée ici plutôt que laissée implicite.
- **Presse-papiers.** Aucun montant/identifiant de paiement mis dans le presse-papiers automatiquement.
- **Anti-hameçonnage.** Rappel contextuel `abo.securite.antiHameconnage` sur l'écran de paiement, rendu en **texte statique persistant** (non superposé, non dépendant du survol — cf. §8 1.4.13) ; l'app **ne demande jamais** de code bancaire par message/appel, et l'écran le dit.
- **Consentement.** Case **non pré-cochée**, bouton primaire désactivé (`aria-disabled`, raison reliée) tant que le consentement n'est pas donné ; conditions accessibles avant l'acte. **Aucune urgence artificielle**, aucun compte à rebours, aucun « dernières places ».
- **Confirmations d'actions.** Résiliation, changement d'offre et paiement décrivent leur **conséquence** (montant, date d'effet) avant de valider (prévention des erreurs, §8 3.3.4). La résiliation est **réversible** avant l'échéance et **prise en compte à la date de la demande**, jamais laissée dans un état ambigu (§10).
- **Redirection prestataire honnête.** Avant toute redirection (3-D Secure), l'écran prévient et **revient dans l'app** avec un état clair (jamais laisser l'utilisateur perdu hors app).
- **Pas de rétention manipulatoire.** Le sous-parcours de résiliation ne comporte **aucun** obstacle supplémentaire, **aucune** offre imposée bloquant l'action, **aucun** bouton « Résilier » caché ou grisé sans raison.

---

## 10. Offline-first & réseau

- **Consultable hors ligne :** écran (1) présentation, (2) détail premium et (5) gestion s'affichent depuis le **dernier catalogue et le dernier état d'abonnement en cache**, avec mention honnête « vu le {date} » (`abo.offline.tarifs`). Le passage hors ligne / le retour en ligne sont **annoncés en région live** (`abo.a11y.horsLigne` / `abo.a11y.enLigne`) en plus de l'indicateur visuel.
- **Jamais de débit en file.** Le paiement (écran 3) **n'est pas** mis en file d'attente hors ligne : débiter en différé à l'insu de l'utilisateur serait trompeur. Hors ligne, le bouton « Confirmer et payer » est **désactivé (`aria-disabled`, raison reliée)** ; le **choix d'offre reste mémorisé** localement et repris au retour du réseau.
- **Résiliation hors ligne — jamais ambiguë, date d'effet = date de la demande.** Une résiliation confirmée hors ligne est **enregistrée localement de façon durable avec la date de la demande comme date d'effet légale**, affichée sans ambiguïté (`abo.resil.horsLigne` : « Résiliation prise en compte le {date}. Elle reste valable même sans reconnexion… »), et **confirmée de façon idempotente** à la reconnexion. Elle n'est **jamais perdue** par une coupure et **ne peut jamais aboutir à un prélèvement** postérieur à la date de la demande. *(Position de conception à valider juridiquement — `Q9`/`Q7`. Alternative admise si le juridique l'exige : refuser la résiliation hors ligne avec explication honnête et renvoi vers un canal joignable — mais jamais un état intermédiaire pouvant entraîner un débit.)*
- **Changements sûrs mis en file.** Sur l'écran (5), un **changement d'offre sans débit** confirmé et non ambigu peut être mis en file **avec mention explicite** « Sera appliqué au retour du réseau » (`abo.offline.misEnFile`). Un changement **impliquant un débit** suit la règle de paiement (désactivé, raison reliée, aucun débit en file).
- **Synchro au retour.** Reconnexion → confirmation de l'état réel (« Résiliation confirmée », « Offre à jour »), résolution transparente ; l'utilisateur est **informé du résultat**.
- **Pas de double facturation.** Un paiement interrompu par une coupure est **idempotent** côté prestataire ; au retour, l'app affiche l'état réel (payé / non payé) sans relancer aveuglément (cf. §12).
- **eSIM de secours.** Le quota de data affiché provient de `SPECS-esim-secours.md` (**dépendance bloquante**, §0) ; la carte SIM numérique de secours peut d'ailleurs servir de **filet de connectivité** pour finaliser une action réseau — sans jamais être imposée.
- **Messages honnêtes.** Jamais d'erreur technique brute : « Le paiement a besoin d'une connexion. Votre choix est gardé. »

---

## 11. Performance

Budgets charte §9 (mobile d'entrée de gamme, réseau lent).

- **Comparaison d'offres légère :** le catalogue (libellés + prix) tient dans le **budget critique ≤ 200 Ko** ; il est **mis en cache** pour un affichage quasi instantané aux visites suivantes.
- **Squelettes** pour les cartes d'offres et le récapitulatif (pas de spinner nu) ; contenu progressif.
- **Feedback < 100 ms** au tap sur « Choisir premium », le sélecteur mensuel/annuel et la case de consentement, même si la confirmation serveur arrive après.
- **Écran de paiement priorisé :** rendu du récapitulatif avant le chargement du module prestataire ; le bouton reste désactivé (raison reliée) jusqu'à disponibilité, avec état clair.
- **`prefers-reduced-data` :** pas de préchargement lourd d'illustrations d'offres ; visuels allégés.
- **INP ≤ 200 ms** sur les interactions clés (bascule période, cocher, ouvrir/fermer la modale de résiliation).

---

## 12. Cas limites & erreurs

| Situation | Comportement | Message (FR, langage clair) — clé |
|---|---|---|
| Carte refusée / expirée | État-B ; pas de débit ; propose réessayer/changer de carte. | « Le paiement n'a pas abouti. Aucun montant n'a été prélevé. Votre carte semble expirée. » — `abo.echec.titre` + `abo.echec.pasDeDebit` + `abo.echec.carteExpiree` |
| Coupure réseau pendant le paiement | Idempotence : au retour, afficher l'état réel (payé / non payé), **aucune double tentative**. | « Vérification du paiement… » puis état réel. — `abo.paiement.verification` (puis `abo.confirm.titre` ou `abo.echec.titre`) |
| Paiement reçu mais activation en attente | Confirmer la réception, activer dès que possible, prévenir ; pas de re-paiement. | « Votre paiement est bien reçu. L'activation peut prendre un instant… » — `abo.confirm.activationAttente` |
| Consentement non coché | Bouton payer désactivé (`aria-disabled`) + aide **persistante reliée** expliquant pourquoi (pas seulement grisé). | « Cochez la case pour continuer. » — `abo.cta.payer.desactive.aide` |
| Limite de l'offre gratuite atteinte | Message neutre, alternative toujours offerte, pas de pop-up récurrent. | « Vous avez atteint la limite de l'offre gratuite pour {ressource}. » — `abo.limite.corps` |
| Catalogue/tarifs indisponibles | Afficher le cache « vu le {date} » ; paiement désactivé (raison reliée). | « Tarifs vus le {date}. Reconnectez-vous pour confirmer. » — `abo.offline.tarifs` |
| Résiliation demandée hors ligne | **Enregistrée durablement, date d'effet = date de la demande**, jamais perdue ni ambiguë ; confirmée à la reconnexion. | « Résiliation prise en compte le {date}. Elle reste valable même sans reconnexion ; nous la confirmons dès le retour du réseau. » — `abo.resil.horsLigne` |
| Changement d'offre sans débit hors ligne | Mis en file avec mention explicite (uniquement si sûr et non ambigu). | « Sera appliqué au retour du réseau. » — `abo.offline.misEnFile` |
| Erreur d'affichage de l'abonnement | Ne **jamais** bloquer la résiliation : offrir « Réessayer » + canal d'aide. | « Impossible d'afficher votre abonnement. » + « Réessayer » — `abo.gestion.erreur` + `abo.cta.reessayer` |
| Changement d'offre (montée/descente) | Récapituler le **delta** (nouveau prix, date d'effet, prorata éventuel) avant de valider ; débit éventuel soumis à la règle réseau. | « Renouvellement automatique le {date}, {prix}/période… » — `abo.recap.renouvellement` |
| Devise/locale non prise en charge | Repli sur devise par défaut du marché + mention ; jamais d'affichage cassé. | (format localisé via ICU + `select` période, pas de « € » codé en dur) |
| Renouvellement à venir / carte bientôt expirée | Rappel neutre, non alarmant ; proposer de mettre à jour la carte. | « Votre carte expire bientôt. Mettez-la à jour pour garder l'offre premium. » — `abo.renouv.carteExpireBientot` (à cadrer, `Q10`) |

---

## 13. Critères d'acceptation (testables)

- [ ] Les libellés à l'écran n'emploient **jamais** « freemium », « SKU », « tier », « eSIM/profil » — uniquement « offre gratuite », « offre premium », « carte SIM numérique de secours » (glossaire charte §16).
- [ ] Le **prix est affiché TTC**, avec **périodicité** et mention « toutes taxes comprises », sur les écrans 1, 2, 3 et 4.
- [ ] **Aucune case n'est pré-cochée** ; le bouton de paiement est **désactivé (`aria-disabled`, raison reliée)** tant que le consentement n'est pas donné, avec texte d'aide persistant.
- [ ] **Aucun** compte à rebours, « dernières places » ou urgence artificielle n'apparaît nulle part.
- [ ] La comparaison présente les **mêmes rubriques dans le même ordre** pour les deux offres. *(⚠️ testable sur données réelles uniquement après réception de `SPECS-esim-secours.md` + `Q4`/`Q5` — cf. §0 ; les valeurs provisoires servent au prototypage.)*
- [ ] L'écran 1 gère le cas **utilisateur déjà premium** (badge sur la carte premium, aucun CTA d'upsell, accès « Gérer mon abonnement »).
- [ ] Le montant et la date de renouvellement sont visibles **avant** paiement et **sur** la confirmation.
- [ ] **Résilier** est atteignable en autant d'étapes (ou moins) que souscrire, visible sans le chercher, avec **une** confirmation décrivant la conséquence et **aucune** rétention imposée.
- [ ] La modale de résiliation gère le **focus** (déplacé dedans à l'ouverture, piégé, fermable au clavier via Échap, restitué au déclencheur à la fermeture) — WCAG 2.1.2 (§8).
- [ ] La résiliation est **réversible** avant l'échéance (« Réactiver ») et **prise en compte à la date de la demande**, y compris hors ligne — **jamais** dans un état ambigu pouvant entraîner un prélèvement.
- [ ] Le parcours financier documente **3.3.4** (récapitulatif vérifiable, consentement explicite, engagement réversible) — §8.
- [ ] En cas d'échec de paiement, le message indique **« aucun montant prélevé »** (sauf débit réel confirmé) et propose une reprise, sans code technique.
- [ ] Le message de **limite atteinte** est neutre et propose **toujours** une alternative « continuer en gratuit ».
- [ ] Hors ligne : les offres sont consultables (cache daté) ; le **paiement est désactivé** avec raison reliée ; **aucun débit** n'est mis en file ; le passage hors ligne / retour en ligne est **annoncé en région live**.
- [ ] Chaque chaîne affichée possède une **clé i18n** (§7) ; aucune concaténation ; **période gérée par `select` ICU** (jamais injectée comme mot) ; pluriels/montants via **ICU** ; testé **FR / EN / ES / AR-RTL** et **+40 %** de longueur sans troncation.
- [ ] Devises, prix et dates sont **localisés** (jamais formatés à la main).
- [ ] Succès, modification, résiliation, échec et **changement d'état réseau** sont **annoncés en région live** (poli / assertif selon gravité).
- [ ] Toutes les cibles ≥ **44 px**, focus visible ≥ 3px, contrastes conformes (§8).
- [ ] Aucune donnée de carte bancaire ni secret n'apparaît dans les **journaux/analytique** (§9) ; l'absence de blocage de capture sur l'écran de paiement est **justifiée et tracée** (§9).

---

## 14. Métriques de succès (KPIs UX)

| KPI | Définition | Cible indicative |
|---|---|---|
| **Taux de complétion de souscription** | % d'utilisateurs entrés en écran (3) qui atteignent la confirmation (4). | À définir en atelier ; suivi de tendance. |
| **Taux d'échec de paiement récupéré** | % d'échecs (État-B) suivis d'une souscription réussie après « Réessayer ». | Élevé = messages clairs et reprise efficace. |
| **Facilité de résiliation** | Nombre d'étapes / temps médian pour résilier vs pour souscrire. | Résilier **≤** souscrire (exigence charte §2.10). |
| **Abandon au récapitulatif** | % d'abandons sur l'écran (3) et raison (hors ligne, consentement, prix). | Bas ; à analyser sans dark pattern correctif. |
| **Compréhension de l'offre** | Test utilisateur : % de personnes (dont Robert) expliquant correctement prix, périodicité et résiliation après lecture. | Élevé = transparence atteinte. |

> Mesures **anonymisées, sans contenu sensible** (charte §10) : aucun montant nominatif, aucune donnée de paiement.

---

## 15. Questions ouvertes

Reprises de la charte (`Q4`–`Q7`) et poursuivies selon la convention de numérotation de la charte (`Q8`, `Q9`…) :

- `Q4` (charte) — La **data de la carte SIM de secours** est-elle incluse (quota) dans l'offre gratuite ou strictement premium ? La valeur affichée dépend de `SPECS-esim-secours.md`. **Bloquant** pour figer les écrans 1/2 (§0).
- `Q5` (charte) — Périmètre précis **gratuit vs premium** (capacité, nombre d'appareils, data). Nécessaire pour figer les rubriques comparées (écran 1). **Bloquant** (§0).
- `Q6` (charte) — Langues de lancement et responsabilité de la traduction (impacte mentions fiscales/légales localisées). Rappel : langues de lancement = **FR · EN · ES · AR-RTL** (charte §1) → toutes couvertes par la matrice de test §13.
- `Q7` (charte) — Contraintes **réglementaires** (RGPD, droit de la consommation, mentions légales, TVA/devises par marché) impactant le récapitulatif et la **date d'effet de résiliation** (lié à `Q9`).
- `Q8` (nouvelle, `HYP-7`) — Périodicités proposées (mensuel / annuel / autre) et modalité d'affichage de l'économie annuelle.
- `Q9` (nouvelle, `HYP-8`) — Politique de **résiliation** : effet immédiat ou à échéance, **date d'effet = date de la demande** (à confirmer côté juridique), remboursement au prorata, réversibilité. Tranche le comportement hors ligne (§10).
- `Q10` (nouvelle) — Gestion des **échecs de renouvellement** (carte expirée) : période de grâce, relances, rétrogradation vers l'offre gratuite sans perte de secrets.
- `Q11` (nouvelle, `HYP-6`) — **Prestataire de paiement** retenu et parcours 3-D Secure. **Critère de sélection imposé :** parcours 3-D Secure **conforme WCAG 3.3.8** (authentification accessible), retour propre garanti dans l'app (impact écran 3 et §8).

---

*Fin de la SPEC `SPECS-abonnement-freemium.md` v0.2 (proposition révisée post-audit). Documents liés : `CHARTE-UX.md`, `SPECS-esim-secours.md` (**dépendance bloquante non résolue**, §0).*
