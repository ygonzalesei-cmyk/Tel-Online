# MEMOIRE PRODUIT — BACKLOG (Tel-Online)

> **Consolidation : 2026-07-23**
>
> **Nature de ce document.** Ce backlog est une liste de **propositions priorisées**, **PAS des décisions**. Aucune ligne ci-dessous n'est actée. Les seules décisions actées figurent dans `VISION.md` (décisions patron datées). Toute ligne suffixée **« (à arbitrer => /tickets) »** suppose un choix produit ou d'implémentation **non tranché** : elle doit être arbitrée via un ticket avant réalisation, jamais décidée ici.
>
> **Légende**
> - Préfixe de palier : `[MVP]` / `[V1]` / `[V2]`
> - ID stable : `B-0xx` — série distincte de celle des décisions actées `D-0x` (dans `VISION.md`) ; les deux séries sont assumées comme distinctes.
> - Priorité : `P0` (bloquant / socle) · `P1` (important) · `P2` (souhaitable)
> - Les bornes de semaines (S0, S1, …) sont des **propositions** de séquencement, pas un engagement de planning.

---

## Roadmap (vue d'ensemble)

| Palier | Bornes (proposées) | Objectif |
|---|---|---|
| **MVP** | S0–S2 | Socle zero-knowledge + **preuve de la boucle sauvegarde → restauration à l'identique** (Fidélité + vérification d'intégrité minimale) |
| **V1** | ~S3–S12 | Multi-types de données, restauration robuste, **garde-fous « zéro perte »** (redondance, récupération, intégrité), paliers payants |
| **V2** | S13+ | Usages collectifs (famille, partage) et modules avancés |

> Rappel : les bornes de semaines sont des **propositions** de séquencement, pas un engagement de planning (délimitation exacte à arbitrer => /tickets, cf. Q#12).

---

## MVP (S0–S2)

Objectif : établir le socle zero-knowledge et livrer la **preuve de la boucle sauvegarde → restauration à l'identique** (Fidélité + vérification d'intégrité minimale) sur un premier type de donnée.

