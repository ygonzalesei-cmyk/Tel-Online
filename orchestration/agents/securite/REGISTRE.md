# REGISTRE.md — Sécurité (SEC-###) TEL ONLINE

> Writer unique : **/securite**. Gravité **P0–P3**. **VETO Release tant qu'un P0 est ouvert** (R10).
> Audit **non destructif**. La Sécurité **ne corrige pas** le code métier (elle spécifie / route).
>
> **MAJ 2026-07-23 — T-001 (modèle de menace) + T-005 (garde-fous).** Ouverture de **42 constats
> SEC-###** (18 P0 ⇒ **Release en VETO**) et de
> **24 garde-fous à veto**. Analyse détaillée : [`MODELE-MENACE.md`](./MODELE-MENACE.md).

## Échelle de gravité
| Niveau | Signification | Effet |
|---|---|---|
| **P0** | Critique (fuite de données, zero-knowledge cassé, garde-fou violé) | **VETO Release** jusqu'à résolution |
| **P1** | Élevé | À corriger avant release de la fonctionnalité concernée |
| **P2** | Moyen | Planifié (backlog priorisé) |
| **P3** | Faible | Suivi |

## Registre
_42 constats — P0 : 18 ·
P1 : 21 ·
P2 : 3 · P3 : 0. Triés par gravité.
Détail (STRIDE, risque, exigence, veto) en **Annexe A**._

