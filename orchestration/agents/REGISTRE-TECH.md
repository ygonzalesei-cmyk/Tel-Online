# REGISTRE-TECH.md — Registre technique (NOMS uniquement, jamais les valeurs)

> Writer désigné au cadrage : **/module-infra** (autres agents → routent leurs ajouts vers Infra).
> **R11** : avant de croire qu'un accès/outil « n'est pas là », **lire ce fichier**. **Aucun secret en clair** :
> on note le **NOM** de la variable/de l'outil et son **emplacement**, **jamais la valeur**.

## Accès / secrets (noms + emplacement)
| Nom (variable/clé) | Usage | Emplacement (où poser la valeur) | Statut |
|---|---|---|---|
| *(à cadrer)* | — | Page « Connexions » / variables d'env *(à confirmer patron, cf. ETAT §5.4)* | ⏳ |

## Intégrations — canaux OFFICIELS uniquement
| Domaine | Canal officiel visé | Nom d'accès (var) | Statut |
|---|---|---|---|
| Comptes (Google & autres) | **OAuth** officiel | *(à cadrer)* | ⏳ |
| eSIM de secours | **Opérateur partenaire** (transfert/re-téléchargement, **pas de clone**) | *(à cadrer)* | ⏳ |
| Paiements / abonnement | **API bancaires officielles + wallets certifiés** (SCA/DSP2) | *(à cadrer)* | ⏳ |
| Hébergement | **UE** | *(à cadrer)* | ⏳ |

## Outils / flags
| Outil / flag | Usage | Statut |
|---|---|---|
| *(à cadrer S0–S2)* | — | ⏳ |

## Règle de mise à jour
Toute nouvelle intégration ⇒ **ajouter une ligne ici** (nom + emplacement), et le signaler dans le rapport. **Jamais** de valeur en clair dans le dépôt.
