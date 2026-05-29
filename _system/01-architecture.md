# Système ObsiSceptique — Architecture

ObsiSceptique n'est pas qu'un coffre Obsidian : c'est un **système complet de vérification des faits** piloté par [Hermes Agent](https://hermes-agent.nousresearch.com).

## Principe général

```
┌─────────────────────────────────────────────────────────┐
│                    SOURCES (RSS, API, Web)              │
│                         ↓ watchers                      │
│  ┌───────────────────────────────────────────────────┐  │
│  │              01_Inbox/ (captures brutes)          │  │
│  └─────────────────────┬─────────────────────────────┘  │
│                        ↓ triage                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │          02_Revendications/ (claims à vérifier)    │  │
│  └─────────────────────┬─────────────────────────────┘  │
│                        ↓ kanban worker                  │
│  ┌───────────────────────────────────────────────────┐  │
│  │          03_Verifications/ (fact-checks)           │  │
│  │   03a_confirme / 03b_infirme / 03c_nuance         │  │
│  └─────────────────────┬─────────────────────────────┘  │
│                        ↓ publication                    │
│  ┌───────────────────────────────────────────────────┐  │
│  │        06_Publications/ (articles, posts)         │  │
│  └─────────────────────┬─────────────────────────────┘  │
│                        ↓ diffusion                     │
│  ┌──────────┐  ┌────────────┐  ┌──────────────────┐    │
│  │ WordPress│  │   Email    │  │ Social (FB, Mast)│    │
│  └──────────┘  └────────────┘  └──────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

## Composants

| Composant | Rôle | Technologie |
|-----------|------|-------------|
| **Vault Obsidian** | Base de connaissance structurée | Markdown + Obsidian |
| **Hermes Agent** | Orchestrateur de tâches | Python (CLI) |
| **Kanban** | Suivi des vérifications | Hermes Kanban (SQLite) |
| **Watchers** | Surveillance de flux | Hermes watchers (cron) |
| **ChromaDB** | Indexation RAG du vault | all-MiniLM-L6-v2 → ONNX |
| **WordPress** | Publication des articles | REST API + Basic Auth |
| **SMTP** | Envoi des rapports par email | mail.rouze.eu:587 |

## Arborescence du vault

```
ObsiSceptique/
├── 00_Index/              glossaires et index
│   ├── glossaire.md       termes généraux
│   └── glossaire-biais-cognitifs.md   65+ biais
├── 01_Inbox/              captures brutes
├── 02_Revendications/     claims à vérifier
├── 03_Verifications/      fact-checks conclus
│   ├── 03a_confirme/      vérifié vrai
│   ├── 03b_infirme/       vérifié faux
│   └── 03c_nuance/        plus complexe
├── 04_Sources/            études, articles, acteurs
├── 05_Correlations/       rapprochements thématiques
├── 06_Publications/       brouillons d'articles
├── 07_Templates/          fiches normalisées
├── 08_Veille/             flux RSS et watchers
└── _system/               documentation système
    ├── README.md
    ├── 01-architecture.md
    ├── 02-workflow.md
    ├── 03-hermes-setup.md
    ├── 04-skills.md
    ├── 05-wordpress.md
    └── 06-forking.md
```

## Principes fondateurs

1. **Sourcer systématiquement** — toute affirmation doit avoir une source vérifiable
2. **Séparer claim et vérification** — le doute initial n'est pas un verdict
3. **Accepter la nuance** — les faits sont rarement binaires (03c_nuance existe pour ça)
4. **Automatiser sans dépendre** — l'IA accélère la recherche, l'humain valide
5. **Open source par nature** — la méthode doit être reproductible et critiquable
