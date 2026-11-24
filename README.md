# FraudGuard

Détection de fraude carte bancaire en temps réel — POC de chaîne de données du M2 IMSD (Université Évry Paris-Saclay).

Une transaction est rejouée par le simulateur dans Kafka, scorée par l'API (`/score`, features Redis + modèle MLflow), archivée dans le Data Lake S3 (Bronze → Silver → Gold) et affichée dans le site web et les tableaux de bord.

## Équipe

| Rôle | Membre | Périmètre |
| --- | --- | --- |
| M1 | Elyes | Ingestion, streaming, base clients |
| M2 | Achraf | API, sécurité, plateforme |
| M3 | Manel | Data Lake et batch |
| M4 | Marwa | Site web et analytique |
| M5 | Karim | Data science |

## Arborescence

| Dossier | Contenu |
| --- | --- |
| `contracts/` | Contrats de données (Avro, JSON Schema) |
| `oltp/` | Base clients PostgreSQL (schéma, données fictives) |
| `ingestion/` | Simulateur de transactions → Kafka |
| `streaming/` | Spark Streaming → S3 Bronze |
| `api/` | API de scoring et API du site |
| `app/` | Site web |
| `alerts/` | Génération et stockage des alertes |
| `batch/` | Traitements batch Silver / Gold |
| `dags/` | DAG Airflow |
| `quality/` | Contrôles Great Expectations |
| `features/` | Calcul des features (Redis, Gold) |
| `ml/` | EDA, entraînement, MLflow |
| `dashboards/` | Superset, Grafana |
| `deploy/` | docker compose, Kubernetes, Helm |
| `tests/` | Tests automatisés |
| `docs/` | Contrats d'API, décisions techniques |

## Lancer le projet en local

### Prérequis

- Git
- Docker et Docker Compose v2
- Python 3.11

### Étapes

```bash
# 1. Cloner le dépôt
git clone https://github.com/Achraf-ABID/fraudguard.git
cd fraudguard

# 2. Créer le fichier de configuration local (jamais commité)
cp .env.example .env   # puis renseigner les mots de passe et le sel de hachage

# 3. Démarrer les services (Kafka, Schema Registry, PostgreSQL, …)
docker compose -f deploy/compose/docker-compose.yml up -d

# 4. Vérifier que tout tourne
docker compose -f deploy/compose/docker-compose.yml ps
```

> Le fichier `deploy/compose/docker-compose.yml` arrive avec la tâche M1-03 et `.env.example` avec la configuration des services. Les données Sparkov ne sont pas dans Git : elles sont sur S3 (`raw/`, tâche M3-01).

### Lancer les tests et le lint

```bash
pip install ruff pytest
ruff check .
pytest
```

## Règles Git

- `main` est protégée : pull request obligatoire, 1 relecture, CI verte.
- Une tâche = une issue = une branche `feature/M<n>-<sujet>`.
- Commits : `feat(api): …`, `fix(app): …`, `docs(rgpd): …`.
- Fusion en squash, branche supprimée après fusion.
- Jamais dans Git : données, modèles, `.env`, mots de passe, clés, sel de hachage.
- Toute modification d'un contrat (`contracts/`) prévient toute l'équipe dans la PR.
- Chaque choix technique est consigné dans `docs/decisions.md`.
