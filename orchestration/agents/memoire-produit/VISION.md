# MÉMOIRE PRODUIT — VISION (Tel-Online)

> **Statut** : consolidation de la mémoire produit par le writer unique (T-008).
> **Date de consolidation** : 2026-07-23.
> **Périmètre du writer unique** : `orchestration/agents/memoire-produit/` (VISION.md, BACKLOG.md, FAIT.md). Le présent fichier ne porte que la **VISION**, le **glossaire** et les **décisions actées**. Le modèle freemium, la roadmap et les questions ouvertes vivent dans BACKLOG.md ; FAIT.md reste vide tant que rien n'est VERIFIED en production.
> **Règle « ne rien inventer »** : ce document ne consigne que ce qui est **acté par le brief / le cadrage**. Tout besoin nouveau ou toute question ouverte est routé vers **BACKLOG.md** (propositions priorisées) ou **/tickets** (questions à instruire) — jamais tranché ici. On distingue strictement : décisions actées (§5, datées) ≠ propositions de backlog ≠ questions ouvertes (=> /tickets).
>
> **Hypothèse produit (à confirmer, NON actée => /tickets, cf. Q#1)** : service de sauvegarde / synchronisation de données personnelles à chiffrement de bout en bout garantissant « zéro perte ». Cette hypothèse oriente la lecture ; elle ne vaut pas décision.

## 1. Promesse produit — « zéro perte »

**La promesse centrale de Tel-Online est simple : aucune donnée confiée par l'utilisateur ne doit jamais être perdue.** Ce que l'utilisateur confie au service reste récupérable, intact et fidèle à l'original, dans le temps et à travers ses appareils.

> **Périmètre du service à confirmer** : la nature exacte du service — **sauvegarde, synchronisation, ou les deux** — n'est **pas actée**. Elle relève de la **Question ouverte #1** (=> BACKLOG.md / /tickets). La promesse « zéro perte » est énoncée indépendamment de ce périmètre.

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
- **Clés détenues par l'utilisateur** : les clés permettant de déchiffrer restent sous le contrôle de l'utilisateur et ne sont jamais transmises en clair au fournisseur.
- **Non-accès structurel** : l'incapacité du fournisseur à lire les données est une propriété de l'architecture, pas une simple politique interne.

**Tension réelle avec la récupération de compte.** Le zero-knowledge crée une tension directe avec l'expérience courante de « mot de passe oublié » : puisque le fournisseur ne détient pas les clés, il ne peut pas, par lui-même, redonner accès aux données. Cette tension **doit être résolue par des garde-fous côté utilisateur — clé ou phrase de récupération — et non par un accès fournisseur** qui romprait la promesse zero-knowledge. Autrement dit, la récupération repose sur des secrets que l'utilisateur détient et peut restaurer, jamais sur une porte dérobée. Les mécanismes précis de récupération (format de la phrase, sauvegarde assistée, récupération sociale, etc.) sont des choix d'implémentation *(=> backlog / /tickets)*.

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
- **Énoncé (strict, tel que mandaté)** : On ne clone pas de modules — les modules ne sont pas clonés / dupliqués.
- **Portée à préciser => /tickets (cf. Q#13)** : toute interprétation opérationnelle n'est **pas** actée — notamment la notion de « module en un seul exemplaire de référence », la « réutilisation », la distinction entre « module cloné » et « module écrit à neuf », et la portée exacte du principe. Ces lectures relèvent des /tickets.

### D-02 — Stack fixée au cadrage

- **ID** : D-02
- **Intitulé** : Stack technique fixée au cadrage.
- **Date** : cadrage (date exacte à confirmer) ; consignée le 2026-07-23.
- **Source** : cadrage patron.
- **Énoncé (strict, tel que mandaté)** : La stack technique est fixée au cadrage (et non plus tard). Elle sert de socle stable au reste de la roadmap.
- **Portée à préciser => /tickets** : la **correspondance cadrage ↔ S0** n'est **pas** actée (elle est une proposition de séquencement => backlog / /tickets). Le détail de la stack n'est pas fourni ici => /tickets si un besoin de documentation apparaît.

---

**Renvoi des questions ouvertes.** Toute question ouverte (périmètre du service — sauvegarde / synchronisation / les deux ; corbeille et rétention ; mécanismes de récupération ; niveau de redondance ; protection des métadonnées ; conformité RGPD — droit à l'effacement, portabilité / export, base légale, sous-traitance ; cadence de facturation ; interprétation opérationnelle des décisions ; etc.) ainsi que toute **proposition priorisée** figurent dans **BACKLOG.md** et sont routées vers **/tickets**. Aucune n'est tranchée dans le présent fichier. **FAIT.md** reste vide tant qu'aucun élément n'est **VERIFIED** en production.
