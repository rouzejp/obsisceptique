# Skills Hermes pour ObsiSceptique

Les skills sont des procédures réutilisables qu'Hermes Agent charge pour exécuter des tâches spécialisées. Voici les skills utilisés par ObsiSceptique.

## Skills du système

### obsisceptique-factcheck-workflow

Skill principal qui orchestre le pipeline complet de vérification.

**Déclencheur :** demande de vérification d'un claim
**Étapes :** recherche multi-sources → notes vault → PDF → email → article WordPress → archivage

Contenu détaillé : voir le [skill dans le dépôt Hermes](https://github.com/nousresearch/hermes-agent) ou le fichier SKILL.md local.

### fact-checking

Analyse les affirmations avec un système de notation (A à F), fournit preuves et contre-preuves.

**Source :** [Fabric](https://github.com/danielmiessler/fabric) / LobeHub
**Fonction :** analyse structurée des claims et sophismes

### obsidian

Lecture, écriture et recherche dans le vault.

**Commandes :**
```bash
# Recherche dans le vault
hermes search "mot clé" --vault /chemin/vers/obsisceptique
```

### wordpress

Publication sur le blog via l'API REST WordPress.

**Opérations supportées :**
- Création d'articles (titre, contenu HTML, catégories, meta)
- Upload d'images
- Gestion des catégories
- Gestion des menus

**Auth :** Application Password (Basic Auth)

## Ajouter un skill au projet

Créez un fichier `.hermes/skills/` ou utilisez la commande :

```bash
hermes skill create mon-skill --category custom
```

Exemple de structure SKILL.md :

```yaml
---
name: mon-skill
description: Description courte
version: 1.0.0
tags: [categorie]
---

# Mon Skill

Instructions détaillées...
```

## Pitfalls retenus

| Problème | Solution |
|----------|----------|
| WordPress ne convertit pas le Markdown | Fournir le contenu en HTML |
| Pandoc + pdflatex ne supporte pas les emojis (✅❌🟡) | Utiliser weasyprint pour le PDF |
| Himalaya peut mal encoder l'UTF-8 | Utiliser Python smtplib + Header() pour les emails |
| Wiki GitHub non créé par API | Visiter l'onglet Wiki dans un navigateur d'abord |
