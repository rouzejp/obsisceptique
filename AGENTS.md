# AGENTS.md — Architecture d'orchestration ObsiSceptique

> Document complémentaire à `_index.md`
> Décrit comment les tâches sont traitées via Hermes, les boards Kanban et les workers spécialisés.

---

## Principe

L'orchestration ne repose pas sur des agents autonomes tournant en permanence, mais sur un **flux de travail** où chaque étape est matérialisée par une **carte Kanban** qui progresse dans les colonnes :

```
triage → todo → ready → running → done
```

À chaque étape, Hermes (toi ou moi) traite la carte, puis la fait passer à l'étape suivante.

---

## Pipeline de traitement d'un claim

```
🌐 Veille / Capture
      │  (flux RSS, Mastodon, article, échange)
      ▼
┌─────────────┐  Carte Kanban → 01_Inbox/
│   TRIAGE    │  Triage : est-ce vérifiable ? pertinent ?
└──────┬──────┘
       │  ↓ promote → todo
       ▼
┌─────────────────┐  Carte → 02_Revendications/
│  REVENDICATION  │  Claim formalisé + sources brutes
└────────┬────────┘
         │  ↓ ready → running
         ▼
┌──────────────┐   ┌──────────────┐
│  ANALYSE     │   │   OSINT      │  ← en parallèle
│  fact-check  │◄──┤   sources    │
│  verdict     │   │   acteurs    │
└──────┬───────┘   └──────┬───────┘
       │                  │
       └──────┬───────────┘
              ▼
┌──────────────┐
│ CORRELATION  │  Patterns, liens avec d'autres vérifications
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  REDACTION   │  Article sourcé → 06_Publications/
└──────┬───────┘
       │
       ▼
  Publication WordPress
```

---

## Cartes Kanban par agent

Chaque « agent » correspond à un type de carte dans un board Kanban, pas à un processus autonome :

| Agent | Board | Colonne d'entrée | Dossier vault |
|---|---|---|---|
| **Veille** | `default` | triage | `01_Inbox/`, `08_Veille/` |
| **Analyse** | `default` | ready | `03_Verifications/` |
| **OSINT** | `default` | ready | `04_Sources/` |
| **Corrélation** | `default` | ready | `05_Correlations/` |
| **Rédaction** | `default` | ready | `06_Publications/` |
| **Dialogue** | — | interaction directe | `02_Revendications/` |
| **Mémoire** | — | mise à jour passive | `00_Index/` |

### 1. Agent Veille

**Rôle :** Surveillance passive des flux d'information.

| Entrée | Sortie | Déclenché par |
|---|---|---|
| Flux RSS, API Mastodon, articles web | Fiche brute dans `01_Inbox/` | Crons Hermes (watchers) / demande explicite |

**Ce qu'il fait :**
- Collecte les claims, articles, liens intéressants
- Crée une carte Kanban en **triage** sur le board `default`
- Ne vérifie PAS, ne commente PAS, collecte seulement

**Prompt type :**
> Tu surveilles les flux scepticisme/zététique. Identifie les affirmations nouvelles et vérifiables. Crée une fiche dans `01_Inbox/` avec : claim exact, source URL, date, urgence (faible/moyen/élevé). Puis crée une carte Kanban en triage.

### 2. Agent Analyse (fact-check)

**Rôle :** Vérification méthodique d'un claim.

| Entrée | Sortie |
|---|---|
| Claim depuis `02_Revendications/` | Fiche dans `03_Verifications/` avec verdict |

**Grille d'analyse :**
1. Type d'affirmation (empirique / normative / interprétative)
2. Sources primaires (peer-reviewed, consensus scientifique)
3. Biais détectés (confirmation, autorité, anecdote…)
4. Verdict + niveau de certitude
5. Explication pédagogique

**Verdicts possibles :** `03a_confirme/` ✓ | `03b_infirme/` ✗ | `03c_nuance/` ~