| SEC-### | Date | Gravité | Sujet | État | Ticket lié |
|---|---|---|---|---|---|
| SEC-001 | 2026-07-23 | P0 | Recuperation de compte cassant le zero-knowledge (escrow / fallback support lisible) | Ouvert | T-001 · T-005 |
| SEC-002 | 2026-07-23 | P0 | Alteration / re-cle du blob de recuperation de compte | Ouvert | T-001 · T-005 |
| SEC-003 | 2026-07-23 | P0 | Rotation malveillante de la seed / cle maitre (rancongiciel de compte) sans audit ni step-up | Ouvert | T-001 · T-005 |
| SEC-004 | 2026-07-23 | P0 | Phishing de la phrase de recuperation (BIP39) — point unique de defaillance humain | Ouvert | T-001 · T-005 |
| SEC-005 | 2026-07-23 | P0 | Transfert eSIM detourne = primitive de SIM-swap (editeur = operateur = RSP) | Ouvert | T-001 · T-005 |
| SEC-006 | 2026-07-23 | P0 | Transfert / changement d'appareil sans preuve d'intention signee (repudiation du SIM-swap) | Ouvert | T-001 · T-005 |
| SEC-007 | 2026-07-23 | P0 | Alteration de la requete de transfert / du code d'activation eSIM (EID cible, SM-DP+ rogue) | Ouvert | T-001 · T-005 |
| SEC-008 | 2026-07-23 | P0 | Webhook PSP forge / rejoue -> credit fictif du portefeuille | Ouvert | T-001 · T-005 |
| SEC-009 | 2026-07-23 | P0 | SCA/3DS contournable et sans liaison dynamique (dynamic linking DSP2) + alteration des parametres apres validation | Ouvert | T-001 · T-005 |
| SEC-010 | 2026-07-23 | P0 | IDOR / BOLA sur ressources non-ZK (revelation carte virtuelle PAN/CVV, KYC, eSIM, portefeuille) | Ouvert | T-001 · T-005 |
| SEC-011 | 2026-07-23 | P0 | Coffre sans chiffrement authentifie (bit-flipping, AEAD absent) | Ouvert | T-001 · T-005 |
| SEC-012 | 2026-07-23 | P0 | Rollback / ecrasement / troncature du coffre et des sauvegardes (poison sync) | Ouvert | T-001 · T-005 |
| SEC-013 | 2026-07-23 | P0 | Downgrade / empoisonnement des parametres KDF servis par le serveur | Ouvert | T-001 · T-005 |
| SEC-014 | 2026-07-23 | P0 | Livraison d'un client malveillant (OTA / remote-config / CI-CD) exfiltrant le secret maitre | Ouvert | T-001 · T-005 |
| SEC-015 | 2026-07-23 | P0 | Impersonation support/admin et ATO de la console d'administration (minting de session) | Ouvert | T-001 |
| SEC-016 | 2026-07-23 | P0 | Insider / breche avec DB + KMS-HSM dans le meme perimetre -> dechiffrement de masse | Ouvert | T-001 · T-005 |
| SEC-017 | 2026-07-23 | P0 | Escalade via jeton d'authentification (confusion d'algorithme / claim de role forge) | Ouvert | T-001 |
| SEC-018 | 2026-07-23 | P0 | Deverrouillage biometrique non lie materiellement a la cle | Ouvert | T-001 · T-005 |
| SEC-019 | 2026-07-23 | P1 | Jetons bearer non lies a l'appareil (rejeu / vol -> usurpation de session) | Ouvert | T-001 |
| SEC-020 | 2026-07-23 | P1 | Endpoint hostile : extraction memoire / hooking Frida / repackaging / attestation contournee | Ouvert | T-001 |
| SEC-021 | 2026-07-23 | P1 | Certificate pinning absent / faible -> MITM du trafic sensible et du canal SM-DP+ | Ouvert | T-001 |
| SEC-022 | 2026-07-23 | P1 | Metadonnees du coffre non chiffrees (titres, domaines, usernames) | Ouvert | T-001 · T-005 |
| SEC-023 | 2026-07-23 | P1 | Journaux serveur / APM fuyant jetons, KYC, PAN et metadonnees du coffre | Ouvert | T-001 |
| SEC-024 | 2026-07-23 | P1 | Journal d'audit place dans le blob E2E / horodatage fourni par le client -> preuves falsifiables | Ouvert | T-001 |
| SEC-025 | 2026-07-23 | P1 | Fuite d'ecran / seed via snapshot multitache, capture, session-replay, crash reporter, SDK tiers | Ouvert | T-001 · T-005 |
| SEC-026 | 2026-07-23 | P1 | Presse-papier / clavier IME tiers capturant mot de passe maitre, seed, PAN | Ouvert | T-001 · T-005 |
| SEC-027 | 2026-07-23 | P1 | Sauvegarde OS (iCloud / Google) exfiltrant le coffre, des cles deverrouillables et la seed | Ouvert | T-001 · T-005 |
| SEC-028 | 2026-07-23 | P1 | WebView (3DS/KYC/SSO) : cache persistant + pont JS expose au natif | Ouvert | T-001 |
| SEC-029 | 2026-07-23 | P1 | Deep link / custom URL scheme non verifie (interception OAuth/magic-link, deputy confus) | Ouvert | T-001 |
| SEC-030 | 2026-07-23 | P1 | Code / QR d'activation eSIM spoofe, rejoue ou non lie au compte+appareil (quishing) | Ouvert | T-001 · T-005 |
| SEC-031 | 2026-07-23 | P1 | MFA fatigue / prompt bombing / push spoofe + flood-deregistrement du canal SCA | Ouvert | T-001 · T-005 |
| SEC-032 | 2026-07-23 | P1 | Mass-assignment de champs privilegies (kyc_status, tier, plafond, solde) + idempotence de paiement absente | Ouvert | T-001 · T-005 |
| SEC-033 | 2026-07-23 | P1 | Usurpation au KYC/onboarding (deepfake, injection de flux liveness, documents forges) + fuite sous-traitant KYC | Ouvert | T-001 · T-005 |
| SEC-034 | 2026-07-23 | P1 | Alteration des metadonnees de profil eSIM (BPP / Profile Policy Rules) | Ouvert | T-001 · T-005 |
| SEC-035 | 2026-07-23 | P1 | Remote-config / kill-switch abuse (desactivation de controles ou DoS de flotte) | Ouvert | T-001 |
| SEC-036 | 2026-07-23 | P1 | SSRF / absence de segmentation reseau -> acces au plan d'administration et au KMS | Ouvert | T-001 |
| SEC-037 | 2026-07-23 | P1 | DoS de disponibilite du coffre (compteur d'echecs empoisonne / auto-effacement, couplage deverrouillage<->serveur) | Ouvert | T-001 · T-005 |
| SEC-038 | 2026-07-23 | P1 | DoS des parcours critiques (lockout de recuperation, epuisement SM-DP+, gel AML induit, quota de sauvegarde, boucle de deconnexion) | Ouvert | T-001 |
| SEC-039 | 2026-07-23 | P1 | Cle de signature d'intention non materielle / notifications de securite non signees (preuves forgeables) | Ouvert | T-001 |
| SEC-040 | 2026-07-23 | P2 | Passkeys / WebAuthn mal configurees (RP-ID, synchro) + liaison OAuth/SSO sans email verifie | Ouvert | T-001 |
| SEC-041 | 2026-07-23 | P2 | Enumeration de comptes / oracle temporel via les flux de reset et de derivation | Ouvert | T-001 |
| SEC-042 | 2026-07-23 | P2 | Notifications push exposant OTP de transaction, montants et identite de contacts | Ouvert | T-001 · T-005 |

> **Effet R10 :** 18 P0 ouverts ⇒ **Release reste
> en VETO** tant qu'ils ne sont pas résolus/acceptés. Les P0 sont bloquants avant toute mise en production.

## Exigences structurantes (cadrage S0–S2)
- **Coffre ZERO-KNOWLEDGE** : le serveur **ne lit jamais** les données ; E2E ; **clé dérivée d'un secret utilisateur** (phrase de récupération + biométrie).
- **Récupération « zéro perte » SANS déchiffrement serveur** : codes de secours hors-ligne, **récupération sociale**, clé matérielle.
- **Sync multi-appareils chiffrée** + résolution de conflits.
- Intégrations **canaux officiels uniquement** ; **hébergement UE** ; journalisation ; **moindre privilège**.

> Corpus détaillé et testable : **51 exigences dérivées** dans
> [`MODELE-MENACE.md`](./MODELE-MENACE.md) §9 (E2E/AEAD, dérivation Argon2id, récupération sans
> déchiffrement serveur, authentification, eSIM, paiement, sauvegarde, journalisation).

## Garde-fous (veto si violés) — cf. T-005
Chaque garde-fou est une **règle de veto** : toute violation ⇒ **SEC-### P0** + **blocage Release** (R10).
Les **4 imposés** (DoD T-005) :
- ❌ **OTP bancaire (3D Secure) intercepté/stocké** → **SCA/DSP2, passkeys**, récupération rapide de la ligne. *(VETO-V1)*
- ❌ **« clone » d'eSIM** → **transfert/re-téléchargement opérateur** (GSMA SM-DP+), profil unique. *(VETO-V2)*
- ❌ **mots de passe en clair** → **coffre chiffré zero-knowledge** uniquement (Argon2id, plancher client). *(VETO-V3)*
- ⚠️ **flux financier** ⇒ **KYC/AML** obligatoire (à cadrer, lien T-003). *(VETO-V4)*

**24 garde-fous au total** — les 20 additionnels (récupération sans backdoor,
anti-rollback signé, audit WORM inviolable, token binding, anti-MITM, intégrité OTA/CI-CD, anti-IDOR, etc.)
sont détaillés en **Annexe B**.

---

## Annexe A — Détail des constats SEC-###
| SEC-### | Prio | STRIDE dominant | Actif | Risque | Exigence / contrôle | Garde-fou lié |
|---|---|---|---|---|---|---|
| SEC-001 | P0 | Spoofing (+ Elevation of Privilege) | Phrase de recuperation / kit de recuperation de compte + coffre chiffre | Un fallback support/KYC ou un escrow reconstructible cote serveur reintroduit un acces au coffre sans la seed. Un attaquant usurpant le support, ou l'operateur lui-meme (malveillance/requisition), peut reouvrir/reprendre un coffre cense etre illisible cote serveur : contradiction directe avec la promesse zero-knowledge. | Recuperation 100% cote client derivee de la seed ; aucun escrow ni reconstruction possible cote serveur ; aucun chemin support permettant de reouvrir un coffre ; si assistance, prouver que le serveur ne peut mathematiquement pas reconstituer la KEK/DEK. | Pas de mot de passe en clair / coffre chiffre zero-knowledge, derivation robuste |
| SEC-002 | P0 | Tampering | Sauvegardes chiffrees et kit de recuperation de compte | Le backend ou un insider altere le blob de recuperation pour le re-chiffrer vers une cle controlee par l'attaquant, captant le prochain flux de recuperation et prenant le controle du coffre. | Chiffrement authentifie (AEAD) du blob de recuperation, signature client verifiable, ancrage cryptographique a la seed, step-up phishing-resistant avant tout usage, versioning immuable. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-003 | P0 | Repudiation (+ Denial of Service) | Phrase de recuperation / seed + cle maitre derivee (KEK/DEK) | Une rotation de la cle maitre/seed sans step-up ni journal inviolable verrouille durablement l'utilisateur (extorsion/ransomware de compte), est repudiable et non attribuable, et reste irreversible en zero-knowledge. | Step-up phishing-resistant (passkey) + cooldown + notification hors-bande obligatoires avant rotation ; versioning/undo cote client ; journal inviolable append-only cote serveur. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-004 | P0 | Spoofing | Phrase de recuperation / seed (type BIP39) | Faux support/faux ecran/overlay amene la victime a saisir la seed hors du canal legitime. Sa divulgation entraine la compromission totale (coffre, TOTP, identifiants) et des pivots eSIM/paiement, sans aucune trace ni possibilite d'annulation cote serveur. | La seed n'est JAMAIS saisie en ligne ni jamais demandee par le support ; anti-overlay / anti-tapjacking ; passkeys phishing-resistant ; notifications hors-bande et delais sur actions sensibles ; education anti-phishing. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-005 | P0 | Spoofing (+ Elevation of Privilege) | Profils eSIM operateur (RSP / SM-DP+) | Le flux 'changement d'appareil / transfert eSIM' EST la primitive de SIM-swap et le SMS transite sur le propre reseau de l'editeur. Vol du MSISDN -> interception des OTP SMS/voix -> ATO -> pivot paiement DSP2 et recuperation de compte, en cascade. | Authentification liee a l'ancien appareil (passkey) ; confirmation hors-bande + cooldown + gel financier post-changement ; re-KYC ; double controle ; le numero jamais traite comme facteur de confiance ; suppression du repli SMS ; profil unique GSMA (pas de clone). | Pas de clone d'eSIM : transfert operateur SM-DP+, profil unique |
| SEC-006 | P0 | Repudiation | Profils eSIM operateur (RSP / SM-DP+) + cle de signature d'intention | Sans preuve d'intention signee non-repudiable, un transfert eSIM est repudiable et non attribuable ; l'abus support/insider devient indetectable et incontestable. | Signature d'intention en materiel (Secure Enclave/StrongBox) liant utilisateur + appareil + action + horodatage serveur/TSA ; journal inviolable append-only. | Pas de clone d'eSIM : transfert operateur SM-DP+, profil unique |
| SEC-007 | P0 | Tampering | Profils eSIM operateur (RSP / SM-DP+) | Alteration de l'EID / de l'appareil cible dans la requete de transfert, ou du code d'activation, pour rediriger le profil vers un SM-DP+ malveillant ou vers l'appareil de l'attaquant. | Integrite de bout en bout de la requete (signee) ; liaison serveur EID <-> compte ; verification de l'adresse SM-DP+ authentique + pinning ; code d'activation a usage unique, non rejouable. | Pas de clone d'eSIM : transfert operateur SM-DP+, profil unique |
| SEC-008 | P0 | Spoofing (+ Tampering / Elevation of Privilege) | Moyens et flux de paiement (portefeuille, cartes virtuelles, recharges) | Un webhook PSP non authentifie ou rejouable simule un paiement / une SCA / un KYC reussi et credite le portefeuille sans reglement reel (fraude directe + blanchiment). | Signature verifiee (HMAC/mTLS) + anti-rejeu (nonce + timestamp) + idempotence ; jamais de credit sur le seul webhook ; reconciliation avec l'API PSP ; aucun secret/token en clair dans les logs. | Tout flux financier : KYC/AML obligatoire (+ pas d'OTP rejoue) |
| SEC-009 | P0 | Elevation of Privilege (+ Repudiation / Tampering) | Moyens et flux de paiement (cartes virtuelles, virements, recharges) | Decision SCA pilotee cote client et/ou absence de dynamic linking : un attaquant autorise un paiement puis altere montant/beneficiaire entre validation et execution ; paiement repudiable. | SCA imposee et decidee cote serveur/PSP ; dynamic linking liant cryptographiquement montant + beneficiaire a l'authentification ; scellement/verification serveur des parametres avant execution ; passkeys. | Pas d'OTP 3DS stocke/rejoue : s'appuyer sur SCA/DSP2/passkeys |
| SEC-010 | P0 | Elevation of Privilege (+ Information Disclosure) | Cartes virtuelles / documents KYC / profils eSIM / historique de paiement | Acces ou revelation par ID sans controle de propriete expose PAN/CVV, pieces KYC, profils eSIM, historique et soldes d'autres utilisateurs. | Controle de propriete cote serveur systematique (object-level authorization) ; aucun acces direct par ID ; revelation PAN/CVV sous step-up + tokenisation ; tests BOLA automatises. | Tout flux financier : KYC/AML obligatoire |
| SEC-011 | P0 | Tampering | Coffre-fort numerique chiffre (blob E2E opaque) | Absence d'AEAD : alteration ciblee du ciphertext (bit-flipping) et suppression/reordonnancement silencieux d'enregistrements du coffre non detectes. | AEAD obligatoire (AES-256-GCM / XChaCha20-Poly1305) ; MAC couvrant tous les enregistrements + metadonnees ; verification d'integrite au dechiffrement. | Pas de mot de passe en clair / coffre chiffre robuste |
| SEC-012 | P0 | Tampering (+ Denial of Service) | Coffre chiffre (blob E2E opaque) + sauvegardes chiffrees | Le backend sert une version anterieure (rollback vers un etat vulnerable/revoque) ou ecrase/tronque le blob (destruction, poison sync), corrompant ou detruisant le coffre. | Versioning monotone signe cote client (compteur anti-rollback) ; manifeste / arbre de Merkle d'integrite de collection ; versions immuables cote serveur + restauration point-in-time ; detection de divergence. | Pas de mot de passe en clair / coffre chiffre robuste |
| SEC-013 | P0 | Tampering (+ Elevation of Privilege) | Cle maitre derivee cote client (KDF, KEK/DEK) | Le serveur affaiblit le cout/sel du KDF (ou rejoue un sel) : brute-force offline de la KEK possible pour un detenteur du blob ; parametres empoisonnes -> DoS algorithmique. | Parametres KDF minimaux imposes et verifies cote client (Argon2id fort, sel unique aleatoire >= 128 bits) ; jamais dictes librement par le serveur ; borne haute anti-DoS. | Pas de mot de passe en clair / derivation de cle robuste |
| SEC-014 | P0 | Tampering (+ Elevation of Privilege) | Integrite du code applicatif + cle maitre / mot de passe maitre | L'operateur (malveillance/contrainte legale) ou un CI/CD compromis pousse une build/config ciblee capturant le mot de passe maitre au deverrouillage, retournant l'ancre de confiance zero-knowledge contre un utilisateur precis. | Builds reproductibles + transparence binaire ; signature de code + attestation ; interdiction d'OTA de code crypto natif ; pipeline CI/CD durci (SLSA, signature, separation des taches) ; remote-config signee sans capacite de desactiver des controles. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-015 | P0 | Elevation of Privilege (+ Spoofing / Repudiation) | Console d'administration et acces interne privilegie | Un agent support/admin usurpe ou compromis minte une session valide pour agir a la place de l'utilisateur (paiements, eSIM, KYC) et peut alterer/supprimer les journaux pour couvrir l'abus. | MFA phishing-resistant + moindre privilege + acces JIT ; suppression de toute capacite 'se connecter en tant que' ; double controle des actions sensibles ; journaux inviolables WORM/append-only externalises ; separation des taches. | Aucun (transverse plan de controle) |
| SEC-016 | P0 | Elevation of Privilege (+ Information Disclosure) | Donnees KYC, contacts/photos, references PAN, sauvegardes backend | Snapshots DB et backups stockes avec les cles KMS dans le meme perimetre : un insider (DBA/SRE) ou une breche totale dechiffre en masse les stores regules non-ZK et exfiltre la PII. | Separation stricte KMS/HSM hors du perimetre DB ; chiffrement enveloppe avec controle d'acces distinct ; HSM ; tokenisation PAN ; minimisation ; acces JIT audite ; cloisonnement des backups. | Tout flux financier : KYC/AML obligatoire (donnees regulees) |
| SEC-017 | P0 | Elevation of Privilege (+ Spoofing) | Jetons de session et d'authentification (tous les actifs backend) | Verification laxiste (alg 'none', confusion HS/RS, claims de confiance) permet de forger un jeton avec role/tenant arbitraire, ouvrant un acces total au backend. | Verification de signature stricte (algorithmes fixes, cles separees), rejet de 'none' ; autorisation serveur independante des claims client ; verification audience/issuer. | Aucun (transverse) |
| SEC-018 | P0 | Elevation of Privilege (+ Spoofing) | Cle maitre derivee (KEK/DEK) + coffre chiffre | Biometrie 'booleenne' (gate applicatif) contournable par hook ; cle non liee au materiel ; un tiers enrolant sa biometrie deverrouille le coffre ; lecture du coffre sans presence utilisateur. | Cle protegee par Secure Enclave/StrongBox, liee a la biometrie (setUserAuthenticationRequired + invalidation on enrollment change) ; presence utilisateur exigee ; pas de gate booleen cote application. | Pas de mot de passe en clair / derivation de cle robuste |
| SEC-019 | P1 | Spoofing (+ Repudiation) | Jetons de session et d'authentification applicative | Un bearer token vole est rejoue depuis un autre appareil ; attribution faible des operations sensibles. | Token binding (DPoP / mTLS / attestation d'appareil) ; jetons de courte duree + rotation ; revocation cote serveur ; liaison a l'appareil. | Aucun (transverse) |
| SEC-020 | P1 | Tampering (+ Information Disclosure) | Cle maitre en clair transitoire, coffre deverrouille, jetons | Sur appareil root/instrumente : dump memoire de la DEK, hooking runtime du code crypto, app repackagee contournant l'attestation, usurpation d'un client legitime. | Attestation d'appareil/app (Play Integrity / App Attest) ; RASP anti-hook / anti-root ; non-persistance + zeroization de la cle ; minimisation de la fenetre en clair ; jetons lies a l'attestation. | Aucun (transverse) |
| SEC-021 | P1 | Spoofing (+ Tampering / Information Disclosure) | Jetons, sync du coffre, canal SM-DP+, parametres critiques en transit | Sans pinning, un MITM intercepte/altere les reponses API et impersonne le backend ou le SM-DP+ (redirection de profil, vol de jetons). | Pinning robuste (pins de secours + rotation geree) ; TLS strict ; verification de la chaine de certification SM-DP+. | Aucun (transverse) |
| SEC-022 | P1 | Information Disclosure | Coffre-fort chiffre (blob E2E opaque) | Metadonnees en clair cote serveur (titres, domaines, usernames) cassent la promesse zero-knowledge : profilage et ciblage des utilisateurs. | Chiffrer aussi les metadonnees ; minimiser les fuites structurelles (tailles, index) ; padding. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-023 | P1 | Information Disclosure | Jetons de session, donnees KYC, metadonnees, references PAN | Logs applicatifs et observabilite (APM) capturent secrets et PII, directement exploitables en cas de breche ou par un insider (aggrave le scenario breche totale). | Redaction/masquage systematique ; interdiction de secrets/PII en logs ; retention minimale ; controle d'acces + scanning des logs. | Aucun (transverse) |
| SEC-024 | P1 | Repudiation | Journaux d'evenements et preuves de securite | Un journal de securite dans le blob E2E est falsifiable par le detenteur de cle ; l'horodatage client rend la chronologie des preuves manipulable. | Journal serveur inviolable independant du client ; horodatage serveur/TSA ; signatures ; stockage append-only. | Aucun (transverse) |
| SEC-025 | P1 | Information Disclosure | Phrase de recuperation / seed + coffre | Snapshot de l'app en arriere-plan, enregistrement d'ecran, SDK session-replay/crash capturent la seed, le heap ou le texte a l'ecran. | FLAG_SECURE + exclusion de snapshot sur ecrans sensibles ; aucun SDK tiers session-replay/crash sur ces ecrans ; scrubbing du heap. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-026 | P1 | Information Disclosure (+ Spoofing) | Cle maitre derivee / seed / identifiants | Un IME tiers ou service d'accessibilite capte le mot de passe maitre a la source de la derivation ; le presse-papier universel exfiltre seed et PAN. | Clavier securise in-app pour les secrets ; interdiction/avertissement IME tiers sur champs sensibles ; pas de copie de la seed ; effacement automatique du presse-papier. | Pas de mot de passe en clair / derivation de cle robuste |
| SEC-027 | P1 | Information Disclosure | Sauvegardes chiffrees + coffre + seed | Le canal de backup OS contourne le perimetre zero-knowledge et exfiltre le coffre + parametres KDF (brute force offline), voire une cle deverrouillable ou la seed, sans alerte serveur. | Exclusions de backup (attributs no-backup) ; cles hardware-bound non exportables ; seed jamais persistee ; tests CI verifiant les exclusions. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-028 | P1 | Information Disclosure (+ Elevation of Privilege) | Identite numerique, jetons de session, PAN | Le cache WebView persistant conserve PAN, pieces d'identite et cookies de session ; un pont JS expose des methodes natives privilegiees a du contenu tiers. | Pas de cache persistant sur ecrans sensibles ; isolation WebView ; aucun JS bridge expose a du contenu tiers ; allowlist stricte d'origines. | Aucun (transverse) |
| SEC-029 | P1 | Spoofing (+ Elevation of Privilege / Tampering) | Jetons de session, identite, flux de paiement, profils eSIM | Squattage de scheme intercepte le code OAuth / magic-link / token de reset ; un deep link pre-remplit ou declenche un paiement ou un transfert eSIM (deputy confus) ; boucle de crash persistante. | App Links / Universal Links verifies (pas de custom scheme pour secrets) ; PKCE ; validation d'intention + step-up sur actions sensibles ; aucune action automatique via lien ; robustesse aux intents malveillants. | Aucun (transverse) |
| SEC-030 | P1 | Spoofing (+ Elevation of Privilege) | Profils eSIM operateur (RSP / SM-DP+) | QR quishing vers un SM-DP+ rogue, vol du matching ID/ICCID, rejeu du code, telechargement d'un profil non lie au compte+appareil ; fuite du code via logs/support. | Activation liee au compte + appareil (EID) ; code a usage unique non rejouable ; verification SM-DP+ authentique ; aucune fuite du matching ID/ICCID (logs/support) ; preuve liant l'appareil consommateur. | Pas de clone d'eSIM : transfert operateur SM-DP+, profil unique |
| SEC-031 | P1 | Spoofing (+ Denial of Service) | Jetons de session + moyens et flux de paiement | Bombardement d'approbations push (SCA/login) et push spoofe amenent l'utilisateur a approuver ; flood/deregistrement du jeton push supprime le canal SCA. | Number matching + limitation de frequence + contexte affiche ; passkeys plutot que push simple ; canal SCA resilient et protege contre le deregistrement abusif. | Pas d'OTP 3DS stocke/rejoue : s'appuyer sur SCA/DSP2/passkeys |
| SEC-032 | P1 | Elevation of Privilege (+ Tampering) | Donnees KYC + flux de paiement + portefeuille | Mass-assignment eleve le statut KYC / tier / plafond / solde ; les requetes de paiement sans idempotence provoquent double execution ou rejeu. | Allowlist des champs modifiables + controle serveur des champs sensibles ; cles d'idempotence + anti-rejeu + deduplication serveur. | Tout flux financier : KYC/AML obligatoire |
| SEC-033 | P1 | Spoofing (+ Information Disclosure) | Identite numerique et donnees KYC | Deepfake / injection de flux liveness / documents forges franchissent le KYC ; un sous-traitant KYC (ou ses sous-processeurs) expose pieces d'identite et selfies via breche. | Liveness robuste anti-injection (detection d'injection camera, attestation SDK) + verification documentaire ; due diligence + chiffrement + minimisation chez les sous-processeurs ; decisions KYC tracees de facon inviolable et attribuables a l'operateur. | Tout flux financier : KYC/AML obligatoire |
| SEC-034 | P1 | Tampering | Profils eSIM operateur (RSP / SM-DP+) | Alteration du Bound Profile Package ou des Profile Policy Rules pour modifier le comportement/les droits du profil eSIM. | Verification des signatures BPP et de l'integrite RSP conformes GSMA ; controle serveur des regles de politique de profil. | Pas de clone d'eSIM : transfert operateur SM-DP+, profil unique |
| SEC-035 | P1 | Tampering (+ Denial of Service) | Metadonnees de configuration et posture de securite du client + cles d'attestation | Une remote config alteree desactive des controles de securite ; un kill-switch ou une expiration de pin TLS provoque un DoS de flotte. | Config signee et verifiee ; controles de securite non desactivables a distance ; comportement fail-safe ; rotation de pin geree sans point unique de defaillance. | Aucun (transverse) |
| SEC-036 | P1 | Elevation of Privilege | Ensemble des actifs backend, console admin, KMS | Une SSRF ou l'absence de segmentation permet d'atteindre le plan d'administration interne et le KMS depuis un service expose. | Segmentation reseau stricte ; allowlist d'egress ; protection SSRF (validation d'URL, blocage metadata) ; isolation du KMS. | Aucun (transverse) |
| SEC-037 | P1 | Denial of Service | Coffre-fort chiffre + cle maitre derivee | Empoisonnement du compteur d'echecs ou auto-effacement declenchable a distance verrouille/detruit le coffre ; le couplage deverrouillage<->serveur empeche l'ouverture en cas d'indisponibilite backend. | Deverrouillage 100% offline ; compteur d'echecs local resistant ; pas d'auto-wipe declenchable a distance ; possibilite d'undo. | Pas de mot de passe en clair / coffre chiffre zero-knowledge |
| SEC-038 | P1 | Denial of Service | Kit de recuperation, profils eSIM, moyens de paiement, sauvegardes, jetons | Abus cible du rate-limit/lockout du reset, epuisement des tentatives au SM-DP+, declenchement induit des controles AML/velocite (gel), epuisement du quota de backup, boucle de deconnexion forcee. | Rate-limit par identite legitime plutot que par ressource ; alternatives hors-bande ; protection contre l'abus cible ; quotas et alertes de velocite calibres. | Aucun (transverse) |
| SEC-039 | P1 | Repudiation | Cles cryptographiques client d'attestation / de signature d'intention | Une cle de signature d'intention logicielle est forgeable sur app instrumentee ; des notifications de securite non signees et sans accuse rendent la notice repudiable. | Cle de signature d'intention en materiel (Secure Enclave/StrongBox) ; notifications de securite signees avec accuse de reception ; non-repudiation des actions sensibles. | Aucun (transverse) |
| SEC-040 | P2 | Spoofing (+ Elevation of Privilege) | Identifiants / identite numerique / jetons de session | Passkeys synchronisees compromises ou mauvaise config RP-ID ; liaison de compte OAuth/SSO sans email verifie permettant l'appropriation du compte telco. | RP-ID correct et strict ; exigence d'email verifie pour toute liaison de compte ; attestation passkey selon la sensibilite ; revue de la configuration WebAuthn. | Aucun (transverse) |
| SEC-041 | P2 | Information Disclosure | Metadonnees et telemetrie | Les flux de reset et de derivation revelent l'existence de comptes (reponses differenciees, oracle temporel), facilitant le ciblage. | Reponses uniformes ; timing constant ; rate-limit ; pas de divergence observable entre compte existant et inexistant. | Aucun (transverse) |
| SEC-042 | P2 | Information Disclosure | Moyens et flux de paiement (cartes virtuelles, virements, recharges) | Les notifications push affichent OTP de transaction, montants et contacts sur l'ecran verrouille, exposes en cas de vol d'appareil verrouille. | Notifications sans contenu sensible (contenu revele apres deverrouillage) ; aucun OTP en push ; s'appuyer sur SCA/passkeys plutot que sur des codes affiches. | Pas d'OTP 3DS stocke/rejoue : s'appuyer sur SCA/DSP2/passkeys |

## Annexe B — Garde-fous à veto (détail)

### VETO-V1 — 🚩 **IMPOSÉ (T-005)**
> **Règle** : Interdit de stocker, mettre en cache, journaliser, pre-remplir ou rejouer un OTP 3D Secure ou tout OTP bancaire, cote client comme cote serveur, et interdit de piloter la decision d'authentification forte depuis le client => a la place, SCA DSP2 avec liaison dynamique et facteurs phishing-resistant (passkeys/FIDO2, approbation cryptographique liee a l'appareil), decision d'autorisation cote ACS/serveur uniquement.

