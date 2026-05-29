# Forker ObsiSceptique

Ce projet est conçu pour être **forké et adapté** à vos propres besoins. Licence CC-BY-NC 4.0.

## Pour qui ?

- **Journalistes** — standardiser vos vérifications
- **Militants associatifs** — outiller votre veille
- **Enseignants / chercheurs** — enseigner la zététique
- **Blogueurs sceptiques** — professionnaliser votre fact-checking
- **Curieux** — comprendre la méthode

## Ce que vous récupérez

| Composant | Fichier |
|-----------|---------|
| Structure vault | `_index.md`, `00_Index/` à `08_Veille/` |
| Templates de fiches | `07_Templates/template-fact-check.md` |
| Architecture agents | `AGENTS.md` |
| Workflow complet | `_system/02-workflow.md` |
| Configuration Hermes | `_system/03-hermes-setup.md` |
| Exemple de vérification | `03_Verifications/03c_nuance/melenchon-merah-juin2021.md` |
| Glossaire des biais | `00_Index/glossaire-biais-cognitifs.md` (65+ biais) |

## Ce que vous devez adapter

1. **Vault Obsidian** — ouvrir le coffre dans Obsidian (`Fichier → Ouvrir un coffre`)
2. **Hermes Agent** — installer, configurer votre API key, adapter `_system/03-hermes-setup.md`
3. **WordPress** — créer vos propres credentials d'application, adapter les IDs de catégories
4. **SMTP** — configurer votre serveur mail
5. **Watchers** — remplacer les flux RSS par ceux qui vous intéressent

## Guide de démarrage rapide

```bash
# 1. Cloner
git clone https://github.com/votre-compte/obsisceptique.git
cd obsisceptique

# 2. Installer Hermes Agent
pip install hermes-agent
hermes setup

# 3. Configurer
mkdir -p .hermes/skills
# Copier les skills depuis _system/04-skills.md

# 4. Démarrer
hermes chat  # Session interactive
```

## Architecture recommandée

```
~/obsisceptique/          ← Ce dépôt (le vault)
~/.hermes/                ← Configuration Hermes
  ├── config.yaml
  ├── .env                ← Credentials (NON commités)
  ├── skills/
  │   ├── obsisceptique-factcheck-workflow/
  │   └── ...
  └── kanban/
      └── default.db      ← Tableau Kanban SQLite
```

## Licence

CC-BY-NC 4.0 — utilisation libre pour usage non commercial, avec attribution.

**Vous devez :**
- Créditer l'auteur original (Jean-Paul Rouzé — rouze.eu)
- Indiquer les modifications
- Ne pas faire d'utilisation commerciale

**Pour une utilisation commerciale :** contactez l'auteur.

## Contribuer

Les contributions sont les bienvenues via :
- Issues GitHub (suggestions, bugs)
- Pull requests (améliorations, nouveaux templates)
- Signalement de claims à vérifier

## Communauté

ObsiSceptique est un projet vivant. N'hésitez pas à :
- Forker et adapter
- Partager vos vérifications
- Proposer des améliorations du workflow
- Signaler des sources pertinentes
