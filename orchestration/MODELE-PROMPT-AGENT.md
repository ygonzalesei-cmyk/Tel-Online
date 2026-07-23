# MODELE-PROMPT-AGENT.md — Gabarit de prompt de dispatch (réutilisé à chaque vague)

> Utilisé par **l'orchestrateur** pour chaque dispatch. **Un bloc = un prompt** copiable-collable et **lançable tout de suite**.
> Nom de l'agent au-dessus du bloc **et** répété en 1re ligne DANS le bloc. Un prompt ne s'adresse **jamais** à un nom
> de fonctionnalité/écran — uniquement à un **skill existant**. `atelier` est une branche, jamais un destinataire.

## Gabarit (copie conforme)

```
▼ DÉBUT MODÈLE
→ Session <NOM> — ouvre d'abord le skill /<nom-exact> (charge-le AVANT de commencer), et travaille/réponds en français.
Mission <T-###> : <périmètre précis + ce qui est attendu + fichiers autorisés>.
Contraintes : reste dans TON périmètre (un seul writer) ; suis la charte design (R12) si UI ; sécurité/garde-fous
TEL ONLINE (zero-knowledge, pas d'OTP bancaire stocké, pas de clair).
À la fin : écris ton rapport 🟢/🔴 dans orchestration/rapports/<module>.md ; commit + PR draft vers atelier, ne
pousse JAMAIS sur main ; ne reste pas bloqué sur une question (R7) : va au bout, note-la dans le rapport.
▲ FIN MODÈLE
```

## Rappels de discipline (orchestrateur)
- **Lançable tout de suite** : n'affiche qu'un prompt sans prérequis. Prompt à prérequis → **déclencheur** dans `ETAT.md`, produit **quand** le déclencheur est réalisé.
- **Parallélisme sûr** : rails à **fichiers disjoints** ; **un seul writer** par fichier partagé.
- Ne jamais redonner un prompt déjà lancé (sauf rapport prouvant la fin, ou confirmation patron). Ne jamais garder un prompt « pour plus tard » → dispatch, ou ticket/backlog.
- Si **< 3 prompts** dans une vague, ajouter **« Pourquoi pas plus ? »**.
