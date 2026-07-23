# REGISTRE-TECH.md — Registre technique (NOMS uniquement, jamais les valeurs)

> Writer désigné au cadrage : **/module-infra** (autres agents → routent leurs ajouts vers Infra).
> **R11** : avant de croire qu'un accès/outil « n'est pas là », **lire ce fichier**. **Aucun secret en clair** :
> on note le **NOM** de la variable/de l'outil et son **emplacement**, **jamais la valeur**.

**T-007 — Registre initial (2026-07-23).** Aligné sur `memoire-produit/VISION.md` (coffre **zero-knowledge**, **eSIM de secours**, intégrations **canaux officiels**, **hébergement UE**) et `securite/REGISTRE.md` (garde-fous, moindre privilège, 0 P0 à J0). Les fournisseurs cités sont des **candidats à valider patron** (rien d'engagé — cf. `ETAT.md` §5). Emplacement des secrets = **coffre de secrets UE** / page « Connexions » — **à confirmer patron** (ETAT §5.4). Les noms définitifs seront complétés au **T-004** (modules métier + stack).

> ⚠️ **Zero-knowledge (garde-fou VISION/Sécurité)** : les clés qui **déchiffrent les DONNÉES UTILISATEUR** sont **dérivées côté client** (phrase de récupération + biométrie) et ne figurent **jamais** côté serveur ni dans ce registre. Le serveur ne détient que du **chiffré**. **Aucune variable ci-dessous ne déchiffre le coffre utilisateur.**
> ⚠️ **Garde-fous** : **aucun OTP bancaire 3-D Secure** intercepté/stocké (SCA/DSP2 délégué) · **eSIM = TRANSFERT** opérateur, **jamais clone** · **aucun mot de passe en clair**.

## Intégrations — canaux OFFICIELS uniquement
| Domaine (VISION) | Canal officiel visé | Accès (noms — détail §Accès/secrets) | Statut |
|---|---|---|---|
| Comptes (Google & autres) | **OAuth 2.0 / OIDC** officiel (Google, Apple ; autres à cadrer) | `OAUTH_GOOGLE_*`, `OAUTH_APPLE_*` | ⏳ Comptes officiels à ouvrir |
| eSIM de secours | **Opérateur partenaire** — provisioning **GSMA RSP SGP.22** (transfert, **pas de clone**) | `ESIM_OPERATOR_*`, `ESIM_SMDP_ADDRESS` | ⏳ Contrat opérateur (RSP) |
| Paiements / abonnement (freemium) | **PSP SCA/DSP2** + **wallets certifiés** (Apple Pay, Google Pay) ; open banking DSP2 optionnel | `PSP_*`, `APPLE_PAY_*`, `GOOGLE_PAY_*`, `OPENBANKING_*` | ⏳ Compte marchand |
| Auth / session (périmètre infra) | **JWT** signés (JWKS), moindre privilège | `IDP_JWT_*` | ⏳ À provisionner (interne) |
| Coffre chiffré (au repos) | **KMS/enveloppe** côté serveur (chiffre le **stockage**, pas les données ZK) | `KMS_KEY_REF` | ⏳ À provisionner |
| Hébergement & données | **UE** (cloud UE, BDD/stockage UE, coffre secrets UE) | `CLOUD_*`, `DATABASE_*`, `OBJECT_STORAGE_*`, `VAULT_*` | ⏳ Compte cloud UE |
| Notifications (e-mail / SMS) | Fournisseur **UE** (SMS de **service** ≠ OTP bancaire) | `EMAIL_*`, `SMS_*` | ⏳ Compte officiel |

## Accès / secrets (noms + emplacement)
> **Emplacement** = « **Coffre UE** » (secret, jamais dans le dépôt) ou « **Config UE** » (variable non secrète, région UE). Statut **⏳** = à provisionner.

### Comptes — OAuth / OIDC (Google, Apple ; autres à cadrer)
| Nom (variable/clé) | Usage | Emplacement | Statut |
|---|---|---|---|
| `OAUTH_GOOGLE_CLIENT_ID` | Client OAuth/OIDC Google (public) | Config UE | ⏳ |
| `OAUTH_GOOGLE_CLIENT_SECRET` | Secret client Google (serveur) | Coffre UE | ⏳ |
| `OAUTH_GOOGLE_REDIRECT_URI` | Callback autorisé | Config UE | ⏳ |
| `OAUTH_GOOGLE_SCOPES` | Scopes OIDC (minimisation) | Config UE | ⏳ |
| `OAUTH_APPLE_CLIENT_ID` | Services ID Apple | Config UE | ⏳ |
| `OAUTH_APPLE_TEAM_ID` | Team ID Apple | Config UE | ⏳ |
| `OAUTH_APPLE_KEY_ID` | Key ID (signe le `client_secret`) | Config UE | ⏳ |
| `OAUTH_APPLE_PRIVATE_KEY` | Clé privée AuthKey `.p8` (ES256) | Coffre UE | ⏳ |
| `OAUTH_APPLE_REDIRECT_URI` | Callback autorisé | Config UE | ⏳ |
| `OAUTH_<AUTRE>_CLIENT_ID` / `_SECRET` | Autres fournisseurs OAuth (à cadrer patron) | Config / Coffre UE | ⏳ à cadrer |

### eSIM de secours — opérateur partenaire (GSMA RSP, transfert, pas de clone)
| Nom | Usage | Emplacement | Statut |
|---|---|---|---|
| `ESIM_OPERATOR_API_BASE_URL` | Endpoint API provisioning opérateur | Config UE | ⏳ |
| `ESIM_OPERATOR_OAUTH_TOKEN_URL` | Endpoint jeton OAuth2 opérateur | Config UE | ⏳ |
| `ESIM_OPERATOR_OAUTH_CLIENT_ID` | Client d'accès API opérateur | Config UE | ⏳ |
| `ESIM_OPERATOR_OAUTH_CLIENT_SECRET` | Secret client API opérateur | Coffre UE | ⏳ |
| `ESIM_OPERATOR_MTLS_CLIENT_CERT_REF` | Réf. certificat client mTLS (Vault) | Coffre UE | ⏳ |
| `ESIM_OPERATOR_MTLS_CLIENT_KEY_REF` | Réf. clé privée mTLS (Vault) | Coffre UE | ⏳ |
| `ESIM_SMDP_ADDRESS` | Adresse SM-DP+ opérateur (flux RSP) | Config UE | ⏳ |
| `ESIM_PROVISIONING_WEBHOOK_SECRET` | Signature des webhooks d'état de profil | Coffre UE | ⏳ |

**Note (transfert, pas clone).** Profil commandé au **SM-DP+** partenaire puis **transféré** vers l'eUICC via activation code / matching id (**GSMA SGP.22** ; extension **SGP.32** IoT planifiée). **EID / ICCID / activation code / matching id / confirmation code = données par profil au runtime** (minimisées, chiffrées en transit), **jamais** des secrets globaux du dépôt. **Jamais de clonage** (garde-fou VISION/Sécurité).

### Paiements / abonnement — SCA/DSP2 + wallets certifiés
| Nom | Usage | Emplacement | Statut |
|---|---|---|---|
| `PSP_PUBLISHABLE_KEY` | Clé publiable (tokenisation côté client) | Config UE | ⏳ |
| `PSP_SECRET_KEY` | Clé API serveur (paiement / abonnement) | Coffre UE | ⏳ |
| `PSP_WEBHOOK_SIGNING_SECRET` | Vérifie l'authenticité des webhooks PSP | Coffre UE | ⏳ |
| `PSP_ACCOUNT_ID` | Identifiant marchand PSP | Config UE | ⏳ |
| `APPLE_PAY_MERCHANT_ID` | Merchant ID Apple Pay | Config UE | ⏳ |
| `APPLE_PAY_MERCHANT_IDENTITY_KEY_REF` | Réf. clé d'identité marchand (validation de session) | Coffre UE | ⏳ |
| `APPLE_PAY_MERCHANT_DOMAIN` | Domaine vérifié (Apple Pay Web) | Config UE | ⏳ |
| `GOOGLE_PAY_MERCHANT_ID` | Merchant ID Google Pay | Config UE | ⏳ |
| `GOOGLE_PAY_GATEWAY` / `_GATEWAY_MERCHANT_ID` | Passerelle PSP (tokenisation) | Config UE | ⏳ |
| `OPENBANKING_CLIENT_ID` / `_CLIENT_SECRET` | Open banking DSP2 (AISP/PISP agréé) — **option** | Config / Coffre UE | ⏳ option |
| `OPENBANKING_QWAC_KEY_REF` / `_QSEALC_KEY_REF` | Réf. clés eIDAS QWAC (mTLS) / QSealC (JWS) | Coffre UE | ⏳ option |

**Garde-fou paiements.** **SCA / DSP2** : authentification forte et **3-D Secure (3DS2) délégués au PSP** ; l'utilisateur reçoit **lui-même** ses OTP légitimes → **aucun OTP bancaire stocké/intercepté**. Cartes **tokenisées** (aucun PAN en clair). Tout flux financier ⇒ **KYC/AML à cadrer** (conformité — T-003).

### Auth / session (JWT) & chiffrement au repos — périmètre infra
| Nom | Usage | Emplacement | Statut |
|---|---|---|---|
| `IDP_JWT_PRIVATE_KEY` | Clé privée de signature des JWT de session (RS256/ES256) | Coffre UE | ⏳ |
| `IDP_JWT_PUBLIC_KEY` | Clé publique (exposée via JWKS) | Config UE | ⏳ |
| `IDP_JWT_KID` | Identifiant de clé (rotation JWKS) | Config UE | ⏳ |
| `IDP_JWT_ISSUER` / `IDP_JWT_AUDIENCE` | Claims `iss` / `aud` | Config UE | ⏳ |
| `IDP_JWT_REFRESH_TOKEN_SECRET` | Signature/validation des refresh tokens | Coffre UE | ⏳ |
| `KMS_KEY_REF` | Réf. clé KMS/enveloppe — chiffre le **stockage au repos** (ciphertext, métadonnées non-ZK) | Coffre UE | ⏳ |

⚠️ `KMS_KEY_REF` protège le **stockage** (défense en profondeur), **pas** le contenu du coffre utilisateur (déjà chiffré côté client — zero-knowledge). **Aucune clé serveur ne déchiffre les données utilisateur.**

### Hébergement & infrastructure — UE (RGPD, résidence des données)
| Nom | Usage | Emplacement | Statut |
|---|---|---|---|
| `CLOUD_API_TOKEN` | Jeton API du fournisseur cloud UE | Coffre UE | ⏳ |
| `CLOUD_PROJECT_ID` | Projet / tenant cloud | Config UE | ⏳ |
| `CLOUD_REGION` | Région — **DOIT** être UE | Config UE | ⏳ |
| `VAULT_ADDR` / `VAULT_NAMESPACE` | Adresse / namespace du coffre de secrets UE | Config UE | ⏳ |
| `VAULT_ROLE_ID` / `VAULT_SECRET_ID` | Auth AppRole appli → coffre | Injecté CI/orchestrateur UE | ⏳ |
| `DATABASE_URL` | Connexion BDD managée (contient des identifiants) | Coffre UE | ⏳ |
| `DATABASE_HOST` / `_PORT` / `_NAME` / `_USER` | Paramètres BDD (non-secrets) | Config UE | ⏳ |
| `DATABASE_REGION` | Région BDD — **DOIT** être UE | Config UE | ⏳ |
| `OBJECT_STORAGE_ACCESS_KEY_ID` / `_SECRET_ACCESS_KEY` | Clés stockage objet S3 UE (blobs chiffrés, sauvegardes) | Coffre UE | ⏳ |
| `OBJECT_STORAGE_ENDPOINT` / `_REGION` / `_BUCKET` | Endpoint / région / bucket — **UE** | Config UE | ⏳ |
| `EMAIL_API_KEY` | API e-mail transactionnel UE (vérif. compte, alertes) | Coffre UE | ⏳ |
| `SMS_API_KEY` / `SMS_API_SECRET` | API SMS de **service** UE (rappels/alertes — **≠ OTP bancaire**) | Coffre UE | ⏳ |
| `SMS_SENDER_ID` | Identifiant d'expéditeur SMS | Config UE | ⏳ |

## Outils / flags
| Outil / flag | Usage | Statut |
|---|---|---|
| `APP_ENV` | Environnement d'exécution (dev / staging / prod) | ⏳ à cadrer |
| `LOG_LEVEL` | Niveau de journalisation (moindre privilège, **sans PII**) | ⏳ à cadrer |
| `RATE_LIMIT_*` | Rate-limit (robustesse infra) | ⏳ à cadrer |
| Gestionnaire de secrets (Vault / KMS UE) | **Source unique** des valeurs (jamais dans le dépôt) | ⏳ à provisionner |
| IaC (Terraform / API officiel) | Provisioning reproductible, **régions UE** | ⏳ à cadrer |

## Règle de mise à jour (R11)
- Toute **nouvelle intégration** ⇒ **ajouter une ligne ici** (nom de variable + emplacement + statut) et le **signaler dans le rapport** (`rapports/module-infra.md`).
- **Rotation / révocation** d'un accès ⇒ mettre à jour la ligne correspondante.
- **Aucune valeur en clair** dans le dépôt — uniquement des **NOMS** ; les valeurs vivent dans le **coffre de secrets UE**.
- Avant de conclure qu'un accès « n'existe pas », **relire ce registre** (R11).
- **Un seul writer** : `/module-infra`. Les autres agents **routent** leurs besoins d'accès vers Infra (ils n'écrivent pas ici).

## Entrées liées & décisions patron
- **Lues au cadrage** : `memoire-produit/VISION.md` (zero-knowledge, eSIM de secours, canaux officiels, UE) · `securite/REGISTRE.md` (garde-fous, moindre privilège, 0 P0 à J0). Registre **aligné**.
- **À trancher patron** (cf. `ETAT.md` §5) : opérateur **eSIM** partenaire ; prestataire **paiement / wallet** ; **hébergeur UE** — connus ou à sourcer ? Emplacement des clés (page « Connexions » vs variables d'env) ?
- **Dépendances** : noms définitifs des **modules métier** et de la **stack** au **T-004** (après T-001/002/003 🟢 + validation patron) ⇒ registre complété en conséquence.
