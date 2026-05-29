# Workflow de vérification

Le cœur du système ObsiSceptique est un pipeline de vérification reproductible, de la réception d'un claim à sa publication.

## Workflow complet (1 claim)

```
1. RÉCEPTION
   Claim détecté (watcher, suggestion, demande manuelle)
       ↓
2. TRIAGE
   Dépôt dans 02_Revendications/ avec fiche minimale
       ↓
3. RECHERCHE
   Collecte multi-sources (web, archives, RAG)
   Transcription des citations exactes
       ↓
4. ANALYSE
   Tableau confirmations / réfutations
   Vérification chronologique
   Identification des sophismes
       ↓
5. VERDICT
   03a_confirme / 03b_infirme / 03c_nuance
   Note sur plusieurs critères
       ↓
6. PRODUCTION
   Texte réseau social (Facebook, Mastodon...)
   PDF complet avec sources
   Email détaillé
       ↓
7. PUBLICATION
   Article WordPress (catégorie dédiée)
   Sauvegarde dans le vault
       ↓
8. DIFFUSION
   Partage sur les réseaux
   Notification aux abonnés (Mastodon via ActivityPub)
```

## Template de fiche de vérification

Chaque vérification suit ce modèle (voir `07_Templates/template-fact-check.md`) :

```markdown
# Titre du claim

> **Verdict : 🟡 À nuancer / 🔴 Faux / 🟢 Confirmé**

## Claim initial
[L'affirmation à vérifier]

## Contexte
[Date, lieu, intervenant, circonstances]

## Transcription / citation exacte
[Source primaire obligatoire]

## Analyse

### Éléments confirmés
| Élément | Statut | Preuve |
|---------|--------|--------|

### Éléments infirmés
| Élément | Statut | Preuve |
|---------|--------|--------|

## Réactions
[Des parties prenantes, si pertinent]

## Verdict
[Note par critère avec explication]

## Sources
[URLs vérifiées numérotées]
```

## Workflow automatisé (Hermes Agent)

Quand un claim arrive, Hermes peut exécuter le pipeline complet via le skill `obsisceptique-factcheck-workflow` :

1. Charger le skill
2. Lancer la recherche multi-sources
3. Structurer l'analyse
4. Générer les livrables (PDF, email, article WP)
5. Archiver dans le vault

Ce workflow est entièrement reproductible et documenté dans `04-skills.md`.