### 3. Agent OSINT (sources)

**Rôle :** Traçage des acteurs et évaluation des sources.

| Entrée | Sortie |
|---|---|
| Acteur ou source signalé | Fiche dans `04_Sources/` |

**Grille source :**
- Type : individu · organisation · institution
- Production : peer-review · vulgarisation · militantisme
- Financement : indépendant · institutionnel · privé
- Biais : conflits d'intérêts identifiés

### 4. Agent Corrélation

**Rôle :** Détection de patterns entre vérifications.

| Entrée | Sortie |
|---|---|
| `03_Verifications/` + `04_Sources/` | Fiches dans `05_Correlations/` |

**Types de corrélations :**
- **causale** → A entraîne B (preuve explicite)
- **fortuite** → co-occurrence sans lien établi
- **thématique** → même domaine / même narratif
- **réseau** → acteurs partagés

**Certitude :** [!] faible · [!!] moyen · [!!!] élevé

### 5. Agent Rédaction

**Rôle :** Production d'articles sourcés.

| Entrée | Sortie |
|---|---|
| Fiches vérifiées de `03_Verifications/` | Article dans `06_Publications/` |

**Structure article :**
1. Introduction accessible
2. Points d'accord avec le lecteur
3. Développement argumenté sourcé
4. Nuances et limites
5. Conclusion ouverte

**Exports :** Markdown (vault) → WordPress (API REST)

### 6. Agent Dialogue

**Rôle :** Interaction humaine directe (toi ↔ Hermes).

C'est toi qui parles à Hermes. Quand un échange identifie un claim vérifiable, Hermes crée une fiche dans `02_Revendications/` et une carte Kanban.

### 7. Agent Mémoire / Index

**Rôle :** Maintien de la cohérence du vault.

Déclenché après chaque nouvelle fiche : mettre à jour `00_Index/`, ajouter les wikilinks, taguer, détecter les doublons.

**Règle absolue :** ne JAMAIS modifier le corps d'une fiche existante — métadonnées et liens uniquement.

---

## Correspondance avec les boards Kanban

```
Board "default"
┌────────┬────────┬────────┬────────┬────────┬────────┐
│triage  │ todo   │ ready  │running │blocked │ done   │
├────────┼────────┼────────┼────────┼────────┼────────┤
│Nouveau │À       │Prêt à │En cours│Blocage │Terminé│
│claim   │vérifier│analyser│        │        │        │
└────────┴────────┴────────┴────────┴────────┴────────┘
```

**Dépendances entre cartes** (parents/enfants) :
```
Veille (triage)  →  Analyse (ready)  →  Corrélation (ready)
                        ↓                    ↓
                    OSINT (ready)       Rédaction (ready)
```

Une carte enfant ne passe en `ready` que quand sa parente est `done`.

---

## Exemple de workflow complet

1. **Veille :** Un watcher RSS détecte un article sur l'homéopathie
   → Carte en triage : « Homeopathie : nouvelle étude X »
   → Fiche dans `01_Inbox/`

2. **Tri :** Tu qualifies le sujet
   → Passe en todo
   → Déplace la fiche dans `02_Revendications/`

3. **Analyse + OSINT** (parallèle) :
   → Carte Analyse passe en running → verdict dans `03_Verifications/`
   → Carte OSINT passe en running → fiche acteurs dans `04_Sources/`

4. **Corrélation :** Quand Analyse et OSINT sont done
   → Carte Corrélation passe en ready → liens dans `05_Correlations/`

5. **Rédaction :** Quand Corrélation est done
   → Carte Rédaction passe en ready → article dans `06_Publications/`

6. **Publication :** Article publié sur WordPress
   → Carte passe en done

---

## Licence

MIT — voir LICENSE.

---

*Document adapté des principes AGENTS.md proposés par un lecteur de l'article sur le scepticisme assisté (rouze.eu).*
