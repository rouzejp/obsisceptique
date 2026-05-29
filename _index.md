# ObsiSceptique

Coffre Obsidian dédié à la zététique, au scepticisme scientifique et au fact-checking.

Structure inspirée du système de connaissances Jenkins.

## Architecture

```
00_Index/          ← glossaire, index thématique, biais cognitifs
   glossaire.md    ← termes généraux
   glossaire-biais-cognitifs.md ← glossaire complet des biais
01_Inbox/          ← captures brutes, liens, notes rapides
02_Revendications/ ← les claims à vérifier (une note par sujet)
03_Verifications/  ← fact-checks conclus
  03a_confirme/    ← vérifié vrai
  03b_infirme/     ← vérifié faux / trompeur
  03c_nuance/      ← plus complexe qu'il n'y paraît
04_Sources/        ← études, articles, personnes
  04a_Etudes/      ← publications scientifiques
  04b_Articles/    ← presse, blogs, médias
  04c_Acteurs/     ← personnalités, organisations
05_Correlations/   ← rapprochements thématiques
  pseudo-medecine/
  astrologie/
  paranormal/
  derives-ia/
06_Publications/   ← brouillons d'articles
  brouillons/      ← en cours d'écriture
  publie/          ← publié sur le blog
07_Templates/      ← modèles de fiches
08_Veille/         ← watchers RSS, résumés Mastodon
  watchers/        ← config des flux
```

## Orchestration (Kanban)

Les boards Kanban Hermes servent de couche d'orchestration :

| Board | Usage |
|---|---|
| [[hermes.rouze.eu](https://hermes.rouze.eu) → Kanban | Vue d'ensemble des tâches |
| `default` | Veille, articles, OSINT — de la découverte à la publication |
| `homelab` | Tâches infrastructure |
| `lecture` | Livres, articles à noter |

**Flux de travail typique :**
```
Veille / Inbox → Revendication → Analyse / OSINT → Corrélation → Rédaction → Publication
   (01_Inbox)    (02_Revendic.)  (03_Vérif.+Sources) (05_Corrél.)   (06_Publications)
```
Chaque étape est représentée par une carte Kanban qui avance dans les colonnes (triage → ready → running → done).

## Licence

Ce coffre est publié en licence libre — voir LICENSE.
