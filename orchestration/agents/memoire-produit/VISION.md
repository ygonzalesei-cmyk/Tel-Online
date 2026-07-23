# MÉMOIRE PRODUIT — VISION (Tel-Online)

> **Statut** : consolidation de la mémoire produit par le writer unique (T-008), **alignée le 2026-07-23 sur la vague 1 intégrée dans `atelier`** (cadrage Sécurité T-001/T-005, Design T-006, Infra / Registre technique T-007).
> **Date de consolidation** : 2026-07-23.
> **Périmètre du writer unique** : `orchestration/agents/memoire-produit/` (VISION.md, BACKLOG.md, FAIT.md). Le présent fichier ne porte que la **VISION**, le **glossaire** et les **décisions actées**. Le modèle freemium, la roadmap et les questions ouvertes vivent dans BACKLOG.md ; FAIT.md reste vide tant que rien n'est VERIFIED en production.
> **Règle « ne rien inventer »** : ce document ne consigne que ce qui est **acté / spécifié par le cadrage**, avec **source**. Tout besoin nouveau ou question ouverte est routé vers son owner — **/tickets**, **/securite**, **/design** — ou vers **BACKLOG.md** ; jamais tranché ici. On distingue strictement : décisions actées (§5, datées) ≠ propositions de backlog ≠ questions ouvertes.
>
> **Définition produit (consolidée du cadrage vague 1 — sources citées ; matérialisation définitive au T-004 + validation patron)** : **TEL ONLINE** est un service de **continuité et de récupération « zéro perte » de la vie numérique mobile**, articulé autour de trois piliers :
> 1. **eSIM de secours** — restauration rapide de la ligne mobile par **transfert** opérateur (GSMA RSP), **jamais par clonage** (garde-fou Sécurité T-005 ; Registre technique T-007) ;
> 2. **coffre zero-knowledge** — mots de passe et secrets chiffrés côté client (clé **dérivée d'un secret utilisateur : phrase de récupération + biométrie**), le serveur ne détenant que du **chiffré** (Sécurité T-001 ; Registre technique T-007) ;
> 3. **volet paiement / abonnement** — **freemium** et fonctions financières via **PSP SCA/DSP2** et **wallets certifiés**, sans jamais stocker d'OTP bancaire (garde-fous T-005 ; Registre technique T-007).
>
> Hébergement **UE** (résidence des données). Les **modules métier définitifs** et la **stack** sont fixés au **T-004** (Infra/Sécurité + validation patron) — voir §5.

## 1. Promesse produit — « zéro perte »

**La promesse centrale de Tel-Online est simple : aucune donnée confiée par l'utilisateur ne doit jamais être perdue.** Ce que l'utilisateur confie au service reste récupérable, intact et fidèle à l'original, dans le temps et à travers ses appareils.

> **Portée de « zéro perte » (précisée par le cadrage vague 1)** : la promesse couvre les trois piliers — **continuité de la ligne** (eSIM de secours), **coffre** de secrets (zero-knowledge) et **accès comptes / paiement**. Le détail des **types de données** et le **périmètre définitif des modules** sont arrêtés au **T-004** (voir §5 et BACKLOG Q#1). La promesse « zéro perte » vaut pour l'ensemble de ces piliers.

Concrètement, cette promesse implique :

- **Persistance** : une donnée conservée reste disponible tant que l'utilisateur ne l'a pas lui-même supprimée.
- **Fidélité** : la donnée restituée est identique à la donnée d'origine, sans altération ni corruption silencieuse.
- **Résilience** : la perte d'un appareil, d'une copie ou d'un incident isolé ne doit jamais entraîner la perte définitive de la donnée.
- **Récupérabilité** : l'utilisateur peut retrouver ses données même après un changement d'appareil ou une réinstallation.

Ce que la promesse **n'est pas** (limites honnêtes) :

- Ce n'est **pas une protection contre les suppressions volontaires** de l'utilisateur : quand l'utilisateur supprime, la suppression doit être effective (voir §3). Toute protection contre une **erreur de suppression** (corbeille, rétention, annulation) et sa durée éventuelle sont *(à préciser => /tickets)*.
- Ce n'est **pas une garantie contre l'oubli des moyens d'accès** : en architecture zero-knowledge, la perte irrémédiable de la clé / phrase de récupération peut rendre les données illisibles. La promesse « zéro perte » porte sur la conservation de la donnée chiffrée, pas sur la capacité du fournisseur à la déchiffrer à la place de l'utilisateur (voir §2).
- Ce n'est **pas un versionnage illimité ni un historique complet** par défaut : l'étendue de l'historique et des versions relève du backlog *(=> backlog / /tickets)*.
- Ce n'est **pas une garantie de disponibilité contractuelle** : les niveaux de service garantis, ainsi que les objectifs chiffrés de perte de données maximale tolérée et de délai de rétablissement, sont *(à préciser => /tickets)*.

## 2. Zero-knowledge

**Principe : le fournisseur ne peut jamais lire les données de l'utilisateur en clair.** Les données sont chiffrées avant de quitter l'appareil, et Tel-Online n'a jamais accès aux contenus déchiffrés.

Conséquences de conception :

- **Chiffrement côté client** : le chiffrement et le déchiffrement se font sur l'appareil de l'utilisateur ; le serveur ne manipule que des données chiffrées.
- **Clés dérivées côté client** : la clé qui déchiffre les données utilisateur est **dérivée sur l'appareil d'un secret utilisateur (phrase de récupération + biométrie)** ; elle ne figure **jamais** côté serveur (Sécurité T-001 ; Registre technique T-007). Le chiffrement au repos côté serveur (KMS) protège le **stockage**, mais **aucune clé serveur ne déchiffre le coffre utilisateur**.
- **Non-accès structurel** : l'incapacité du fournisseur à lire les données est une propriété de l'architecture, pas une simple politique interne.

**Tension réelle avec la récupération de compte.** Le zero-knowledge crée une tension directe avec l'expérience courante de « mot de passe oublié » : puisque le fournisseur ne détient pas les clés, il ne peut pas, par lui-même, redonner accès aux données. Cette tension **doit être résolue par des garde-fous côté utilisateur — clé ou phrase de récupération — et non par un accès fournisseur** qui romprait la promesse zero-knowledge. Autrement dit, la récupération repose sur des secrets que l'utilisateur détient et peut restaurer, jamais sur une porte dérobée. **Cette tension est confirmée et suivie côté Sécurité** (constats **SEC-001 à SEC-004**, P0 — récupération/escrow cassant le zero-knowledge, phishing de la phrase BIP39) et **instruite par le T-002** (architecture du coffre zero-knowledge : dérivation de clé, récupération sans déchiffrement serveur), avec les SPECS `/design` *phrase-recuperation* et *recuperation-zero-perte*. Les mécanismes précis restent des choix d'implémentation *(=> /securite T-002 ; /design ; décision patron « 2 secrets vs 1 kit hardware » — cf. BACKLOG Q#7)*.

**Limite : le zero-knowledge porte sur le contenu.** Le principe garantit la non-connaissance du **contenu en clair**, mais **pas nécessairement celle des métadonnées** qu'un service peut observer malgré le chiffrement (tailles, horodatages, nombre et fréquence d'objets, adresses IP, etc.). Un lecteur ne doit pas comprendre « zero-knowledge » comme « le fournisseur ne sait rien ». La **minimisation et la protection des métadonnées** constituent un principe distinct, dont le périmètre relève du backlog *(=> /tickets)*.

## 3. Garde-fous

Les garde-fous ci-dessous sont des **principes de conception** qui découlent directement de « zéro perte » et du zero-knowledge. Ils énoncent *ce qui doit être garanti* ; le *comment* relève du backlog ou des tickets.

- **Chiffrement côté client** : les données sont chiffrées sur l'appareil avant tout envoi, de sorte que seul l'utilisateur puisse les déchiffrer. *(choix d'implémentation => backlog / /tickets)*
- **Clés jamais transmises en clair** : les clés de déchiffrement restent sous le contrôle de l'utilisateur et ne transitent ni ne sont stockées en clair côté fournisseur. *(choix d'implémentation => backlog / /tickets)*
- **Récupération de compte compatible zero-knowledge** : l'utilisateur dispose d'un moyen de récupération (clé ou phrase de récupération) lui permettant de retrouver l'accès sans que le fournisseur puisse lire les données. *(choix d'implémentation => backlog / /tickets)*
- **Vérification d'intégrité (anti-corruption silencieuse)** : toute donnée restituée doit pouvoir être vérifiée comme identique à l'original, afin de détecter et prévenir toute corruption non signalée. *(choix d'implémentation => backlog / /tickets)*
- **Redondance des copies** : les données chiffrées sont conservées en plusieurs exemplaires, de sorte qu'un incident isolé n'entraîne jamais de perte définitive. *(niveau de redondance et répartition => backlog / /tickets)*
- **Suppression effective** : lorsqu'un utilisateur supprime une donnée, la suppression est réellement appliquée à l'ensemble des copies, dans un délai maîtrisé. *(portée exacte, corbeille / rétention et délai => à préciser => /tickets)*
- **Journal d'audit** : les opérations sensibles (accès, modifications de configuration de sécurité, suppressions) sont journalisées de manière vérifiable par l'utilisateur, sans compromettre le zero-knowledge. *(contenu, portée et exposition du journal => backlog / /tickets)*

### Garde-fous produit actés — Sécurité (T-005, règles à veto Release)

> Source : `_REGLES-COMMUNES.md` (« Garde-fous produit TEL ONLINE ») et `securite/REGISTRE.md`. Ces règles sont **actées** : toute violation ouvre un **SEC-### P0** et **bloque la Release** (R10). Elles sont **possédées par `/securite`** ; la mémoire produit les **reflète** (elle ne les modifie pas).

- **Aucun OTP bancaire (3-D Secure) intercepté ni stocké** : l'authentification forte **SCA/DSP2** et le 3DS2 sont **délégués au PSP** ; l'utilisateur reçoit lui-même ses OTP légitimes.
- **eSIM = transfert opérateur, jamais clonage** : provisioning via **GSMA RSP** (SM-DP+), sans duplication de profil.
- **Aucun mot de passe / secret en clair** : les secrets ne vivent que dans le **coffre chiffré zero-knowledge**.
- **Tout flux financier ⇒ KYC/AML** à cadrer avec la conformité (=> /securite T-003).
- **Chiffrement authentifié & intégrité du coffre** : AEAD, anti-rollback / anti-troncature des sauvegardes (constats SEC-011/012). *(détail => /securite)*

> **État sécurité (à J0).** La Sécurité a ouvert **42 constats SEC-### (18 P0)** au cadrage (T-001/T-005) ⇒ **VETO Release actif** tant qu'un P0 reste ouvert. Détail : `securite/REGISTRE.md` (writer unique `/securite`).

> **Tensions de conception à arbitrer (=> /tickets).** Ces garde-fous ne sont pas orthogonaux ; plusieurs sont en tension directe : (a) **Redondance des copies** ↔ **Suppression effective** (effacer de façon fiable toutes les répliques) ; (b) **versionnage / sauvegardes incrémentales** ↔ **Suppression effective** (purger effectivement l'historique) ; (c) **Journal d'audit** ↔ **zero-knowledge** (le journal peut exposer des métadonnées au fournisseur). Ces arbitrages sont routés vers le backlog / /tickets, non tranchés ici.

## 4. Glossaire

| Terme | Définition |
|-------|------------|
| **Zéro perte** | Promesse produit selon laquelle aucune donnée confiée au service ne doit être perdue du fait d'un événement **non voulu par l'utilisateur** : panne, corruption silencieuse, perte d'un appareil ou d'une copie, incident isolé. La promesse **ne couvre pas les suppressions décidées par l'utilisateur** (voir garde-fou « Suppression effective ») ; toute protection contre une erreur de suppression (corbeille, rétention, annulation) est un choix à instruire => backlog / /tickets. |
| **Zero-knowledge** | Architecture dans laquelle le fournisseur du service n'a pas connaissance du **contenu en clair** des données de l'utilisateur : le service opère sans jamais pouvoir déchiffrer ce qu'il stocke. Le terme est employé ici au sens « non-connaissance du contenu par le fournisseur », **distinct** de la notion cryptographique de *preuve à divulgation nulle de connaissance*. Il se traduit concrètement par le chiffrement côté client et par des clés jamais transmises en clair. |
| **Chiffrement de bout en bout (E2EE)** | Label descriptif (à confirmer, non acté) désignant une protection où les données sont chiffrées côté client et ne peuvent être déchiffrées que par l'utilisateur légitime, sans exposition en clair pendant le transport ou le stockage. Le vocabulaire **acté** au cadrage reste « chiffrement côté client » et « clés jamais transmises en clair » ; E2EE en est un intitulé proche à confirmer. |
| **Clé de récupération / phrase de récupération** | Élément secret détenu par l'utilisateur permettant de retrouver l'accès à ses données chiffrées (par exemple après perte d'un appareil), de façon compatible avec le principe zero-knowledge. |
| **Garde-fou** | Principe de conception découlant de la promesse « zéro perte » et de l'approche zero-knowledge, servant de limite ou d'exigence à respecter, sans préjuger d'un choix technique précis. |
| **Freemium** | Modèle de distribution combinant une offre gratuite (limitée) et des offres payantes proposant des capacités ou des limites étendues. |
| **Appareil (au sens du plan)** | Terminal (par exemple un téléphone) rattaché à un compte et pris en compte dans les limites d'un palier tarifaire ; l'offre gratuite est associée à un seul appareil. |
| **Espace de stockage chiffré** | Terme générique désignant l'espace logique dans lequel les données de l'utilisateur sont conservées sous forme chiffrée. Toute structuration produit particulière (par exemple en « coffre ») est une **proposition à arbitrer => backlog / /tickets**, non un acquis. |
| **Sauvegarde** | Terme générique : opération consistant à créer et conserver une copie des données afin de pouvoir les retrouver ultérieurement. Son inclusion dans le périmètre du service est **à confirmer** (cf. §1, Q#1). |
| **Restauration** | Terme générique : opération consistant à rétablir des données à partir d'une copie conservée. Son inclusion dans le périmètre du service est **à confirmer** (cf. §1, Q#1). |
| **Synchronisation** | Terme générique : mise en cohérence des données entre plusieurs appareils ou copies rattachés à un même compte. Son inclusion dans le périmètre du service est **à confirmer** (cf. §1, Q#1). |
| **Intégrité / vérification d'intégrité** | Propriété garantissant que les données n'ont pas été altérées ; la vérification d'intégrité est le contrôle permettant de détecter une corruption, y compris silencieuse. |
| **Redondance** | Existence de plusieurs copies des données afin de réduire le risque de perte en cas de défaillance d'une copie. |
| **MVP** | Version minimale viable : premier périmètre livrable réunissant les fonctions essentielles. |
| **V1** | Première version consolidée faisant suite au MVP. |
| **V2** | Version ultérieure élargissant le périmètre au-delà de la V1. |
| **Cadrage** | Phase initiale du projet consacrée au cadrage, précédant les phases de réalisation. Sa **correspondance éventuelle avec l'étape S0** de la roadmap est une proposition de séquencement (=> backlog / /tickets), **non une décision actée**. |
| **« Sx » (semaine / sprint de la roadmap)** | Notation désignant une étape de la roadmap, où « x » est le numéro de la semaine ou du sprint (par exemple S0, S1, S13). |
| **VERIFIED (au sens de FAIT.md)** | Statut réservé aux éléments effectivement vérifiés en production ; seul un élément VERIFIED peut figurer dans FAIT.md. |
| **/tickets** | Destination de routage des besoins nouveaux et des questions ouvertes, non tranchés dans la mémoire produit et à instruire séparément. |
| **eSIM de secours** | Profil eSIM permettant de **restaurer rapidement la ligne mobile** sur un appareil, par **transfert** opérateur (GSMA **RSP**, flux SM-DP+), **jamais par clonage** (garde-fou Sécurité). |
| **GSMA RSP (SGP.22)** | Standard de *Remote SIM Provisioning* utilisé pour provisionner/transférer un profil eSIM via un serveur **SM-DP+** ; base technique de l'eSIM de secours (Registre technique T-007). |
| **Coffre zero-knowledge** | Espace chiffré où sont conservés mots de passe et secrets ; la clé est **dérivée côté client** (phrase de récupération + biométrie) et le serveur ne voit que du **chiffré** (Sécurité T-001/T-002). |
| **Phrase de récupération** | Secret utilisateur (type BIP39) servant à **dériver la clé** du coffre et à récupérer l'accès sans déchiffrement serveur ; point sensible (cf. SEC-004). |
| **Mot de passe maître / biométrie** | Facteurs de déverrouillage local du coffre ; combinaison exacte des secrets (**« 2 secrets » vs « 1 kit hardware-backed »**) = **décision patron ouverte** (ETAT §5). |
| **SCA / DSP2 · 3-D Secure (3DS2)** | Cadre européen d'authentification forte des paiements ; **délégué au PSP** — TEL ONLINE **ne stocke aucun OTP bancaire** (garde-fou T-005). |
| **PSP / wallet / EMI** | Prestataire de services de paiement, portefeuilles certifiés (Apple/Google Pay) ; statut d'**émetteur de monnaie électronique (EMI)** = décision de conformité ouverte (ETAT §5.5b ; => /securite T-003). |
| **KYC / AML** | Contrôles d'identité et de lutte anti-blanchiment applicables à tout **flux financier** ; à cadrer en conformité (=> /securite T-003). |
| **Hébergement UE / résidence des données** | Exigence que le stockage, la base et le coffre de secrets soient **en Union européenne** (RGPD) — posée au Registre technique (T-007). |
| **SEC-### / veto Release** | Identifiant d'un constat de sécurité (registre `/securite`) ; un **SEC-### P0** ouvert **bloque toute mise en production** (R10). |
| **Canaux officiels** | Principe : les intégrations passent par les **API/officiels des fournisseurs** (OAuth/OIDC, GSMA RSP, PSP DSP2), jamais par contournement (Registre technique T-007). |

## 5. Décisions actées (patron)

> Cette section ne recense **que** les décisions explicitement arrêtées par le patron au cadrage. Toute autre orientation relève du **BACKLOG** (propositions priorisées) ou des **/tickets** (questions ouvertes). Aucune décision n'est actée ici sans mandat du brief.
>
> - **Source commune** : cadrage patron.
> - **Date de consignation** : 2026-07-23.
> - **Date exacte du cadrage** : à confirmer.
> - **Légende des identifiants** : deux séries distinctes et assumées coexistent — **D-0x** pour les décisions actées (ce fichier) et **B-00x** pour les items de backlog (BACKLOG.md). Le format de padding diffère volontairement d'une série à l'autre.

### D-01 — Modules non clonés

- **ID** : D-01
- **Intitulé** : Modules non clonés.
- **Date** : cadrage (date exacte à confirmer) ; consignée le 2026-07-23.
- **Source** : cadrage patron.
- **Énoncé (officiel, cadrage)** : les **modules métier** sont **définis au cadrage Infra/Sécurité** — **pas de clone « BTP » de « Pilotage »** (source : skill `/memoire-produit` ; `ETAT.md` §0). Le dispositif transverse (9 agents) est cloné de *Pilotage*, mais **pas** les modules métier.
- **Matérialisation** : le périmètre définitif des modules métier est arrêté au **T-004** (`/module-infra` + `/securite`, après T-001/002/003 🟢 **+ validation patron**). Portée fine encore ouverte => BACKLOG Q#13.

### D-02 — Stack fixée au cadrage

- **ID** : D-02
- **Intitulé** : Stack technique fixée au cadrage.
- **Date** : cadrage (date exacte à confirmer) ; consignée le 2026-07-23.
- **Source** : cadrage patron.
- **Énoncé (officiel, cadrage)** : la **stack technique est laissée au cadrage Infra/Sécurité (S0–S2)** (source : skill `/memoire-produit` ; `ETAT.md` §0). Elle sert de socle stable au reste de la roadmap.
- **Matérialisation** : le choix définitif de la stack est arrêté au **T-004** (avec les modules métier, sur validation patron). Le Registre technique (T-007) en pose déjà les intégrations via **canaux officiels** (OAuth/OIDC, GSMA RSP, PSP DSP2, hébergement UE). Détail stack => BACKLOG Q#14.

---

**Renvoi des questions ouvertes.** Toute question ouverte (périmètre / modules définitifs & stack => **T-004** ; coffre zero-knowledge & récupération => **/securite T-002** ; conformité RGPD/DSP2/KYC-AML => **/securite T-003** ; paliers & cadence freemium => **/design** ; décisions patron — dépôt, partenaires eSIM/paiement/hébergeur, « 2 secrets vs kit hardware », statut EMI => **ETAT.md §5**) ainsi que toute **proposition priorisée** figurent, avec leur routage, dans **BACKLOG.md**. Aucune n'est tranchée dans le présent fichier. **FAIT.md** reste vide tant qu'aucun élément n'est **VERIFIED** en production.
