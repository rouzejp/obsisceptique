# Publication WordPress

ObsiSceptique publie ses vérifications sur un blog WordPress via l'API REST.

## Architecture

```
Vault Obsidian (vérification terminée)
    ↓ (conversion markdown → HTML)
API REST WordPress (Basic Auth)
    ↓
Article publié sur le blog
    ↓
ActivityPub (fédération Mastodon/Fediverse)
    ↓
Webmentions (liens sortants)
```

## Catégories utilisées

| Catégorie | Slug | Usage |
|-----------|------|-------|
| Zététique | zetetique | Fact-checking et vérifications |
| Politique | politique | Analyse politique |

## Publication automatisée

Via l'API REST WordPress :

```bash
# Créer un article publié
curl -s -X POST -u "$WP_USER:$WP_APP_PASS" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Titre de l'article",
    "content": "<p>Contenu en HTML...</p>",
    "status": "publish",
    "categories": [33],
    "meta": {"content_origin": "ai"}
  }' \
  "$WP_SITE/wp-json/wp/v2/posts"
```

### Paramètres du payload

- `title` — Titre de l'article
- `content` — Corps en HTML (le Markdown n'est pas supporté)
- `status` — `draft`, `publish`, `pending` ou `private`
- `categories` — Tableau d'IDs de catégories
- `meta.content_origin` — `human`, `ai` ou `mixed`
- `excerpt` — Résumé (optionnel)

### Ajout au menu

```bash
# Ajouter un lien de catégorie dans le menu principal
curl -s -X POST -u "$WP_USER:$WP_APP_PASS" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Zététique",
    "url": "https://votre-site.com/category/zetetique/",
    "type": "custom",
    "menus": 17,
    "menu_order": 6
  }' \
  "$WP_SITE/wp-json/wp/v2/menu-items"
```

## Fédération ActivityPub

Le site WordPress peut être fédéré sur le Fediverse grâce au plugin ActivityPub. Les articles sont automatiquement :

1. Fédérés aux followers
2. Syndiqués sur Mastodon (via Bridgy ou script direct)
3. Dotés de webmentions (pour les liens sortants)

## IndieWeb / POSSE

Pour une syndication POSSE (Publish on Own Site, Syndicate Elsewhere) :

```json
"meta": {
  "_syndicate-to": ["bridgy-publish"],
  "content_origin": "ai"
}
```

## Post-publication

Après publication, une trace est créée dans le vault :

```
20_Usine_Contenu/03_Publie/YYYY-MM-DD-slug.md
```

avec frontmatter : titre, type, status, date, catégorie, ID WP, URL, tags.
