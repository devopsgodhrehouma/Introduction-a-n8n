# Apprendre n8n par des projets ultra simples

Une mini-formation pour découvrir l'automatisation avec **n8n**, installée **en local** avec **Docker Desktop**. On part de zéro et on monte en douceur, projet après projet.

## Prérequis

- Un ordinateur (Windows, macOS ou Linux).
- **Docker Desktop** installé (expliqué pas à pas en Leçon 2).
- Aucune connaissance en programmation requise.

## Le plan du cours

| # | Leçon | Ce que tu apprends |
|---|-------|--------------------|
| 1 | [Comprendre n8n](lecon-01-comprendre-n8n.md) | Théorie : workflow, nœud, trigger, items (rien à installer) |
| 2 | [Installer n8n avec Docker](lecon-02-installer-n8n-docker.md) | Docker Desktop + `docker-compose.yml`, n8n sur `localhost:5678` |
| 3 | [Premier workflow « Hello n8n »](lecon-03-premier-workflow-hello.md) | Manual Trigger, Edit Fields, expressions `{{ }}` |
| 4 | [Projet : la citation du jour](lecon-04-projet-citation-du-jour.md) | Schedule Trigger + HTTP Request (appeler une API) |
| 5 | [Projet : un mini-webhook](lecon-05-projet-webhook.md) | Webhook : recevoir des données et répondre |
| 6 | [Projet : de l'API vers un fichier](lecon-06-projet-api-vers-fichier.md) | Liste d'API → CSV sauvegardé sur ta machine |

## Les fichiers fournis

- **`docker-compose.yml`** — démarre n8n en local (Leçon 2).
- **`workflow-01-hello.json`** — le workflow de la Leçon 3, importable.
- **`workflow-02-citation.json`** — le projet de la Leçon 4, importable.
- **`workflow-03-webhook.json`** — le projet de la Leçon 5, importable.
- **`workflow-04-api-vers-fichier.json`** — le projet de la Leçon 6, importable.
- **`n8n_data/`** et **`fichiers/`** — créés automatiquement au 1er démarrage (tes données et le dossier partagé).

## Démarrage rapide

```bash
# Depuis ce dossier (qui contient docker-compose.yml)
docker compose up -d
# Puis ouvre http://localhost:5678 dans ton navigateur
```

Pour importer un projet : dans n8n, menu **⋮** → **Import from File...** → choisis le fichier `.json`.

> Conçu pour débuter : chaque leçon est un mode d'emploi pas à pas, avec analogies, schémas et tableaux d'erreurs fréquentes.
