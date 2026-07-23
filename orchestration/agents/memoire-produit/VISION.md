# VISION.md — Mémoire produit TEL ONLINE

> Writer unique : **/memoire-produit**. Source de vérité : « tout ce qui a été demandé depuis le début ».

## Vision
Application **« téléphone virtuel / coffre-fort numérique » TEL ONLINE**, téléchargeable partout
(**iOS, Android, desktop, web/PWA**), **un seul compte**. Promesse **« ZÉRO PERTE »** : en cas de perte/vol,
l'utilisateur **récupère instantanément**, depuis n'importe quel appareil, **toute sa vie numérique + une ligne mobile**.

Contenu centralisé et **chiffré** : comptes (Google & autres, restauration guidée), **eSIM de secours**,
portefeuille d'identifiants/mots de passe, moyens de paiement/validations bancaires **via canaux OFFICIELS uniquement**,
contacts / messages / photos / réglages / liste d'apps. Modèle : **GRATUIT au lancement puis ABONNEMENT (freemium)**.

## Accessible à TOUS + optimisation permanente
Multi-plateforme réelle (1 compte) ; **multilingue** dès le départ + **accessibilité WCAG** ; UX ultra-simple grand
public (onboarding en quelques étapes, sans jargon) ; **offline-first**, léger/rapide, faible conso data/batterie,
appareils bas de gamme + connexions faibles ; **perf = critère qualité permanent** ; tarif d'entrée accessible.

## Architecture & sécurité NON NÉGOCIABLES
- **Coffre ZERO-KNOWLEDGE** (serveur ne lit jamais ; E2E ; clé dérivée d'un secret utilisateur = phrase de récupération + biométrie).
- **Récupération « zéro perte » SANS déchiffrement serveur** (codes hors-ligne, récupération sociale, clé matérielle).
- **Sync multi-appareils chiffrée** + résolution de conflits.
- Intégrations **UNIQUEMENT canaux officiels** (OAuth ; API bancaires officielles + wallets certifiés ; eSIM opérateur partenaire).
- **Hébergement UE**, journalisation, moindre privilège.

## Garde-fous (ce qu'on NE fait PAS — veto Sécurité si violés)
- ❌ intercepter/stocker les **OTP bancaires (3D Secure)** → SCA/DSP2, passkeys, récupération rapide de la ligne.
- ❌ « **cloner** » une eSIM → transfert/re-téléchargement opérateur.
- ❌ centraliser les **mots de passe en clair** → coffre chiffré zero-knowledge.
- ⚠️ tout flux financier ⇒ **KYC/AML** à cadrer.

## Décisions actées (patron)
- **2026-07-23** — Modules métier **NON clonés** de Pilotage (pas de BTP) → **définis au cadrage Infra/Sécurité** (S0–S2).
- **2026-07-23** — **Stack technique** laissée au **cadrage Infra/Sécurité** (S0–S2).

## Glossaire
- **Zero-knowledge** : le serveur ne peut jamais déchiffrer/lire les données utilisateur.
- **eSIM de secours** : ligne mobile récupérable via l'opérateur partenaire (pas de clone).
- **Freemium** : gratuit (coffre de base + 1 appareil) puis abonnement (multi-appareils, eSIM secours, stockage étendu…).
- **Récupération sociale** : restauration via contacts de confiance, sans déchiffrement serveur.
- **SCA/DSP2** : authentification forte du client (paiements) — l'utilisateur reçoit lui-même ses OTP légitimes.
