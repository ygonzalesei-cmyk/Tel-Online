# TEL ONLINE — Cadrage conformité (RGPD · DSP2/SCA · KYC-AML · hébergement UE)

| | |
|---|---|
| **Réf.** | T-003 (cadrage conformité) — amont : T-001 🟢 · aval : prérequis T-004 |
| **Agent** | `securite` (writer unique de `orchestration/agents/securite/`) |
| **Version** | 1.0 |
| **Date** | 2026-07-23 |
| **Base** | `origin/atelier` (vague 1 intégrée) |
| **Statut** | Cadrage — **audit non destructif**, aucun code, aucun secret en clair |
| **Entrées** | [`MODELE-MENACE.md`](./MODELE-MENACE.md) (§12 + Annexe réglementaire) · [`REGISTRE.md`](./REGISTRE.md) (SEC-###, Annexe C) · [`ARCHI-ZERO-KNOWLEDGE.md`](./ARCHI-ZERO-KNOWLEDGE.md) · `../memoire-produit/VISION.md` |

> **Périmètre & non-modification.** Ce document **crée** le cadrage conformité. Il **ne modifie pas**
> `MODELE-MENACE.md` / `REGISTRE.md` : les risques sont **référencés par numéro** (SEC-###). Les constats
> réglementaires de fond ont été ouverts en T-001 (REGISTRE Annexe C) ; ici on les **structure en exigences
> de conformité opposables** et en **points de contrôle**. Ce cadrage est **soumis à validation d'un référent
> conformité** (cf. §9, question patron).

---

## 0. Posture & résumé

TEL ONLINE cumule **trois régimes réglementaires** rarement réunis dans une même app : **télécom**
(opérateur/eSIM, rétention & interception légales), **services de paiement / monnaie électronique**
(DSP2/SCA, LCB-FT), et **traitement de données personnelles à grande échelle** dont un **coffre
zero-knowledge** et de la **biométrie**. La ligne directrice :

1. **Le zero-knowledge est un atout de conformité** (minimisation, sécurité art. 32, atténuation art. 34)
   **mais ne dispense de rien** sur les zones **régulées non-ZK** (KYC/AML, paiement, métadonnées, télécom).
2. **Cloisonnement strict** domaine ZK ↔ domaine régulé (frontière F6 du modèle) : l'identité en clair
   exigée par le KYC/paiement ne doit **jamais** servir à dé-anonymiser ou déchiffrer le coffre.
3. **Hébergement UE**, **canaux officiels uniquement**, **moindre privilège**, **audit inviolable**.
4. Ce qui relève d'une **décision juridique** (agrément EMI, référent conformité, seuils) est **routé
   patron** (R7) — l'analyse va au bout du faisable sans rester bloquée.

---

## 1. Cartographie réglementaire applicable

| Domaine | Cadres | Autorité (France/UE) |
|---|---|---|
| Données personnelles | **RGPD** (UE 2016/679), **ePrivacy**, Loi Informatique & Libertés | **CNIL** |
| Paiement | **DSP2/PSD2** (UE 2015/2366) + **RTS SCA** (UE 2018/389), **EMD2** (monnaie électronique) | **ACPR** / EBA |
| Cartes | **PCI-DSS v4.0**, **PCI 3DS** | Schémes (Visa/MC) |
| LCB-FT | **AMLD5/6** (2018/843, 2018/1673), futur **AMLR/AMLA**, CMF L.561-* | **ACPR** / **Tracfin** |
| Identité numérique | **eIDAS** (+ eIDAS 2.0/EUDI Wallet), **PVID** (vérification d'identité à distance) | **ANSSI** |
| Télécom | Code des postes & com. électroniques ; **rétention** ciblée & **interception légale (LI)** (post-CJEU *La Quadrature/Tele2*) | **ARCEP** / autorités |
| eSIM | **GSMA RSP** SGP.22 + **SAS-SM** (accréditation) | GSMA |

> **Chevauchement structurant :** l'éditeur **est l'opérateur** → il porte **simultanément** des obligations
> de rétention/LI télécom **et** une promesse de confidentialité ZK. La frontière entre les deux doit être
> **architecturalement prouvée** (voir §7, renvoi T-009 Infra).

---

## 2. RGPD — protection des données

### 2.1 Rôles & responsabilités
- **Responsable de traitement** : l'éditeur TEL ONLINE (finalités & moyens).
- **Sous-traitants (art. 28)** : PSP, prestataire KYC, hébergeur UE, SM-DP+ (eSIM), push (APNs/FCM),
  analytics — **DPA obligatoire** avec chacun, registre des sous-processeurs, transparence.
- **Coffre ZK** : les données du coffre sont **chiffrées E2E** ; le responsable **ne peut pas** y accéder en
  clair → traitement **limité au stockage de chiffré opaque** (minimisation native).

### 2.2 Base légale par finalité (art. 6) & catégories particulières (art. 9)
| Finalité | Base légale (art. 6) | Notes |
|---|---|---|
| Fourniture du coffre / sync chiffrée | **Contrat** (6.1.b) | Données en clair inaccessibles au responsable (ZK) |
| Sauvegarde contacts/photos | **Contrat** + **consentement** si accès contacts/galerie | Permissions **en contexte** (design) |
| Paiement / portefeuille | **Contrat** + **obligation légale** (DSP2, LCB-FT) | — |
| **KYC / liveness biométrique** | **Obligation légale** (LCB-FT) + **art. 9.2.g** (intérêt public) ou **consentement explicite (9.2.a)** | **Art. 9** : gabarit biométrique = **catégorie particulière** ; base légale explicite obligatoire |
| eSIM / connectivité | **Contrat** + **obligation légale** (rétention télécom) | Isolé du coffre |
| Télémétrie / analytics | **Consentement** (ePrivacy) | **Opt-in**, aucun clair du coffre (VETO-V6) |

### 2.3 Minimisation & conservation (art. 5)
- **Minimisation** : ne collecter que le strict nécessaire ; préférer des **attributs vérifiés** à la copie
  brute de pièces d'identité quand possible (couvre SEC-033).
- **Matrice de rétention** (à valider juridiquement) :

| Catégorie | Durée indicative | Fondement |
|---|---|---|
| Coffre chiffré (E2E) | Durée du compte + purge | Contrat ; **crypto-shredding** à l'effacement |
| Journaux de sécurité (WORM) | 6–12 mois | Sécurité art. 32 / preuve |
| **KYC / AML** (pièces, décisions) | **5 ans** après fin de relation | **LCB-FT** (obligation) |
| Données de trafic télécom | Rétention **ciblée** légale | CPCE / jurisprudence UE |
| Métadonnées techniques | Minimales, courtes | Minimisation |

> **Tension WORM ↔ effacement (art. 17).** Concilier journaux immuables et droit à l'effacement via
> **pseudonymisation** dans les logs + **crypto-shredding** (destruction de clé) ; distinguer les données à
> **rétention légale** (KYC/AML, télécom) — **non effaçables** avant échéance — des données **effaçables**.

### 2.4 Droits des personnes (art. 12–22)
| Droit | Mise en œuvre TEL ONLINE |
|---|---|
| Accès / portabilité | Export **côté client** (le responsable n'a que du chiffré) ; export des données régulées via parcours dédié |
| Rectification | Côté client (coffre) ; côté responsable (données KYC/paiement) |
| **Effacement** (art. 17) | **Crypto-shredding** du coffre ; suppression des stores régulés **sauf rétention légale** |
| Opposition / limitation | Analytics opt-out ; traitements non essentiels |
| **Décision automatisée (art. 22)** | **Gel/rejet AML** = décision à effet significatif ⇒ **intervention humaine (human-in-the-loop)**, **contestation**, **explication**, traçabilité du motif (couvre l'angle mort AML du registre) |

### 2.5 Obligations transverses
- **Registre des traitements (art. 30)** : esquisse en **§8**.
- **DPIA (art. 35)** : **obligatoire** (grande échelle + biométrie liveness + profilage AML). **Bloquant**
  pour la mise en production du KYC biométrique.
- **Privacy/Security by design & default (art. 25/32)** : matérialisé par l'architecture ZK
  ([`ARCHI-ZERO-KNOWLEDGE.md`](./ARCHI-ZERO-KNOWLEDGE.md)) — AEAD, KDF plancher, aucune clé serveur.
- **Violation de données (art. 33/34)** : notification autorité **72 h** / personnes ; le **chiffrement
  fort est atténuant**. **Matrice de notifiabilité** : le **ciphertext ZK volé n'est pas notifiable** aux
  personnes ; les **stores non-ZK** (KYC/AML, paiement, métadonnées, identifiants eSIM) **le restent**.
- **Transferts hors-UE (chap. V, Schrems II)** : encadrer APNs/FCM (US), PSP, KYC, cloud, SM-DP+ par
  **SCC + mesures supplémentaires** ; **minimiser les payloads push** (aucun contenu sensible, cf. SEC-042).
- **ePrivacy** : **consentement préalable** pour analytics/attribution/session-replay et accès au terminal.

---

## 3. DSP2 / PSD2 & SCA (paiement)

- **Périmètre** : s'applique dès qu'il y a **service de paiement** (portefeuille, cartes, virements,
  recharges). À confirmer selon le montage (établissement agréé **ou** partenariat avec un PSP/EMI agréé).
- **SCA (art. 97/98)** : requise pour l'**accès en ligne au compte de paiement** **et** l'**initiation** ;
  **deux facteurs indépendants**, décision **côté serveur/ACS** (jamais pilotée client).
- **Dynamic linking (RTS art. 5)** : le code d'authentification **lié au montant + bénéficiaire** ; toute
  altération après consentement **invalide** l'autorisation (couvre SEC-009 / VETO-V19).
- **Garde-fou VETO-V1** : ❌ **aucun OTP bancaire 3D Secure stocké/rejoué** ⇒ **passkeys/FIDO2 + SCA app**,
  pas de repli SMS (couvre SEC-008/009, aligné VISION « l'utilisateur reçoit lui-même ses OTP légitimes »).
- **Moteur d'exemptions** (faible montant, TRA, bénéficiaires de confiance, récurrents) : **côté serveur**,
  **auditable** — jamais pilotable client.
- **Reporting** : dispositif de **notification d'incident majeur** + **statistiques de fraude** à l'autorité.
- **Monnaie électronique (EMD2)** : un **portefeuille à solde** = émission de monnaie électronique ⇒
  **agrément EMI** (ou partenariat), **safeguarding** des fonds (comptes cantonnés), seuils de prépayé
  anonyme (~150 €). **Ledger double-entrée réconcilié** (au-delà de la simple idempotence webhook).
  → **Décision patron** (§9) : agrément propre **ou** partenaire EMI/BIN.
- **PCI-DSS + PCI 3DS** : **tokenisation** des PAN, **jamais** de PAN/CVV en clair/log (VETO-V7),
  révélation via **composant PCI-scopé isolé**, cache 3DS/KYC purgé → **périmètre PCI réduit**.

---

## 4. KYC / AML (LCB-FT)

- **Garde-fou VETO-V4** : ⚠️ **tout flux financier ⇒ KYC/AML** préalable proportionné.
- **CDD/EDD** : identification & vérification à l'entrée en relation, vigilance renforcée sur risque élevé.
- **Liveness anti-injection** (deepfake, injection de flux caméra) + vérification documentaire — viser une
  brique **certifiée PVID** (France) ; gabarit biométrique = donnée **art. 9** (cf. §2.2).
- **Screening PEP & sanctions** (UE/OFAC) **continu**, **monitoring transactionnel** (vélocité), détection
  de comptes multiples (blanchiment).
- **Déclaration de soupçon (SAR/STR)** à la **CRF (Tracfin)** ; **conservation 5 ans** (concilier avec
  l'effacement RGPD — la rétention légale prime pour ces données).
- **Décision automatisée** (blocage/gel) : **human-in-the-loop** + contestation (art. 22, cf. §2.4).
- **Points de contrôle par flux financier** (gates) :

| Flux | Contrôle KYC/AML obligatoire |
|---|---|
| Ouverture portefeuille / carte | KYC vérifié + screening sanctions/PEP |
| Chargement / recharge | Seuils + source des fonds si élevé |
| Virement / paiement | SCA + monitoring vélocité + screening bénéficiaire |
| Augmentation de plafond/tier | Ré-vérification, décision **serveur** (anti mass-assignment SEC-032) |

---

## 5. Hébergement UE & souveraineté

- **Hébergement UE exigé** (données personnelles et régulées) ; **localisation** documentée dans
  `../REGISTRE-TECH.md` (noms uniquement, aucun secret en clair — R11).
- **Sous-processeurs & transferts** : cartographiés, DPA, SCC + mesures (chap. V).
- **Rétention & interception légales télécom (LI)** : infrastructure **mandatée** = surface d'attaque et
  vecteur insider. Elle doit être **architecturalement isolée** du coffre ZK (le vault **jamais**
  atteignable via LI/rétention). Frontière à **prouver** → **routage T-009 / `/module-infra`**.
- **eIDAS / identité** : si l'app revendique une « identité numérique », alignement eIDAS (et EUDI Wallet à
  terme) ; ne pas sur-vendre la « signature d'intention » comme **signature qualifiée** sans conformité.

---

## 6. Journalisation & moindre privilège (documentés)

- **Audit inviolable** : journal **append-only / WORM**, chaîné par hachage, **hors du blob E2E**,
  **horodatage serveur** de confiance (jamais l'horloge client) — VETO-V20, couvre SEC-024.
- **Aucun secret ni PII dans les logs/APM/crash/SDK** : redaction/tokenisation systématique (VETO-V7,
  couvre SEC-023) ; **session-replay interdit** sur écrans coffre/paiement/KYC (VETO-V6).
- **Moindre privilège** : accès **JIT/PAM**, **séparation des rôles**, **double contrôle (4-eyes)** sur
  actions admin sensibles ; **aucune capacité de « se connecter en tant que »** utilisateur (couvre SEC-015).
- **Notifications de sécurité** signées, multi-canal, avec accusé, pour événements critiques.

---

## 7. Mapping conformité → risques & garde-fous

| Exigence de conformité | Garde-fou / exigence | SEC-### liés |
|---|---|---|
| RGPD art. 9 (biométrie liveness) + DPIA | Liveness anti-injection, base légale explicite, minimisation | SEC-033 |
| RGPD art. 22 (décision AML automatisée) | Human-in-the-loop, contestation, traçabilité | (angle mort AML, REGISTRE Annexe D) |
| RGPD art. 17 vs WORM | Crypto-shredding + pseudonymisation | SEC-024 |
| RGPD art. 28 / chap. V (transferts) | DPA, SCC, minimisation push | SEC-042 |
| DSP2 SCA + dynamic linking | VETO-V1 / VETO-V19, passkeys | SEC-008, SEC-009 |
| PCI-DSS (PAN) | VETO-V7, tokenisation, anti-IDOR | SEC-010, SEC-023 |
| EMD2 (monnaie électronique) | Safeguarding, ledger double-entrée | SEC-008 |
| LCB-FT (KYC/AML) | VETO-V4, screening, SAR/Tracfin | SEC-032, SEC-033 |
| Hébergement UE / LI isolée du ZK | Frontière prouvée (→ T-009 Infra) | SEC-016 |
| Journalisation inviolable | VETO-V20, WORM | SEC-015, SEC-023, SEC-024 |

> Aucun **nouveau** SEC-### ouvert ici (les risques réglementaires étaient déjà consignés en T-001). Si la
> revue juridique révèle un écart, il sera ouvert en **SEC-###** dans `REGISTRE.md` (mission de mise à jour
> dédiée, writer unique).

---

## 8. Registre des traitements (esquisse — art. 30)

| # | Traitement | Finalité | Base légale | Catégories de données | Destinataires / sous-traitants | Transfert hors-UE | Durée |
|---|---|---|---|---|---|---|---|
| R1 | Coffre chiffré | Stockage/sync de secrets | Contrat | **Chiffré opaque** (responsable n'y accède pas) | Hébergeur UE | Non | Compte + purge |
| R2 | Sauvegarde contacts/photos | Sauvegarde perso | Contrat + consentement | Chiffré E2E | Hébergeur UE | Non | Compte + purge |
| R3 | KYC / liveness | LCB-FT / entrée en relation | Obligation légale + art. 9 | Identité, **biométrie** | Prestataire KYC (PVID) | À encadrer (SCC) | **5 ans** |
| R4 | Paiement / portefeuille | Services de paiement | Contrat + obligation légale | Transactions, IBAN, PAN tokenisé | PSP / EMI | À encadrer | Légale |
| R5 | eSIM / connectivité | Fourniture ligne mobile | Contrat + obligation légale | MSISDN, EID, trafic | SM-DP+ opérateur | À encadrer | Rétention légale ciblée |
| R6 | Notifications push | Alertes de sécurité/service | Intérêt légitime / consentement | Token push, **contenu minimal** | APNs/FCM (US) | **Oui → SCC + minimisation** | Courte |
| R7 | Télémétrie | Qualité de service | Consentement (ePrivacy) | Métriques anonymisées | Analytics | Selon fournisseur | Courte |

> Esquisse à compléter/valider par le référent conformité (finalités exactes, durées juridiques, DPIA R3/R4).

---

## 9. Décisions nécessaires (R7 — routage patron)

1. **Référent conformité / DPO** : désigner un référent **RGPD/DSP2/KYC-AML**, ou la Sécurité cadre-t-elle
   seule au départ (avec validation juridique ultérieure) ? *(question DoD T-003)*
2. **Statut paiement** : **agrément EMI propre** *ou* **partenariat PSP/EMI agréé** (impacte safeguarding,
   ledger, seuils anonymes). Lien décision T-001.
3. **Partenaires** (aussi remontés par Infra) : **hébergeur UE**, **PSP/wallet**, **opérateur eSIM**,
   **prestataire KYC certifié PVID** — connus ou à sourcer ?
4. **Périmètre PCI-DSS** de la révélation PAN/CVV in-app ; ambition **eIDAS/PVID** pour l'identité.
5. **Isolation LI/rétention télécom vs coffre ZK** : valider le principe d'isolation architecturale
   (exécution → **T-009 / `/module-infra`**).

**Renvois (routage) :**
- `/module-infra` : hébergement UE, isolation LI/rétention (T-009), HSM/KMS, journalisation technique.
- `/tickets` : suivi T-003 → T-004 (modules + stack, sur GO patron) ; ouverture éventuelle d'un ticket
  « DPIA » et « registre des traitements complet ».
- `/design` : parcours SCA/KYC accessibles, consentement ePrivacy, anti-capture (T-010).

---

## 10. Références
- [`MODELE-MENACE.md`](./MODELE-MENACE.md) §12 (réglementaire) · [`REGISTRE.md`](./REGISTRE.md) Annexe C ·
  [`ARCHI-ZERO-KNOWLEDGE.md`](./ARCHI-ZERO-KNOWLEDGE.md) §10 · `../memoire-produit/VISION.md` ·
  `../REGISTRE-TECH.md`.

*Fin du cadrage conformité (T-003). Suivi des risques : [`REGISTRE.md`](./REGISTRE.md) ; VETO Release actif
tant qu'un P0 est ouvert (R10). Cadrage soumis à validation d'un référent conformité.*
