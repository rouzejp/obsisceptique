---
title: AGENTS — Architecture du système ObsiSceptique
description: Organisation, responsabilités et flux des workers Hermes pour le vault de scepticisme
tags: [architecture, agents, kanban, orchestration]
---

# AGENTS.md — Architecture ObsiSceptique

> Document d'architecture des workers et flux de traitement du vault ObsiSceptique.
> Complément à `_index.md` pour la partie orchestration.

---

## Principe

ObsiSceptique fonctionne comme un **pipeline de traitement** où chaque étape est une carte Kanban qui progresse dans les colonnes `triage → ready → running → done`.

L'orchestrateur (Hermes) distribute les tâches entre :
- Les **crons et watchers** (collecte automatique)
- Les **skills** (procédures spécialisées)
- Les **kanban workers** (tâches longues tracées dans le board)
- **L'humain** (validation, décision, rédaction finale)

---

## Pipeline de traitement

```
                    ┌─────────────────────┐
                    │   VEILLE (crons)    │
                    │ watchers RSS,       │
                    │ Mastodon, GitHub    │
                    └─────────┬───────────┘
                              │ détection claim
                              ▼
┌─────────────────────────────────────────────────────┐
│  INBOX (01_Inbox/)                                  │
│  Fiche brute : claim + source + date                │
│  Déclenché : cron / watcher                         │
│  Action : écriture fichier .md, pas d'analyse       │
└─────────────────────────────────────────────────────┘
                              │ Hermes valide + déplace
                              ▼
┌─────────────────────────────────────────────────────┐
│  REVENDICATION (02_Revendications/)                 │
│  Claim validé, structuré, prêt à vérifier           │
│  Kanban : triage → ready                            │
└─────────────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼
┌─────────────────────────┐   ┌─────────────────────────┐
│  ANALYSE                 │   │  OSINT                  │
│  Fact-check méthodique   │   │  Traçage des sources    │
│  → 03_Verifications/    │   │  → 04_Sources/          │
│  Kanban: ready → running │   │  Kanban: ready → running│
└────────────┬────────────┘   └────────────┬────────────┘
              │                               │
              └───────────────┬───────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────┐
│  CORRÉLATION (05_Correlations/)                     │
│  Patterns, réseaux, liens entre vérifications       │
│  Kanban : ready → running → done                    │
└─────────────────────────────────────────────────────┘
                              │ Hermes valide
                              ▼
┌─────────────────────────────────────────────────────┐
│  RÉDACTION (06_Publications/)                       │
│  Article sourcé → brouillon → publié (WordPress)     │
│  Kanban : ready → running → done                    │
└─────────────────────────────────────────────────────┘
```

---

## Correspondance workers ↔ outils Hermes

| Étape | Outil Hermes | Déclencheur | Sortie |
|---|---|---|---|
| **Veille** | Watchers (crons RSS/API/GitHub) | `*/6 * * * *` | Fiche dans `01_Inbox/` |
| **Inbox** | Skill `veille` + script | Nouveau fichier détecté | Claim extrait |
| **Revendication** | Kanban (board `default`) | Hermes valide le claim | Carte Kanban + fichier |
| **Analyse** | Kanban worker + DeepSeek | Carte en `ready` → `running` | Verdict dans `03_Verifications/` |
| **OSINT** | Kanban worker + recherche structurée | Carte en `ready` → `running` | Fiche source dans `04_Sources/` |
| **Corrélation** | Kanban worker + Dataview | Batch de vérifications terminé | Liens dans `05_Correlations/` |
| **Rédaction** | Hermes + API WordPress | Carte en `ready` | Article publié sur rouze.eu |
| **Mémoire/Index** | Hermes (toi) | Après chaque écriture | Mise à jour `00_Index/` |

---

## Kanban boards

| Board | Usage | Colonnes actives |
|---|---|---|
| `default` | Veille, articles, OSINT — flux principal | triage → ready → running → done |
| `homelab` | Tâches infrastructure | triage → ready → running → done |
| `lecture` | Livres, articles à noter | triage → todo → done |

---

## Principes SOLID appliqués

| Principe | Application |
|---|---|
| **S** — Single Responsibility | Chaque worker fait une seule chose. La veille ne vérifie pas. L'analyse ne publie pas. |
| **O** — Open/Closed | Ajouter un watcher ne modifie pas les workers existants. |
| **L** — Liskov Substitution | Tout worker produit une note Obsidian `.md` ou un signal Hermes. |
| **I** — Interface Segregation | Chaque worker reçoit uniquement ce dont il a besoin (pas les conversations brutes à l'indexeur). |
| **D** — Dependency Inversion | Les chemins du vault sont dans `_index.md`, pas codés en dur dans les workers. |

---

## X Commandements × Workers

| Commandement | Workers concernés |
|---|---|
| I — égalité | Dialogue (humain), Rédaction |
| II — écoute | Veille, Dialogue |
| III — comprendre | Analyse, Dialogue |
| V — sans jugement | OSINT, Rédaction |
| VI — expliquer | Analyse, Rédaction |
| VIII — ses erreurs | **TOUS** (obligation transversale) |
| IX — pas d'obstination | Corrélation, Dialogue |
| X — points d'accord | Rédaction |

---

## Flux Kanban typique

1. **Watcher** détecte un claim → dépose dans `01_Inbox/`
2. Hermes crée une carte en **triage** sur le board `default`
3. Tu valides → la carte passe en **ready**
4. Le worker (Hermes) prend la carte → **running** → exécute l'analyse
5. Résultat déposé dans `03_Verifications/` → carte en **done`
6. Étape suivante : Corrélation ou Rédaction, avec dépendance sur la carte précédente

Voir `_index.md` pour la structure complète du vault.
