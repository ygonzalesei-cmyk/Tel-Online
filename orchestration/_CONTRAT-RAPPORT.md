# _CONTRAT-RAPPORT.md — Format imposé des rapports d'agent (R6)

Chaque agent écrit son rapport dans `orchestration/rapports/<agent>.md` à la **fin de CHAQUE mission**.
**Pas de rapport = mission non terminée.** Le rapport le plus récent est en **haut** du fichier (journal anti-chronologique).

## Gabarit d'une entrée de rapport

```
## [AAAA-MM-JJ] T-### — <titre court de la mission>
- **Feu** : 🟢 READY_FOR_PUSH  |  🔴 BLOCKED_<raison>  |  🔴 PARTIAL_BLOCKED
- **Périmètre touché** : <fichiers/dossiers — un seul writer respecté ?>
- **Fait** : <ce qui a été réellement livré>
- **Preuve** : <commit(s) SHA/branche, sortie build/test, capture, lien PR> (R1 : pas de livraison sans preuve)
- **Tests (R8)** : automatisables / semi-auto / humains obligatoires / preuve manquante
- **Sécurité** : impact zero-knowledge / garde-fous ? SEC-### ouverts ?
- **Décisions nécessaires (R7)** : <questions au patron — n'ont PAS arrêté la mission>
- **Reste à faire / prompt de reprise** : <si 🔴, le prompt exact pour reprendre>
- **Routage** : <ticket ou agent à appeler pour la suite>
```

## Règles
- **🟢 READY_FOR_PUSH** = build/test OK, commit fait, PR draft ouverte vers `atelier`. **≠ « en prod »** (R9).
- **🔴** = bloqué : donner la **cause** + le **prompt de reprise** exact.
- Un feu vert **sans preuve** est invalide (R1). L'orchestrateur ne consolide que des feux prouvés.
- Clôture d'un ticket = **commit précis + verdict QA** (règle anti-récidive, cf. `tickets/INDEX.md`).