- **Justification** : Un OTP 3DS stocke ou rejouable devient un secret reutilisable volable qui casse la non-repudiation du paiement. Comme l'editeur EST l'operateur, le canal SMS transite sur le reseau maison : un OTP capte via SIM-swap, log ou notification suffit a valider une transaction. Couvre les menaces Repudiation 'SCA sans liaison dynamique' (P0), 'Contournement SCA/3DS pilote client' (P0) et le rejeu 3DS.
- **Alternative acceptée** : Strong Customer Authentication DSP2 avec dynamic linking (montant + beneficiaire lies cryptographiquement a l'authentification), possession = passkey/FIDO2, decision d'autorisation prise cote serveur/ACS, aucun repli SMS/voix.
- **Référence normative** : DSP2/PSD2 RTS (UE) 2018/389 art. 4 et 5 ; EMVCo 3-D Secure 2.x ; PCI-DSS v4.0 ; NIST SP 800-63B (OTP SMS classe 'restricted').
- **Déclencheur** : Tout parcours de paiement, autorisation de transaction, ajout de beneficiaire, revelation/activation de carte virtuelle, ou toute etape exigeant une SCA.

### VETO-V2 — 🚩 **IMPOSÉ (T-005)**
> **Règle** : Interdit de cloner, dupliquer, exporter, sauvegarder ou reinstancier un profil eSIM hors du mecanisme operateur ; un profil = un EID actif unique => a la place, tout changement d'appareil passe par un transfert RSP GSMA (SM-DP+) avec desactivation atomique de l'ancien profil et preuve d'intention signee.

- **Justification** : Un clone d'eSIM permettrait deux instances du meme MSISDN, donc interception silencieuse des OTP/SCA et SIM-swap indetectable. Couvre 'transfert eSIM abusif' (P0), 'detournement de profil via alteration EID/appareil cible' (P0) et le 'verrou circulaire SIM-swap => perte du 2e facteur' (P0).
- **Alternative acceptée** : Transfert operateur SM-DP+ (GSMA SGP.22/SGP.32), profil lie a l'EID, telechargement lie au couple compte+appareil, cooldown et gel financier post-transfert, re-KYC/step-up avant migration.
- **Référence normative** : GSMA RSP SGP.22 (Consumer) / SGP.32 (IoT) ; GSMA SGP.02 ; GSMA Fraud & Security (serie FS).
- **Déclencheur** : Changement d'appareil, transfert/activation eSIM, restauration de compte impliquant la connectivite.

### VETO-V3 — 🚩 **IMPOSÉ (T-005)**
> **Règle** : Interdit de transmettre, stocker, indexer, journaliser ou traiter cote serveur le mot de passe maitre, la seed ou tout identifiant du coffre en clair, et interdit de deriver la cle avec un KDF faible ou negociable par le serveur => a la place, coffre E2E, derivation cliente memory-hard (Argon2id) a parametres imposes par le client, serveur ne detenant que du chiffre authentifie opaque.

- **Justification** : Le zero-knowledge repose sur l'absence totale de clair cote serveur ; tout clair traite ou KDF faible/downgrade ouvre le brute-force offline apres breche. Couvre 'pas de mot de passe en clair', 'downgrade KDF servi par le serveur' (P0), 'brute-force offline de la KEK' (P1).
- **Alternative acceptée** : Argon2id (ou scrypt) memory-hard, sel unique per-user genere cote client, parametres KDF verrouilles cote client, authentification par PAKE/OPAQUE (le mot de passe n'est jamais envoye), chiffrement AEAD (AES-256-GCM / XChaCha20-Poly1305).
- **Référence normative** : OWASP ASVS v4 (V2.4/V6) ; NIST SP 800-63B ; NIST SP 800-132 ; RGPD art. 32.
- **Déclencheur** : Creation, deverrouillage ou synchronisation du coffre ; authentification ; changement de mot de passe maitre.

### VETO-V4 — 🚩 **IMPOSÉ (T-005)**
> **Règle** : Interdit d'ouvrir, crediter, debiter, recharger ou transferer sur un portefeuille/carte/virement sans KYC/AML prealable proportionne et sans surveillance transactionnelle => a la place, onboarding KYC verifie (liveness anti-deepfake), screening AML/sanctions continu, plafonds/tier fixes cote serveur, et attribution inviolable de chaque decision.

- **Justification** : Obligation reglementaire LCB-FT : sans KYC/AML, blanchiment, fraude et exposition aux sanctions. Couvre 'tout flux financier => KYC/AML', 'usurpation KYC (deepfake/injection liveness)' (P1), 'decision KYC/AML sans trace inviolable' (P1), 'mass-assignment kyc_status/tier' (P1).
- **Alternative acceptée** : KYC verifie (document + liveness passive/active resistant a l'injection de flux), screening PEP/sanctions continu, monitoring de velocite/AML, tier et plafonds non modifiables par le client, journal inviolable des decisions et de leur operateur.
- **Référence normative** : DSP2/PSD2 ; AMLD5/6 (UE 2018/843, 2018/1673) ; LCB-FT (CMF art. L.561-*) ; eIDAS ; RGPD.
- **Déclencheur** : Onboarding paiement, ouverture de portefeuille/carte, tout mouvement de fonds, augmentation de plafond/tier.

### VETO-V5
> **Règle** : Interdit d'implementer une recuperation de compte reconstructible cote serveur : escrow lisible, sequestre de cle, fallback support qui reouvre le coffre, ou key-wrapping dechiffrable par l'operateur => a la place, recuperation strictement fondee sur des secrets detenus par l'utilisateur (seed BIP39 / kit de recuperation hors-ligne), sans capacite de dechiffrement par le fournisseur.

- **Justification** : Toute voie de reconstruction cote serveur detruit la promesse zero-knowledge et cree une cible unique pour insider ou requisition legale. Couvre 'fallback support/KYC cassant le zero-knowledge' (P0), 'recuperation assistee reintroduisant un acces sans la seed / escrow lisible' (P0), 'insider DB+KMS' (P0).
- **Alternative acceptée** : Recuperation par seed cote client uniquement ; kit de recuperation genere et detenu hors-ligne par l'utilisateur ; option de sequestre social/Shamir dont aucune part n'est detenue par l'operateur ; step-up + delais + notifications hors-bande signees.
- **Référence normative** : RGPD art. 25 et 32 (privacy by design, minimisation) ; recommandations ANSSI sur le chiffrement de bout en bout ; NIST SP 800-57 (gestion de cle).
- **Déclencheur** : Conception du parcours de recuperation/reset, rotation de cle maitre/seed, traitement des cas 'compte verrouille' par le support.

### VETO-V6
> **Règle** : Interdit d'envoyer vers toute telemetrie, APM, crash reporter, analytics ou SDK tiers (session-replay) le moindre clair du coffre, de la seed, du mot de passe maitre, d'un PAN/CVV, d'un OTP, d'un contenu KYC, ou tout heap contenant ces valeurs => a la place, telemetrie sans clair, ecrans sensibles exclus de la capture, redaction stricte cote client avant emission.

- **Justification** : Les SDK de replay et de crash capturent texte d'ecran, heap et champs de saisie : exfiltration passive du clair hors du perimetre zero-knowledge. Couvre 'crash reporter et SDK tiers capturant secrets/heap/texte d'ecran' (P1) et 'fuite de la seed via snapshot' (P0).
- **Alternative acceptée** : Allowlist stricte des champs telemetrables, masquage automatique des vues sensibles (FLAG_SECURE/redaction), interdiction des SDK tiers sur les ecrans coffre/paiement/KYC, revue de conformite de chaque SDK.
- **Référence normative** : RGPD art. 5 (minimisation) et 25 ; OWASP MASVS (MSTG-STORAGE/PRIVACY) ; PCI-DSS (aucun PAN/CVV hors perimetre).
- **Déclencheur** : Integration de tout SDK d'observabilite/analytics ; ecrans affichant seed/coffre/paiement/KYC.

### VETO-V7
> **Règle** : Interdit de journaliser (serveur, edge, proxy, mobile) des jetons de session, seed, mot de passe maitre, parametres KDF/sel, PAN/CVV, documents/selfies KYC, codes d'activation eSIM (matching ID/ICCID/SM-DP+) ou payloads de webhook en clair => a la place, redaction/tokenisation systematique, champs sensibles hors des logs, et separation stricte KMS/backups.

- **Justification** : Journaux et APM sont un vecteur d'exfiltration majeur en cas de breche ou d'insider et cassent le zero-knowledge par les metadonnees. Couvre 'journaux fuyant jetons/KYC/metadonnees du coffre' (P0), 'webhooks PSP journalises en clair' (P1), 'code eSIM fuyant via logs' (P1), 'snapshots DB et backups avec les cles KMS dans le meme perimetre' (P0).
- **Alternative acceptée** : Redaction structuree et tokenisation ; sel et parametres KDF jamais journalises ; logs WORM chiffres a acces restreint ; KMS/HSM isole des snapshots et backups.
- **Référence normative** : PCI-DSS v4.0 (req. 3 et 10) ; RGPD art. 32 ; OWASP ASVS V7 ; NIST SP 800-92.
- **Déclencheur** : Toute ecriture de log/trace/metrique cote serveur ou client, ingestion de webhook, pipeline d'observabilite.

### VETO-V8
> **Règle** : Interdit de chiffrer le coffre, les sauvegardes ou tout blob sensible sans chiffrement authentifie, et interdit d'utiliser un ciphertext sans verifier son integrite avant dechiffrement => a la place, AEAD (AES-256-GCM / XChaCha20-Poly1305) avec AAD couvrant version + identifiants, et rejet strict a l'echec du tag.

- **Justification** : Sans chiffrement authentifie, le ciphertext du coffre est alterable par bit-flipping et modifiable silencieusement cote serveur. Couvre 'alteration du ciphertext par absence de chiffrement authentifie (bit-flipping)' (P0) et 'alteration du blob et de ses metadonnees d'integrite par insider/admin' (P1).
- **Alternative acceptée** : AEAD systematique, AAD liant version/compte/enregistrement, MAC verifie obligatoirement avant tout usage, echec de dechiffrement = rejet et alerte.
- **Référence normative** : NIST SP 800-38D (GCM) ; RFC 8439 (ChaCha20-Poly1305) ; OWASP ASVS V6.
- **Déclencheur** : Tout chiffrement/dechiffrement du coffre, des sauvegardes ou du blob de recuperation.

### VETO-V9
> **Règle** : Interdit de servir une version anterieure du coffre/sauvegarde ou d'accepter un ecrasement non versionne (rollback, troncature, reordonnancement, poison sync), et interdit d'executer une rotation destructrice de cle/seed sans delai et confirmation hors-bande => a la place, versionnement monotone signe cote client, compteur anti-rollback, integrite d'ensemble, historique immuable et recuperation multi-version.

- **Justification** : Le backend peut rejouer une ancienne version, tronquer ou detruire le coffre, y compris en rancongiciel de compte. Couvre 'rollback de version servi par le backend' (P0), 'destruction du coffre par ecrasement du blob' (P0), 'rancongiciel : rotation malveillante cle maitre + seed' (P0), 'alteration de l'ensemble des enregistrements' (P1).
- **Alternative acceptée** : Numero de version monotone signe cote client, arbre de Merkle/HMAC couvrant tous les enregistrements, snapshots immuables cote serveur, delai + confirmation hors-bande signee avant toute rotation destructrice.
- **Référence normative** : OWASP ASVS V6/V10 ; NIST SP 800-57 ; principe d'integrite append-only/WORM.
- **Déclencheur** : Synchronisation du coffre, restauration, rotation de cle maitre/seed.

### VETO-V10
> **Règle** : Interdit d'emettre ou d'accepter des jetons de session bearer non lies a l'appareil/cle pour toute operation sensible => a la place, jetons sender-constrained (DPoP ou mTLS) lies a une cle materielle, de courte duree et revocables.

- **Justification** : Un bearer vole permet l'usurpation de session et affaiblit l'attribution des operations sensibles. Couvre 'rejeu de jeton de session non lie a l'appareil' (P0) et 'jetons bearer non lies => attribution faible' (P1).
- **Alternative acceptée** : DPoP (RFC 9449) ou mTLS avec binding a la cle Keystore/Secure Enclave, refresh courts, revocation immediate, liaison au device pour paiement/transfert eSIM/deverrouillage serveur-assiste.
- **Référence normative** : OAuth 2.0 DPoP RFC 9449 ; NIST SP 800-63B ; OWASP ASVS V3 (session).
- **Déclencheur** : Emission/validation de session et toute operation sensible authentifiee.

### VETO-V11
> **Règle** : Interdit d'utiliser le SMS, l'appel vocal ou le MSISDN comme facteur d'authentification, de recuperation, de SCA ou de 2FA => a la place, facteurs phishing-resistant (passkeys/FIDO2, approbation liee a l'appareil avec number-matching) ; le numero n'est jamais un facteur de confiance.

- **Justification** : Editeur = operateur = RSP : le flux 'changement d'appareil / transfert eSIM' EST la primitive de SIM-swap et le SMS transite sur le reseau maison ; tout repli telephonique s'auto-compromet. Couvre la cascade SIM-swap (P0), 'MFA fatigue / prompt bombing' (P1), 'push spoofe' (P1).
- **Alternative acceptée** : Passkeys/FIDO2, TOTP stocke dans le coffre (jamais SMS), approbation push liee a la cle appareil avec number-matching et anti-prompt-bombing (limitation, contexte affiche).
- **Référence normative** : NIST SP 800-63B (SMS 'restricted') ; DSP2 RTS (independance des canaux) ; GSMA FS.
- **Déclencheur** : Login, 2FA, SCA, recuperation de compte, ajout d'appareil.

### VETO-V12
> **Règle** : Interdit de communiquer avec le backend, le SM-DP+ ou le PSP sans TLS a certificate pinning, et interdit d'autoriser un contournement/downgrade du pinning ou l'acceptation des CA utilisateur en release => a la place, pinning cle publique robuste avec rotation gouvernee et refus des CA injectees.

- **Justification** : Sans pinning, un MITM altere les reponses API, vole des jetons et redirige vers un SM-DP+ rogue. Couvre 'absence/contournement du certificate pinning => MITM' (P1), 'impersonation backend/SM-DP+' (P1), 'alteration des reponses API via interception TLS' (P1).
- **Alternative acceptée** : Pinning de cle publique avec set de secours et rotation OTA signee, kill-switch gouverne pour eviter un DoS de flotte, rejet des proxys/CA utilisateur, pas de bypass debug en build de production.
- **Référence normative** : OWASP MASVS (MSTG-NETWORK) ; NIST SP 800-52 ; GSMA RSP (securite du canal SM-DP+).
- **Déclencheur** : Toute connexion reseau app vers backend/SM-DP+/PSP.

### VETO-V13
> **Règle** : Interdit de persister la cle maitre/DEK dechiffree, et interdit de deverrouiller le coffre via une biometrie 'booleenne' non liee cryptographiquement a la cle => a la place, cles en Keystore/Secure Enclave (StrongBox/TEE) avec deblocage exigeant l'authentification biometrique materielle (setUserAuthenticationRequired) et effacement memoire apres usage.

- **Justification** : Une cle residente ou une biometrie booleenne se contourne par dump memoire, hook Frida ou enrolement biometrique tiers. Couvre 'contournement biometrie non liee a la cle' (P0/P1), 'cle/DEK residente extraite par dump ou Frida' (P1), 'enrolement biometrique ajoute par un tiers' (P1).
- **Alternative acceptée** : KEK protegee par le materiel avec invalidation a l'ajout d'empreinte (invalidatedByBiometricEnrollment), DEK ephemere non swappable, zeroization immediate apres usage.
- **Référence normative** : NIST SP 800-63B (AAL) ; OWASP MASVS (MSTG-CRYPTO/AUTH) ; guides Android Keystore / Apple Secure Enclave.
- **Déclencheur** : Stockage de cle, deverrouillage biometrique, enrolement/modification biometrique.

### VETO-V14
> **Règle** : Interdit d'afficher la seed, le mot de passe maitre, un PAN/CVV ou un secret sans protection anti-capture, et interdit de placer un secret dans le presse-papier persistant/universel ou dans le snapshot multitache => a la place, FLAG_SECURE / occultation multitache et presse-papier ephemere non synchronise cross-device.

- **Justification** : Snapshot multitache, enregistrement d'ecran et clipboard universel exfiltrent seed et PAN. Couvre 'fuite de la seed via snapshot multitache et enregistrement d'ecran' (P0) et 'exfiltration mot de passe/seed/PAN via le presse-papier universel' (P1).
- **Alternative acceptée** : FLAG_SECURE (Android) / vue masquee en arriere-plan (iOS), presse-papier avec expiration et marquage sensible (android:isSensitive, non universel), seed non copiable par defaut.
- **Référence normative** : OWASP MASVS (MSTG-STORAGE-9/10) ; RGPD art. 32.
- **Déclencheur** : Ecrans affichant seed/coffre/paiement/KYC et fonctions 'copier'.

### VETO-V15
> **Règle** : Interdit de demander, saisir, transmettre ou valider la phrase de recuperation via une page web, un formulaire en ligne, le support ou tout canal serveur => a la place, la seed n'existe que localement, n'est jamais un input d'un flux serveur legitime, et toute demande de saisie en ligne est traitee comme une attaque.

- **Justification** : La seed est le point unique de defaillance ultime : toute saisie en ligne equivaut a un phishing donnant la compromission totale (coffre, TOTP, identifiants) et les pivots eSIM/paiement. Couvre 'phishing de la phrase de recuperation via faux ecran/overlay/support usurpe' (P0).
- **Alternative acceptée** : Verification de la seed uniquement en local (re-derivation cliente), pedagogie anti-phishing in-app, aucune API acceptant la seed, notifications hors-bande signees sur toute recuperation.
- **Référence normative** : RGPD art. 25 ; OWASP ASVS V2 ; NIST SP 800-63B (resistance au phishing).
- **Déclencheur** : Parcours de recuperation, ecrans de reset, communications support.

### VETO-V16
> **Règle** : Interdit d'inclure le coffre chiffre, les cles, la seed ou les parametres KDF dans une sauvegarde cloud OS (iCloud/Google Backup) ou tout export non maitrise => a la place, exclusion explicite de backup, cles hardware non extractibles, seed non persistee, controle verifie en CI.

- **Justification** : Le canal de sauvegarde OS contourne integralement le perimetre zero-knowledge et permet le brute-force offline du coffre. Couvre 'sauvegarde cloud OS capturant le coffre chiffre et les parametres KDF' (P1) et le scenario 'backup OS exfiltrant des donnees sensibles' (P0).
- **Alternative acceptée** : allowBackup=false / NSURLIsExcludedFromBackupKey, cles en Keystore non exportables, seed non persistee, tests CI verifiant l'absence de secrets dans les backups.
- **Référence normative** : OWASP MASVS (MSTG-STORAGE-8) ; RGPD art. 32 ; guides de sauvegarde plateforme.
- **Déclencheur** : Configuration de sauvegarde de l'app, stockage local de cles/coffre/seed.

### VETO-V17
> **Règle** : Interdit d'autoriser l'acces a une carte virtuelle (PAN/CVV), un document KYC, un profil eSIM, un historique de paiement ou un enregistrement de portefeuille par simple identifiant sans controle de propriete cote serveur, et interdit le mass-assignment de champs privilegies => a la place, autorisation objet-par-objet (ownership/ABAC) deny-by-default et allowlist stricte des champs modifiables.

- **Justification** : IDOR/BOLA sur ces ressources non-ZK provoque une fuite massive de PII et la revelation de PAN/CVV. Couvre 'IDOR sur documents KYC, profils eSIM et historique de paiement' (P0), 'IDOR/BOLA sur revelation PAN/CVV' (P0), 'mass-assignment kyc_status/tier/plafond/solde' (P1).
- **Alternative acceptée** : Verification de propriete a chaque acces, identifiants non enumerables (UUID), pas de mass-assignment (allowlist), tests d'autorisation automatises par role et par objet.
- **Référence normative** : OWASP API Security Top 10 (API1 BOLA / API3 BOPLA) ; PCI-DSS (protection PAN) ; RGPD art. 32.
- **Déclencheur** : Tout endpoint exposant carte/KYC/eSIM/paiement/portefeuille.

### VETO-V18
> **Règle** : Interdit de traiter un webhook PSP/KYC non signe, non horodate, rejouable ou non idempotent comme source de verite d'un paiement/credit => a la place, verification de signature PSP, anti-rejeu (nonce + fenetre temporelle), cle d'idempotence, et reconciliation cote serveur via l'API PSP faisant foi avant credit.

- **Justification** : La forge ou le rejeu de webhook credite le portefeuille sans reglement reel et double des executions. Couvre 'forge de webhook PSP' (P0), 'rejeu de webhook pour simuler un paiement reussi' (P0), 'falsification/rejeu pour crediter sans reglement' (EoP+Spoofing P0), 'rejeu sans idempotence => double execution' (P1).
- **Alternative acceptée** : Signature HMAC/mTLS du PSP verifiee, fenetre anti-rejeu, idempotency-key sur chaque operation, reconciliation par pull vers l'API PSP avant de creer/creediter le ledger.
- **Référence normative** : PCI-DSS v4.0 ; DSP2 ; bonnes pratiques de signature de webhook PSP ; OWASP ASVS V13.
- **Déclencheur** : Reception de tout webhook PSP/KYC, credit/debit du ledger portefeuille.

### VETO-V19
> **Règle** : Interdit de valider ou d'executer un paiement ou un transfert eSIM dont le montant/beneficiaire/cible (EID/appareil) peut etre altere apres la validation utilisateur, ou sans preuve d'intention signee liee a ce que l'utilisateur a vu (WYSIWYS) => a la place, signature d'intention cote appareil (cle materielle) couvrant montant + beneficiaire pour le paiement et EID + appareil pour l'eSIM, verifiee cote serveur avant execution.

- **Justification** : Sans liaison signee de l'intention, le montant/beneficiaire est modifiable apres validation et le transfert eSIM est repudiable. Couvre 'alteration des parametres de paiement apres validation' (P0), 'transfert eSIM sans preuve d'intention signee' (P0), 'SCA sans liaison dynamique' (P0), 'cle de signature d'intention non materielle forgeable' (P1).
- **Alternative acceptée** : Signature d'intention non-repudiable en Secure Enclave/StrongBox (non instrumentable), dynamic linking montant+beneficiaire, affichage strictement egal au contenu signe, verification serveur avant execution.
- **Référence normative** : DSP2 RTS art. 5 (dynamic linking) ; GSMA RSP (autorisation de transfert) ; eIDAS (signature electronique) ; OWASP ASVS V6.
- **Déclencheur** : Confirmation de paiement/virement/recharge ; transfert eSIM / changement d'appareil.

### VETO-V20
> **Règle** : Interdit de placer le journal de securite dans le blob E2E, de permettre a un admin/support de modifier ou supprimer les journaux, de minter une session utilisateur, ou de detenir une capacite de dechiffrement du coffre => a la place, audit serveur append-only inviolable (WORM), separation des pouvoirs, break-glass a double controle trace, aucune capacite de dechiffrement operateur.

- **Justification** : Un journal loge dans le blob est falsifiable par le detenteur de cle, et un admin god-mode = ATO interne indetectable. Couvre 'journal de securite dans le blob E2E' (P0), 'alteration/suppression des journaux par un admin' (P0), 'impersonation support/admin mintant une session' (P0), 'insider DB+KMS dechiffrant en masse' (P0).
- **Alternative acceptée** : Logs append-only signes/WORM hors du blob, dual-control + step-up sur les actions admin, impossibilite d'usurper une identite utilisateur cote serveur, KMS/HSM separe a quorum, moindre privilege.
- **Référence normative** : RGPD art. 32 ; NIST SP 800-92 ; ISO/IEC 27001 A.12.4 ; SOC 2 ; principe du moindre privilege.
- **Déclencheur** : Conception de la journalisation/audit, outillage support/admin, procedure de break-glass.

### VETO-V21
> **Règle** : Interdit de livrer une mise a jour de code (OTA/bundle), une remote-config ou une build non signee, non verifiable, ou capable de cibler un utilisateur pour capturer le clair au deverrouillage => a la place, signature de code obligatoire, builds reproductibles + transparence binaire, et remote-config incapable de desactiver un controle de securite ou de piloter le KDF/pinning.

- **Justification** : L'operateur controlant la distribution du client, c'est le canal de compromission zero-knowledge le plus grave sous contrainte legale ou par insider. Couvre 'injection de bundle/OTA alteree exfiltrant le secret maitre' (P0), 'livraison client malveillant / downgrade KDF via remote-config' (P0), 'pipeline CI/CD signant une release compromise' (P1), 'remote-config desactivant des controles' (P1).
- **Alternative acceptée** : Signature de code + CI/CD durci (SLSA), builds reproductibles publies et attestes (binary transparency), remote-config restreinte a une allowlist de parametres non securitaires, KDF et pinning non pilotables a distance.
- **Référence normative** : SLSA ; NIST SP 800-218 (SSDF) ; OWASP MASVS (MSTG-CODE) ; RGPD art. 25.
- **Déclencheur** : Release/OTA, changement de remote-config, pipeline CI/CD de signature.

### VETO-V22
> **Règle** : Interdit d'accepter un jeton d'authentification avec confusion d'algorithme, alg=none, claim de role non verifie, ou validation permissive => a la place, algorithme fige cote serveur, verification stricte signature/audience/emetteur/expiration, et autorisation resolue depuis l'etat serveur et non depuis des claims clients.

- **Justification** : Confusion d'algorithme ou role forge => escalade de privilege sur l'ensemble des actifs backend. Couvre 'escalade via jeton : claim de role forge ou confusion d'algorithme' (EoP+Spoofing P0).
- **Alternative acceptée** : Algorithme verrouille (pas de negociation), cles asymetriques, verification aud/iss/exp/nbf, roles resolus cote serveur, secret HMAC non devinable et rotate.
- **Référence normative** : RFC 8725 (JWT BCP) ; OWASP ASVS V3 ; NIST SP 800-63B.
- **Déclencheur** : Emission et validation de tout jeton d'auth/session/service.

### VETO-V23
> **Règle** : Interdit d'exposer des methodes natives privilegiees a une WebView chargeant du contenu tiers (3DS/KYC/SSO) ou d'y activer un pont JS non restreint, et interdit de persister PAN/pieces d'identite/cookies dans le cache WebView => a la place, WebView isolee, allowlist d'origines, aucun bridge natif sensible, cache/cookies effaces.

- **Justification** : Un pont JS expose des capacites natives a du contenu tiers et le cache WebView retient des donnees sensibles. Couvre 'pont JS WebView exposant des methodes natives privilegiees' (P1) et 'cache WebView persistant PAN/pieces d'identite/cookies' (P1).
- **Alternative acceptée** : WebView sans bridge natif privilegie, origines whitelistees, cache et cookies purges, aucun stockage de PAN, usage de SFSafariViewController / Custom Tabs quand possible.
- **Référence normative** : OWASP MASVS (MSTG-PLATFORM) ; PCI-DSS ; RGPD.
- **Déclencheur** : Integration d'une WebView 3DS/KYC/SSO.

### VETO-V24
> **Règle** : Interdit de declencher une action sensible (paiement, transfert eSIM, auth, reset) via un deep link ou custom URL scheme non verifie, et interdit d'echanger un code OAuth sans PKCE => a la place, App Links / Universal Links verifies, OAuth Authorization Code + PKCE avec state/nonce, et re-consentement utilisateur explicite avant toute action.

- **Justification** : Le squattage de scheme et l'interception de code OAuth/magic-link permettent le deputy confus et le vol de jeton. Couvre 'detournement de deep link / squattage de scheme' (P1), 'interception de code OAuth via scheme non verifie' (P1), 'deputy confus declenchant une action privilegiee' (P1), 'deep link declenchant paiement/transfert eSIM' (P1).
- **Alternative acceptée** : Liens verifies (assetlinks.json / apple-app-site-association), PKCE (RFC 7636) obligatoire, validation state/nonce, aucune auto-execution d'action sensible sans re-consentement.
- **Référence normative** : RFC 7636 (PKCE) ; RFC 8252 (OAuth for native apps) ; OWASP MASVS.
- **Déclencheur** : Traitement des deep links, callbacks OAuth/SSO, magic-links, liens d'activation eSIM.


## Annexe C — Veille réglementaire

| Cadre | Exigence | Impact produit |
|-------|----------|----------------|
| DSP2/PSD2 — RTS SCA (art. 97, 98 & exemptions) | La SCA s'applique aussi a l'ACCES en ligne au compte de paiement, pas seulement aux paiements; et le RTS impose de gerer les exemptions (faible montant <30 EUR/cumul, TRA/analyse de risque, beneficiaires de confiance, recurrents) de facon controlee cote serveur et auditable. | Le modele traite la SCA comme binaire/toujours active et cote paiement uniquement. Manque: SCA sur l'ouverture du wallet/coffre de paiement, et un moteur d'exemptions serveur (sinon soit friction/non-conformite, soit exemptions pilotables cote client). A rendre auditable et lie a la liaison dynamique (V19). |
| DSP2/PSD2 — reporting d'incident majeur & fraude | Obligation de notifier les incidents operationnels/securite majeurs a l'autorite competente dans les delais, et de reporter les statistiques de fraude. | Aucun pipeline de detection->qualification->notification d'incident ni de reporting fraude n'est prevu. A ajouter comme processus et comme exigence de journalisation (alimentee par l'audit WORM). |
| Monnaie electronique — EMD2 / agrement EMI & safeguarding | Emettre de la monnaie electronique (solde de portefeuille) requiert un agrement EMI (ou partenariat), le cantonnement/safeguarding des fonds clients, et le respect des seuils de prepaye anonyme (limite ~150 EUR sans KYC complet). | Le portefeuille n'est pas positionne au regard d'EMD2. Impacte l'architecture (comptes segregues, reconciliation), le KYC (seuils anonymes) et la solvabilite du ledger. |
| PCI-DSS + PCI 3DS | L'affichage/transmission de PAN+CVV dans l'app place le client et le chemin API dans le perimetre PCI-DSS (masquage PAN, Req 3/4/10, SAQ/AoC); l'integration 3DS releve de PCI 3DS. | La revelation de carte virtuelle (PAN/CVV) etend le scope PCI a l'app entiere. Reduire le scope: reveler le PAN via un composant isole PCI-scoped/tokenise, masquer par defaut, exclure PAN des logs/analytics (deja en veto) et formaliser le scoping PCI + PCI 3DS. |
| RGPD — Art. 35 DPIA & Art. 9 biometrie | Traitement a grande echelle de donnees financieres + biometrie de liveness (categorie particuliere) + profilage AML => DPIA obligatoire et base legale Art. 9 explicite pour le liveness/face-match. | Aucune DPIA ni base Art. 9 mentionnee. Bloquant pour la mise en production du KYC biometrique; conditionne retention et minimisation du selfie/gabarit. |
| RGPD — Art. 22 decision automatisee | Blocage/rejet KYC-AML et gel de fonds automatises (effet juridique/significatif) ouvrent droit a intervention humaine, contestation et explication. | Le gel AML n'est vu que comme DoS. Ajouter human-in-the-loop, canal de contestation, et tracabilite du motif de decision. |
| RGPD — Art. 17 effacement vs immuabilite WORM & Art. 5 minimisation/retention | Concilier journaux/historiques immuables (imposes par V20/V9) avec le droit a l'effacement et des durees de retention definies par categorie. | Introduire crypto-shredding et pseudonymisation dans les journaux; definir une politique de retention par categorie distinguant donnees a retention legale (telco/AML) et donnees effacables. |
| RGPD — Art. 28 sous-traitants & Chapitre V transferts (Schrems II) | DPA avec chaque sous-processeur (PSP, KYC, cloud, SM-DP+, analytics), transparence des sous-processeurs, et encadrement des transferts hors-UE (SCC + mesures) — notamment tokens/payloads push vers Apple/Google (US). | Non traite. Impacte le choix des fournisseurs, la localisation des donnees, et la minimisation des payloads push (reduire l'exposition transfrontaliere). |
| RGPD — Art. 33/34 notification de violation | Notification a l'autorite sous 72h et aux personnes; le chiffrement fort est un facteur attenuant reconnu. | A exploiter: le ZK correct dispense de notifier pour le ciphertext, MAIS les stores non-ZK (KYC/AML, paiement, metadonnees, identifiants eSIM) restent notifiables. Definir la matrice de notifiabilite par store. |
| GSMA RSP — SGP.22 & SAS-SM | Un operateur exploitant son propre SM-DP+/SM-DS doit etre accredite SAS-SM (Security Accreditation Scheme) et respecter SGP.22 (certificats ancres GSMA-CI, Profile Policy Rules, LPA/eUICC). | Le modele ne verifie ni l'accreditation SAS-SM ni l'ancrage GSMA-CI ni le SM-DS/LPA. A ajouter comme exigences de conformite et surfaces a modeliser. |
| eIDAS / verification d'identite a distance (PVID en France) | Si l'app revendique une 'identite numerique' ou onboarde par verification d'identite a distance regulee, alignement eIDAS et, en France, referentiel PVID (ANSSI) pour le prestataire de verification d'identite a distance; signature d'intention 'juridiquement contraignante' => exigences de signature electronique. | Le KYC video et l''identite numerique' ne sont pas rattaches a un cadre de certification. Viser une brique certifiee PVID; ne pas sur-vendre l'intention-signing comme signature qualifiee sans conformite eIDAS. |
| AML — AMLD5/6, futur AMLR/AMLA & reporting FIU (Tracfin) | CDD/EDD, screening PEP et sanctions (UE/OFAC), surveillance continue, declaration de soupcon (SAR/STR) a la CRF (Tracfin en France) et conservation 5 ans. | L'AML est vu comme controle d'acces, pas comme obligation declarative. Ajouter le pipeline SAR/STR + retention 5 ans (a concilier avec l'effacement RGPD). Le ZK n'exonere pas: la surveillance porte sur les flux de paiement, pas sur le coffre. |
| Retention & interception legales telco (post-CJEU La Quadrature/Tele2) | Obligations de conservation ciblee des metadonnees de trafic et capacite d'interception legale (LI) voix/SMS/data cote operateur. | Ces obligations imposent une infrastructure mandatee (surface d'attaque/insider) qui doit etre architecturalement isolee du coffre ZK; a modeliser et a documenter comme frontiere de confiance (le vault ne doit jamais etre atteignable via LI/retention). |
| ePrivacy / consentement traceurs & communications | Consentement prealable pour analytics/attribution/session-replay et acces au terminal (cookies WebView, identifiants publicitaires), au-dela de la seule minimisation. | Les SDK analytics/attribution/remote-config sont listes en surface mais sans gating de consentement. Ajouter un consent management conditionnant l'activation des SDK tiers. |

## Annexe D — Angles morts & arbitrages (à statuer)

### D.1 — Angles morts / compléments (intégrer ou déclarer hors-scope)

| Gravité | Domaine | Manque | Recommandation |
|---------|---------|--------|----------------|
| P1 | Surface — Autofill / service d'accessibilite (specifique gestionnaire de mots de passe) | Le modele traite l'IME/presse-papier mais PAS le fournisseur d'autofill (iOS AutoFill Credential Provider / Android Autofill Framework / service d'accessibilite) qui est la surface centrale d'un password manager mobile. Attaques non couvertes: autofill vers un mauvais package/domaine (target-hijacking, classe AutoSpill), app malveillante ou WebView usurpant un domaine pour aspirer un identifiant, remplissage dans une WebView sans verification d'origine, overlay au-dessus du prompt d'autofill. | Lier l'autofill a la signature verifiee du package (Android Digital Asset Links / verified app links) et aux Associated Domains (iOS); interdire l'autofill dans une WebView sans verification stricte de l'origine; confirmation explicite avant remplissage cross-app; detection d'overlay pendant le remplissage. |
| P1 | Design — concentration mot de passe + secret TOTP dans le meme coffre (effondrement du MFA tiers) | Le coffre stocke a la fois les mots de passe ET les secrets TOTP des comptes tiers. La compromission du coffre livre donc simultanement le facteur 1 (mot de passe) et le facteur 2 (TOTP) de tous les comptes externes: le MFA de l'ecosysteme entier de l'utilisateur s'effondre en un point unique. Cette concentration n'est pas modelisee comme aggravation d'impact. | Traiter la compromission du coffre comme compromission MFA complete dans l'analyse d'impact; envisager la separation cryptographique des secrets TOTP; recommander des passkeys materielles pour les comptes tiers critiques; avertir l'utilisateur du couplage. |
| P0 | Surface telco — portage sortant (port-out) et fonctions reseau (renvoi d'appel, messagerie vocale) | Le modele couvre le transfert eSIM in-app comme primitive de SIM-swap mais IGNORE deux vecteurs telco distincts que l'editeur=operateur expose obligatoirement: (1) le portage sortant du MSISDN vers un autre operateur (port-out fraud, obligation reglementaire de portabilite), et (2) le renvoi d'appel conditionnel / messagerie vocale permettant d'intercepter un OTP vocal sans toucher a l'eSIM. | Verrou de portage (port-out PIN + gel + notification hors-bande + cooldown); monitoring et notification hors-bande de tout changement de renvoi d'appel/voicemail; supprimer toute dependance a l'OTP vocal; traiter port-out et call-forwarding comme des chemins de SIM-swap au meme titre que le transfert eSIM. |
| P1 | Actif/Surface — client web / extension navigateur / synchronisation multiplateforme | Le modele est 'mobile-first' mais un coffre de mots de passe implique quasi systematiquement un acces web/desktop/extension. Or le crypto delivre en JS par le serveur (web) est une surface supply-chain/XSS bien plus grave que l'OTA mobile (deja couvert): un serveur compromis pousse du JS malveillant capturant le clair au deverrouillage, sans signature de code app-store. Cette surface n'apparait nulle part. | Si un acces web/extension existe: le modeliser explicitement (CSP stricte, SRI, integrite de code cote client, isolation), ou declarer contractuellement le hors-scope. Ne pas laisser un chemin non modelise contourner tout le durcissement mobile. |
| P2 | Fonctionnalite — acces d'urgence / heritage / partage de coffre (re-chiffrement) | Aucune mention du partage d'identifiants/notes ni de l'acces d'urgence/heritage, features standard des password managers. Mal concues, ce sont des escrow deguises qui cassent le zero-knowledge (VETO-V5) ou des chemins de re-chiffrement mal revoques. | Si presentes: re-chiffrement vers la cle publique du destinataire (jamais via serveur dechiffrable), acces d'urgence a delai + veto utilisateur, revocation d'acces effective (rotation), et inclusion explicite dans le modele. Sinon, documenter l'absence comme choix. |
| P1 | Surface paiement — push provisioning des cartes vers wallets OS (Apple Pay / Google Pay) | La provision d'une carte virtuelle dans un wallet OS (tokenisation 'push provisioning', parcours ID&V green/yellow path) n'est pas modelisee. Le yellow path repose souvent sur un OTP SMS/e-mail: un attaquant peut provisionner le token de la carte de la victime sur SON appareil et depenser sans jamais toucher au coffre. | Imposer un step-up in-app controle par l'emetteur (approbation liee a l'appareil) pour toute provision de token, jamais d'OTP SMS; notification hors-bande; lier le token a l'appareil enregistre. |
| P1 | Actif reglemente — donnee biometrique de liveness KYC (RGPD Art. 9) & verification d'identite a distance | Le deverrouillage biometrique OS (on-device) est couvert, mais le liveness/face-match du KYC constitue un TRAITEMENT de donnee biometrique (categorie particuliere Art. 9 RGPD) par le responsable, non modelise comme actif ni comme obligation. En France (utilisateur .fr), la verification d'identite a distance pour onboarding regule releve du referentiel PVID (ANSSI) et de l'alignement eIDAS. | Classer le gabarit biometrique de liveness comme actif special-category; base legale Art. 9 explicite; minimisation/retention courte du selfie et du gabarit; viser une brique de verification d'identite certifiee (PVID en France); DPIA obligatoire. |
| P1 | Dimension — decision automatisee AML/KYC (repudiation & droits de la personne) | Le gel AML n'est traite que comme DoS. Or blocage/rejet KYC et gel de fonds sont des decisions automatisees a effet juridique/significatif: cote RGPD Art. 22 (droit a intervention humaine, contestation, explication) ET cote non-repudiation (l'utilisateur doit pouvoir obtenir la preuve/justification d'une decision qui l'affecte). | Human-in-the-loop pour les decisions bloquantes, canal de contestation, journalisation inviolable du motif de decision, et fourniture d'une preuve verifiable a l'utilisateur (utile aussi pour les litiges DSP2). |
| P0 | Surface/Contrainte — retention legale telco & interception legale (LI) vs promesse ZK | L'operateur est soumis a des obligations de retention de metadonnees de trafic et de capacite d'interception legale (LI) sur voix/SMS/data. Ces infrastructures mandatees sont des surfaces d'attaque et des vecteurs insider NON modelises, et creent une tension directe avec la promesse de confidentialite. Le modele ne dit pas comment le plan LI/retention reseau est isole du coffre. | Isoler architecturalement LI et retention au plan reseau, documenter la frontiere prouvant que le vault n'est jamais atteignable via LI, inclure l'infra LI dans le modele insider, et communiquer la portee (transparence). |
| P0 | Actif reglemente — portefeuille = monnaie electronique (EMD2/EMI) & safeguarding des fonds | Un portefeuille detenant un solde constitue de la monnaie electronique: emission soumise a agrement EMI, safeguarding des fonds clients en comptes segregues, et exigences EBA. La solvabilite/integrite du ledger (double-depense interne, reconciliation float/settlement) n'est pas modelisee au-dela de l'idempotence webhook. | Agrement EMI ou partenariat BIN/EMI; cantonnement des fonds; ledger a double-entree reconcilie et audite; controle d'integrite/solvabilite du solde distinct de la simple idempotence. |
| P2 | Dimension — post-quantum / harvest-now-decrypt-later sur ciphertext longue duree | Evoque dans un scenario de breche mais aucune exigence associee. La duree de confidentialite requise d'un coffre (identifiants, secrets) se compte en decennies: les blobs exfiltres aujourd'hui sont dechiffrables demain par un adversaire disposant d'un calculateur quantique (moisson-differee). | Plan de migration PQC (hybridation KEM post-quantique pour l'echange/enveloppement de cle, agilite crypto versionnee dans l'AAD), a defaut d'implementation immediate au moins une trajectoire documentee. |
| P1 | Scenario — fraude par tele-assistance / RAT (partage d'ecran social-engineere) | Le scenario 'appareil compromis' suppose malware/root. Il manque le vecteur en forte croissance ou la victime est convaincue d'installer un outil de partage d'ecran/controle a distance (type support telco usurpe) et effectue elle-meme les operations sensibles pendant que l'attaquant observe/guide: aucun facteur cryptographique n'est casse, mais l'attaquant voit la seed/PAN et pilote le paiement. | Detection de partage d'ecran/enregistrement et d'overlay pendant les flux sensibles (occulter seed/PAN/validation), avertissements in-flow, cooldown sur actions declenchees pendant une session de partage d'ecran detectee. |
| P1 | Surface eSIM — SM-DS (Discovery Server), LPA/eUICC et gouvernance des certificats GSMA-CI | Le modele s'arrete au SM-DP+ et au code d'activation. Il ignore le SM-DS (serveur de decouverte) et le flux de notification associe, l'agent LPA/eUICC cote appareil, et l'ancrage des certificats a la GSMA-CI. Un SM-DS ou un LPA compromis/usurpe permet de pousser/decouvrir des profils hors du chemin modelise. | Modeliser SM-DS et LPA/eUICC comme surfaces; verifier l'ancrage GSMA-CI des certificats SM-DP+/SM-DS; notifications de changement de profil signees et verifiees; accreditation SAS-SM si l'operateur exploite son propre SM-DP+/SM-DS. |
| P2 | Dimension — preuve verifiable cote utilisateur (repudiation dans le sens fournisseur -> utilisateur) | L'audit inviolable (V20) est interne (WORM cote serveur). Il manque la face symetrique: fournir a l'utilisateur des recus/preuves signes et verifiables des operations sensibles (paiement, transfert eSIM, rotation de cle, decision KYC) pour qu'il puisse contester/prouver en cas de litige DSP2 ou de fraude — sans quoi la non-repudiation ne protege que le fournisseur. | Recus signes (cle serveur) horodates par une source de confiance, exportables et verifiables hors-ligne par l'utilisateur, couvrant montant/beneficiaire (paiement) et EID/appareil (eSIM). |
| P2 | Surface — WebView autofill et confusion de domaine (au-dela du cache/pont JS deja couvert) | SEC-028 couvre cache persistant et pont JS, mais pas la confusion de domaine lors de l'autofill/saisie dans une WebView 3DS/KYC/SSO: un contenu tiers charge dans la WebView peut solliciter un remplissage d'identifiant destine a un autre domaine, exfiltrant le credential vers la mauvaise origine. | Jamais d'autofill du coffre dans une WebView; verification stricte de l'origine avant toute saisie assistee; isolation de process de la WebView 3DS/KYC. |
| P1 | Reglementaire/Design — tension audit WORM immuable vs droit a l'effacement RGPD | VETO-V20 impose des journaux immuables (WORM) et VETO-V9 un historique immuable, sans reconcilier avec le droit a l'effacement (Art. 17) ni les durees de retention. Sans mecanisme, l'immuabilite devient une violation RGPD; a l'inverse une purge naive casse l'inviolabilite. | Pseudonymisation dans les journaux + crypto-shredding (effacement par destruction de cle) pour concilier immuabilite et effacement; durees de retention definies et documentees par categorie; distinguer donnees a retention legale (telco/AML) des donnees effacables. |
| P1 | Reglementaire — sous-traitants et transferts internationaux non modelises (APNs/FCM, PSP, KYC, cloud, SM-DP+) | Le modele liste des SDK/tiers en surface mais aucune exigence Art. 28/Chapitre V RGPD: DPA, cartographie et transparence des sous-processeurs, et surtout transferts hors-UE (tokens et payloads push vers Apple/Google US, PSP, fournisseur KYC, analytics) sous Schrems II/SCC. | Registre des sous-processeurs, DPA Art. 28, evaluation des transferts (SCC + mesures supplementaires), minimisation du contenu des push (deja partiellement couvert par SEC-042) pour reduire l'exposition transfrontaliere. |

> **Note VISION.** Le **client web/PWA + desktop** (multi-plateforme, `VISION.md`) est **confirmé en
> périmètre** : la crypto délivrée en JS côté web est une surface supply-chain/XSS plus grave que l'OTA
> mobile (cf. angle mort ci-dessus) et doit être modélisée (CSP stricte, SRI, intégrité de code, isolation).

### D.2 — Exigences potentiellement sur-dimensionnées (proportionnalité)

- Double secret seed BIP39 + mot de passe maitre pour un produit telco grand public: la redondance de deux secrets a haute entropie independants est un modele de wallet crypto mal adapte a une base de masse. Elle degrade l'UX, pousse l'utilisateur a photographier/stocker la seed (annulant FLAG_SECURE/V14 et alimentant le phishing SEC-004) et augmente le DoS de verrouillage permanent (SEC-037/038). Un unique kit de recuperation a haute entropie, hardware-backed, suffirait au meme niveau de garantie ZK.
- mTLS a certificats clients sur mobile a l'echelle de la flotte (option de VETO-V10): le provisioning/rotation de certificats clients sur des terminaux grand public est disproportionne et fragile. DPoP (jetons sender-constrained lies a une cle Keystore/Secure Enclave) atteint le meme objectif anti-rejeu sans l'operationnel mTLS. A conserver DPoP, retirer mTLS de l'exigence par defaut.
- Builds reproductibles + transparence binaire comme exigence dure (VETO-V21): louable mais quasi inatteignable pour des binaires signes par les stores iOS/Android; risque de devenir une exigence 'papier'. Une provenance verifiable (attestation de build type SLSA) + signature de code + remote-config incapable de piloter KDF/pinning est proportionnee et suffisante.
- WYSIWYS avec signature d'intention materielle sur CHAQUE paiement quel que soit le montant (VETO-V19): disproportionne pour les micro/faibles montants et incoherent avec les exemptions SCA faible montant du RTS. A rendre proportionnel au risque (seuils), en s'alignant sur le moteur d'exemptions DSP2 plutot qu'en imposant une signature materielle systematique.
- Certificate pinning impose sur les canaux tiers PSP/3DS/ACS (VETO-V12): epingler des hotes dont on ne controle pas la rotation de certificats (ACS emetteur, pages 3DS/PSP) est fragile et provoque precisement le DoS de flotte a l'expiration du pin que le modele signale lui-meme (SEC-035/DoS kill-switch). Limiter le pinning au backend first-party et au SM-DP+; pour les tiers, s'appuyer sur validation de chaine + CT plutot que sur un pin rigide.

### D.3 — Décisions nécessaires (R7 — routées patron / autres agents)
- **Deux secrets** (phrase de récupération **+** mot de passe maître) **ou un seul kit** hardware-backed ? (UX vs risque).
- **Portefeuille = monnaie électronique** (agrément EMI + safeguarding) ? — lien **T-003** (KYC/AML).
- **Isolation LI / rétention télco** vs coffre ZK : frontière à prouver (routage **Infra**).
- **Périmètre PCI-DSS** de la révélation PAN/CVV ; **certification PVID/eIDAS** de la vérification d'identité.
- **Client web/PWA** : modélisation dédiée (routage **Infra** + **Design** pour l'UX des flux sensibles).

## Journal
| Date | Événement |
|------|-----------|
| 2026-07-23 | Ouverture du registre : 42 SEC-### (18 P0, 21 P1, 3 P2) + 24 garde-fous à veto (T-001 + T-005). Réconcilié avec VISION.md / _REGLES-COMMUNES.md / skill /securite. Audit non destructif. **Release en VETO (P0 ouverts).** |

> **Convention de suivi.** Faire évoluer la colonne *État* (`Ouvert` → `En cours` → `Traité` / `Accepté` /
> `Refusé`) au fil des décisions ; le passage du dernier P0 à résolu **lève le VETO Release** (R10).

