# Configuration Hermes Agent

ObsiSceptique est piloté par [Hermes Agent](https://hermes-agent.nousresearch.com), un orchestrateur open source d'agents IA.

## Prérequis

- Python 3.12+
- Hermes Agent installé (voir [docs officielles](https://hermes-agent.nousresearch.com/docs))
- Clé API d'un fournisseur LLM (DeepSeek, Anthropic, OpenAI…)

## Installation

```bash
# Installer Hermes Agent
pip install hermes-agent

# Ou depuis le dépôt
git clone https://github.com/nousresearch/hermes-agent.git
cd hermes-agent
pip install -e .

# Initialiser
hermes setup
```

## Configuration minimale (config.yaml)

```yaml
provider: deepseek
model: deepseek-v4-flash

skills:
  enabled:
    - obsidian
    - fact-checking
    - wordpress
    - obsisceptique-factcheck-workflow

kanban:
  enabled: true
  board: default

approvals:
  mode: smart
```

## Profil conseillé

```bash
hermes profile create obsisceptique \
  --model deepseek-v4-flash \
  --provider deepseek \
  --skills obsidian,fact-checking,wordpress,obsisceptique-factcheck-workflow
```

## Variables d'environnement

Dans `.env` du profil Hermes :

```bash
# WordPress (rouze.eu — adapter à votre site)
WP_USER=votre-utilisateur
WP_APP_PASS="votre mot de passe d'application"
WP_SITE=https://votre-site.com

# Email (SMTP)
EMAIL_ADDRESS=hermes@votre-domaine.fr
EMAIL_IMAP_HOST=mail.votre-domaine.fr
EMAIL_SMTP_HOST=mail.votre-domaine.fr
EMAIL_ALLOWED_USERS=vous@votre-domaine.fr
```

## Mécanismes clés

### Kanban
Le suivi des vérifications se fait via des cartes Kanban :

```bash
hermes kanban create --board default \
  --title "Vérifier claim X" \
  --body "Description du claim à analyser"
```

### Watchers (surveillance de flux)
```yaml
# watchers.yaml
watchers:
  - name: sciences-news
    type: rss
    url: https://example.com/feed.xml
    interval: 6h
    tags: [veille, sciences]
    
  - name: sceptic-blogs
    type: rss
    url: https://autre-site.com/feed
    interval: 12h
    tags: [veille, scepticisme]
```

### RAG (Recherche dans le vault)
Le vault est indexé dans ChromaDB pour permettre la recherche sémantique :

```python
from rag_retrieve import retrieve_with_context
contexte = retrieve_with_context("affirmation à vérifier")
```

L'indexation se fait avec `all-MiniLM-L6-v2` (384 dimensions, ONNX Runtime — sans PyTorch).