> **Portée de la preuve.** Cette preuve démontre la **boucle technique** sauvegarde → restauration, **pas encore** la garantie « zéro perte » complète : la **Résilience** (redondance des copies, cf. `B-026`) et la **Récupérabilité** (mécanisme de récupération, cf. `B-024`) ne sont atteintes qu'en V1. Au stade MVP (gratuit, 1 appareil, sans récupération ni multi-appareils), la donnée reste exposée à la perte de l'unique appareil.
>
> **Périmètre du service** (sauvegarde / synchronisation / les deux) **à confirmer => /tickets** (cf. Q#1) : « sauvegarde » et « restauration » sont employés ici comme termes techniques génériques de la boucle, sans acter le périmètre produit.

### Jalon S0 — Cadrage
- `[MVP] B-001 (P0)` — Cadrer et documenter l'architecture zero-knowledge / chiffrement côté client (modèle de menace, frontières client/serveur).
- `[MVP] B-002 (P0)` — Fixer la stack au cadrage : produire la fiche de stack retenue (le choix précis des technologies est à arbitrer => /tickets).
- `[MVP] B-003 (P0)` — Maquettes des parcours clés : création de compte, espace de stockage chiffré, sauvegarde, restauration.
- `[MVP] B-004 (P0)` — Cadrer le schéma cryptographique cible : dérivation de clé, clés jamais transmises en clair (choix des primitives à arbitrer => /tickets). **Prérequis** du socle `B-006` / `B-007` / `B-008`.
- `[MVP] B-005 (P1)` — Définir la stratégie de tests et la définition de « restauration réussie » (critère de la preuve de la boucle sauvegarde → restauration).

### Jalon S1 — Socle
- `[MVP] B-006 (P0)` — Authentification / création de compte compatible zero-knowledge (le serveur ne voit jamais le secret utilisateur).
- `[MVP] B-007 (P0)` — Chiffrement côté client : chiffrer/déchiffrer localement avant tout envoi.
- `[MVP] B-008 (P0)` — Espace de stockage chiffré de base : structure de stockage chiffrée des données sauvegardées (structuration précise, p. ex. en « coffre », = proposition à arbitrer => /tickets).
- `[MVP] B-009 (P0)` — Palier gratuit = **1 appareil** : enregistrement et rattachement d'un unique appareil au compte.
- `[MVP] B-010 (P0)` — Gestion locale des secrets sur l'appareil (stockage sécurisé de la clé ; modalités à arbitrer => /tickets). **Prérequis** de `B-006` / `B-007`.

### Jalon S2 — Preuve de la boucle sauvegarde → restauration
- `[MVP] B-011 (P0)` — Sauvegarder un **premier type de donnée** (type précis à arbitrer => /tickets).
- `[MVP] B-012 (P0)` — Restaurer ce type de donnée à l'identique sur l'appareil (boucle sauvegarde → restauration).
- `[MVP] B-013 (P0)` — Vérification d'intégrité minimale de la restauration (contrôle anti-corruption sur le premier type).
- `[MVP] B-014 (P1)` — Journal local des opérations de sauvegarde/restauration (base du futur journal d'audit).

---

## V1 (~S3–S12)

Objectif : élargir les types de données, fiabiliser la restauration, poser les **garde-fous** de « zéro perte » et introduire les **paliers payants**. C'est en V1 que la garantie « zéro perte » complète (Résilience via `B-026`, Récupérabilité via `B-024`) est atteinte.

### Multi-types de données & restauration robuste
- `[V1] B-020 (P0)` — Prise en charge de **plusieurs types de données** (liste et priorités à arbitrer => /tickets).
- `[V1] B-021 (P0)` — Restauration robuste : reprise après interruption, restauration partielle/sélective.
- `[V1] B-022 (P1)` — Sauvegardes incrémentales / versionnage des données (stratégie à arbitrer => /tickets).
- `[V1] B-023 (P1)` — Suppression effective : effacement vérifiable côté serveur et sur les copies (modalités à arbitrer => /tickets).

### Garde-fous « zéro perte » + zero-knowledge
- `[V1] B-024 (P0)` — Récupération de compte compatible zero-knowledge : clé / phrase de récupération (mécanisme exact à arbitrer => /tickets). **Pilier Récupérabilité de « zéro perte ».**
- `[V1] B-025 (P0)` — Vérification d'intégrité généralisée : détection de corruption silencieuse sur l'ensemble des données (méthode à arbitrer => /tickets).
- `[V1] B-026 (P0)` — Redondance des copies : réplication des données chiffrées (nombre de copies et emplacements à arbitrer => /tickets). **Pilier Résilience de « zéro perte ».**
- `[V1] B-027 (P1)` — Journal d'audit consultable par l'utilisateur (opérations, appareils, sessions).
- `[V1] B-028 (P2)` — Auto-vérification périodique de restaurabilité (« restore test » automatisé).

### Paliers payants & facturation
- `[V1] B-029 (P0)` — Palier payant ~4,99 : périmètre et limites d'usage (contenu du palier à arbitrer => /tickets ; montant exact à confirmer => /tickets).
- `[V1] B-030 (P0)` — Palier payant ~9,99 : périmètre et limites d'usage (contenu du palier à arbitrer => /tickets ; montant exact à confirmer => /tickets).
- `[V1] B-031 (P0)` — Multi-appareils au-delà du gratuit : rattachement de plusieurs appareils selon palier (règles à arbitrer => /tickets).
- `[V1] B-032 (P0)` — Facturation / gestion des abonnements : souscription, changement de palier, résiliation (prestataire de paiement et cadence de facturation à arbitrer => /tickets).
- `[V1] B-033 (P1)` — Gestion du cycle de vie du compte : passage gratuit ↔ payant, dépassement de quota (politique à arbitrer => /tickets).

---

## V2 (S13+)

Objectif : usages collectifs et modules avancés, une fois le socle et les garde-fous éprouvés.

- `[V2] B-040 (P1)` — Plan **famille** : structure du plan, nombre de membres, gestion des invitations (composition à arbitrer => /tickets).
- `[V2] B-041 (P1)` — Partage de données entre comptes en préservant le zero-knowledge (modèle de partage à arbitrer => /tickets).
- `[V2] B-042 (P2)` — Rôles et permissions au sein d'un plan famille/partagé (à arbitrer => /tickets).
- `[V2] B-043 (P2)` — Modules avancés (fonctionnalités additionnelles à cadrer ; périmètre à arbitrer => /tickets).
- `[V2] B-044 (P2)` — Restauration inter-appareils / migration de compte vers un nouvel appareil (parcours à arbitrer => /tickets).
- `[V2] B-045 (P2)` — Options de récupération avancées (ex. délégation de confiance) compatibles zero-knowledge (à arbitrer => /tickets).

---

## Modèle freemium

> Modèle *freemium* tel que donné par le brief. Les prix sont **approximatifs** (`~`) et la devise **EUR est présumée** (à confirmer => /tickets). La **cadence de facturation** (mensuelle / annuelle / à vie) n'est **pas** donnée par le brief : à confirmer => /tickets (cf. Q#6). Le positionnement exact de chaque palier (nombre d'appareils, quotas, fonctions) n'est **pas** fourni par le brief : chaque case inconnue est marquée `à préciser => /tickets`. Aucun nom marketing n'est inventé ; les paliers gardent leur libellé par le prix.

| Palier | Prix (approx., EUR — cadence à confirmer => /tickets) | Appareils | Quota / stockage | Fonctions incluses |
|---|---|---|---|---|
| Gratuit | 0 € | 1 appareil | à préciser => /tickets | à préciser => /tickets |
| Palier ~4,99 € | ~4,99 € | à préciser => /tickets | à préciser => /tickets | à préciser => /tickets |
| Palier ~9,99 € | ~9,99 € | à préciser => /tickets | à préciser => /tickets | à préciser => /tickets |
| Palier Famille | à préciser => /tickets | à préciser => /tickets | à préciser => /tickets | à préciser => /tickets |

*Rappel de statut : ce tableau est une **proposition de structure** issue du brief, pas une décision actée sur le contenu des paliers. Seuls les deux points suivants sont fixés par le brief : (a) l'existence de ces quatre paliers, (b) le fait que le palier Gratuit est limité à 1 appareil. Tout le reste relève du backlog ou de /tickets.*

---

## Questions ouvertes (route => /tickets)

1. **Définition précise du produit** : périmètre exact du service (sauvegarde ? synchronisation ? les deux ?) sous « zéro perte » et zero-knowledge, au-delà de la synthèse « à confirmer » du brief.
2. **Appareils supportés** : plateformes cibles (mobile iOS/Android, desktop, web ?) et définition de ce qui compte comme « 1 appareil » pour le palier Gratuit.
3. **Types de données prioritaires** : quelles données personnelles sont couvertes en premier (contacts, photos, messages, fichiers, journaux d'appels...) et dans quel ordre.
4. **Contenu exact de chaque palier** : nombre d'appareils, quotas/stockage et fonctions incluses pour Gratuit, ~4,99 €, ~9,99 € et Famille.
5. **Prix du palier Famille** : montant à préciser (et structure : par foyer, par nombre de membres, etc.).
6. **Devise, cadence de facturation et fiscalité** : confirmer EUR ; préciser la **cadence** (mensuelle / annuelle / à vie) ; TVA incluse ou non ; variations par région.
7. **Mécanisme de récupération zero-knowledge** : dispositif retenu compatible zero-knowledge (clé/phrase de récupération, kit de secours, contacts de confiance...) — principe posé comme garde-fou, choix d'implémentation non tranché.
8. **Vérification d'intégrité** : méthode anti-corruption silencieuse retenue (principe de garde-fou ; implémentation à définir).
9. **Redondance des copies** : niveau de redondance et localisation des copies (principe posé ; paramètres à définir).
10. **Suppression effective** : garanties et délais de suppression définitive côté serveur (principe posé ; modalités à définir). Inclut la question d'une éventuelle **récupération après suppression accidentelle** (corbeille / rétention / annulation), non tranchée.
11. **Journal d'audit** : périmètre, contenu et accès au journal d'audit (principe posé ; spécification à définir).
12. **Bornes de semaines V1 / V2** : délimitation exacte des jalons de la roadmap (S0-S2 -> S13+) entre MVP, V1 et V2.
13. **Portée de « modules non clonés »** : signification opérationnelle de la décision patron « modules non clonés » (quels modules, quelles limites).
14. **Stack fixée au cadrage** : périmètre précis de la stack actée au cadrage (à expliciter là où le brief reste au niveau de la décision, sans détailler les composants). Inclut la correspondance « cadrage ↔ S0 », qui est une **proposition de séquencement** et non un fait acté.
15. **Conformité RGPD** (positionnement européen : EUR, TVA, données personnelles) : droit à l'effacement, portabilité / export des données, base légale du traitement, sous-traitance et hébergement des données — à cadrer, sans trancher ici.

---

> **Rappel de gouvernance.** Tout besoin nouveau ou toute question ouverte issue de ce backlog est **routé vers /tickets** et n'est pas tranché ici. Le passage d'une ligne de backlog à une réalisation exige une décision actée en amont (reportée dans `VISION.md`).
