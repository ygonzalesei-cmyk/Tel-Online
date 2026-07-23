# REGISTRE.md — Sécurité (SEC-###) TEL ONLINE

> Writer unique : **/securite**. Gravité **P0–P3**. **VETO Release tant qu'un P0 est ouvert** (R10).
> Audit **non destructif**. La Sécurité **ne corrige pas** le code métier (elle spécifie / route).

## Échelle de gravité
| Niveau | Signification | Effet |
|---|---|---|
| **P0** | Critique (fuite de données, zero-knowledge cassé, garde-fou violé) | **VETO Release** jusqu'à résolution |
| **P1** | Élevé | À corriger avant release de la fonctionnalité concernée |
| **P2** | Moyen | Planifié (backlog priorisé) |
| **P3** | Faible | Suivi |

## Registre
| SEC-### | Date | Gravité | Sujet | État | Ticket lié |
|---|---|---|---|---|---|
| — | — | — | *(aucune vulnérabilité enregistrée à J0)* | — | — |

## Exigences structurantes (cadrage S0–S2)
- **Coffre ZERO-KNOWLEDGE** : le serveur **ne lit jamais** les données ; E2E ; **clé dérivée d'un secret utilisateur** (phrase de récupération + biométrie).
- **Récupération « zéro perte » SANS déchiffrement serveur** : codes de secours hors-ligne, **récupération sociale**, clé matérielle.
- **Sync multi-appareils chiffrée** + résolution de conflits.
- Intégrations **canaux officiels uniquement** ; **hébergement UE** ; journalisation ; **moindre privilège**.

## Garde-fous (veto si violés) — cf. T-005
- ❌ OTP bancaire (3D Secure) intercepté/stocké → **SCA/DSP2, passkeys**, récupération rapide de la ligne.
- ❌ « clone » d'eSIM → **transfert/re-téléchargement opérateur**.
- ❌ mots de passe en clair → **coffre chiffré** uniquement.
- ⚠️ flux financier ⇒ **KYC/AML** à cadrer (conformité).
