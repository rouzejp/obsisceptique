# ObsiSceptique

**Coffre Obsidian pour la zététique, le scepticisme scientifique et le fact-checking.**

ObsiSceptique est un **système complet de vérification des faits** — pas seulement un vault Obsidian, mais un pipeline automatisé piloté par [Hermes Agent](https://hermes-agent.nousresearch.com), de la collecte des claims à la publication.

## Fonctionnalités

- 📁 **Structure de vault** pensée pour le fact-checking (claim → vérification → publication)
- 📋 **Templates normalisés** (fiche de vérification, source, corrélation)
- 🧠 **Glossaire des biais cognitifs** (65+ biais classés)
- 🤖 **Orchestration Hermes Agent** — Kanban, watchers, skills
- 🔍 **RAG** — Indexation vectorielle du vault (ChromaDB + all-MiniLM-L6-v2)
- 📝 **Publication WordPress** automatisée via API REST
- 📧 **Livrables multiples** (PDF, email, texte réseaux sociaux)
- 🐙 **Open source** — forkable, adaptable, reproductible

## Structure

```
ObsiSceptique/
├── 00_Index/              glossaires et index
├── 01_Inbox/              captures brutes
├── 02_Revendications/     claims à vérifier
├── 03_Verifications/      fact-checks conclus
├── 04_Sources/            études, articles, acteurs
├── 05_Correlations/       rapprochements thématiques
├── 06_Publications/       brouillons d'articles
├── 07_Templates/          fiches normalisées
├── 08_Veille/             flux RSS et watchers
├── _system/               documentation complète du système
├── AGENTS.md              architecture des agents Hermes
└── _index.md              index principal
```

## Documentation système

| Document | Contenu |
|----------|---------|
| [_system/01-architecture.md](_system/01-architecture.md) | Architecture complète du système |
| [_system/02-workflow.md](_system/02-workflow.md) | Pipeline de vérification détaillé |
| [_system/03-hermes-setup.md](_system/03-hermes-setup.md) | Configuration Hermes Agent |
| [_system/04-skills.md](_system/04-skills.md) | Skills et automatisation |
| [_system/05-wordpress.md](_system/05-wordpress.md) | Publication WordPress |
| [_system/06-forking.md](_system/06-forking.md) | Guide de fork et d'adaptation |

## Exemple concret

Une vérification complète est disponible dans le dépôt :

- **Claim :** `02_Revendications/melenchon-merah-juin2021.md`
- **Vérification :** `03_Verifications/03c_nuance/melenchon-merah-juin2021.md`

Résultat : [Article publié sur le blog](https://rouze.eu/fact-check-jean-luc-melenchon-et-mohammed-merah/)

## Licence

**CC-BY-NC 4.0** — utilisation libre et modification autorisée pour usage non commercial avec attribution.

© 2026 Jean-Paul Rouzé — [rouze.eu](https://rouze.eu)
