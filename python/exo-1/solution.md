---
---

# Solution Détaillée de l'Exercice Pratique

Ce document décrit, étape par étape, une solution possible à l'exercice. Il vous servira de guide pour évaluer le travail du candidat.

## Structure Finale des Fichiers

Le candidat devrait arriver à une structure de répertoires qui ressemble à ceci. Chaque fichier a un rôle précis dans la construction d'un projet de test robuste et maintenable.

```
test_session/
├── api_project/
│   └── api.py
└── api_test_kit/
    ├── .git/
    ├── venv/
    ├── tests/
    │   └── test_api.py
    ├── .gitlab-ci.yml
    ├── config.ini
    ├── README.md
    └── requirements.txt
```

* **`api_project/api.py`** : Contient le code source de l'API FastAPI. C'est le service que nous allons tester. Il est volontairement simple pour se concentrer sur la partie test.
* **`api_test_kit/`** : Le répertoire racine de notre kit de test. Il est conçu pour être un projet autonome et un dépôt Git indépendant.
* **`.git/`** : Répertoire interne créé par la commande `git init`. Il contient tout l'historique des versions et la configuration du dépôt Git.
* **`venv/`** : Répertoire de l'environnement virtuel Python. Il isole les dépendances du projet pour éviter les conflits et garantir la reproductibilité.
* **`tests/test_api.py`** : Le cœur du projet de test. Ce fichier contient les cas de test écrits avec le framework Pytest pour valider les endpoints de l'API.
* **`.gitlab-ci.yml`** : Fichier de configuration pour le pipeline d'intégration continue de GitLab. Il définit les étapes (`stages`) et les jobs (`lint`, `test`) à exécuter automatiquement.
* **`config.ini`** : Fichier de configuration permettant d'externaliser les paramètres variables, comme l'URL de base de l'API. Cela évite de les coder en dur dans les tests.
* **`README.md`** : Le fichier de documentation au format Markdown. Il est crucial car il explique comment un autre développeur peut installer, configurer et lancer le projet de test.
* **`requirements.txt`** : Liste toutes les bibliothèques Python nécessaires au projet avec leurs versions exactes. Il est généré par `pip freeze` et permet une installation fiable et reproductible des dépendances.

### Étape 1 à 3 : API, Projet de Test et Écriture des Tests

(Ces étapes restent inchangées)

### Étape 4 : Intégration Continue avec GitLab CI

Le pipeline est mis à jour pour que le job de `linting` génère un rapport qui est ensuite archivé en tant qu'artefact GitLab.

**Contenu du fichier `api_test_kit/.gitlab-ci.yml` :**

```yaml
image: python:3.9-slim

stages:
  - lint
  - test

before_script:
  - pip install -r requirements.txt
  - apt-get update && apt-get install -y procps
  - uvicorn api_project.api:app --host 0.0.0.0 --port 8080 &
  - sleep 5

linting:
  stage: lint
  script:
    - ruff check tests/ > ruff_report.txt 2>&1 || true
    - ruff format --check tests/ >> ruff_report.txt 2>&1 || true
    - cat ruff_report.txt
    - ruff check tests/
  artifacts:
    name: "Rapport de Linting Ruff"
    paths:
      - ruff_report.txt
    when: always

testing:
  stage: test
  script:
    - pytest --cov=tests --cov-report=term-missing --cov-report=xml
  coverage: '/(?i)total.*? (100(?:\.0+)?\%|[1-9]?\d(?:\.\d+)?\%)$/'
  artifacts:
    when: always
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml
```

### Étape 5 : Documentation & Debriefing

(Cette étape reste inchangée)

### Étape 6 (Bonus) : Optimisation du Pipeline avec le Cache

Cette partie correspond à la question bonus posée au candidat.

> #### 💡 Question Bonus à poser pendant le debriefing
>
> * *"Votre pipeline réinstalle les dépendances Python à chaque exécution. Comment pourriez-vous optimiser ce comportement pour accélérer significativement les exécutions futures ?"*
>
> * **Ce que vous cherchez :** Le candidat doit parler du **cache de GitLab CI**. Il doit expliquer que le cache permet de sauvegarder des fichiers et répertoires entre les exécutions de jobs. L'idée est de mettre en cache le répertoire de `pip` pour ne pas avoir à retélécharger et réinstaller les dépendances si le fichier `requirements.txt` n'a pas changé.

**Solution Technique attendue :**

Le candidat doit proposer d'ajouter une section `cache` au fichier `.gitlab-ci.yml`.

**Contenu final du fichier `api_test_kit/.gitlab-ci.yml` avec le cache :**

```yaml
image: python:3.9-slim

# Définition du cache au niveau global pour tous les jobs
cache:
  key:
    files:
      - requirements.txt # La clé du cache est basée sur le contenu de ce fichier
  paths:
    - .cache/pip/ # On sauvegarde le répertoire de cache de pip
  policy: pull-push # On télécharge le cache en début de job et on le met à jour en fin de job

stages:
  - lint
  - test

before_script:
  # pip utilisera le cache s'il est disponible, accélérant grandement cette étape
  - pip install -r requirements.txt
  - apt-get update && apt-get install -y procps
  - uvicorn api_project.api:app --host 0.0.0.0 --port 8080 &
  - sleep 5

linting:
  stage: lint
  script:
    - ruff check tests/ > ruff_report.txt 2>&1 || true
    - ruff format --check tests/ >> ruff_report.txt 2>&1 || true
    - cat ruff_report.txt
    - ruff check tests/
  artifacts:
    name: "Rapport de Linting Ruff"
    paths:
      - ruff_report.txt
    when: always

testing:
  stage: test
  script:
    - pytest --cov=tests --cov-report=term-missing --cov-report=xml
  coverage: '/(?i)total.*? (100(?:\.0+)?\%|[1-9]?\d(?:\.\d+)?\%)$/'
  artifacts:
    when: always
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml
```

## Grille d'Évaluation Synthétique (Mise à Jour)

| Axe d'Évaluation | Ce qu'on observe | Niveau (1 à 5) | Commentaires |
| :--- | :--- | :--- | :--- |
| Maîtrise Python/FastAPI | Capacité à créer une API simple et fonctionnelle. | | |
| Fondamentaux (Artisan) | Aisance avec Linux, Git, venv, pip. | | |
| Maîtrise de Pytest | Capacité à écrire des tests, créer des fixtures, utiliser la paramétrisation. | | |
| **Maîtrise de la CI/CD** | **Capacité à créer un pipeline GitLab CI fonctionnel (stages, jobs, coverage, artifacts).** | | |
| Qualité du Code | Clarté, simplicité, respect du principe DRY, séparation config/code. | | |
| Posture de Coach | Qualité du README.md, clarté des explications orales, capacité à vulgariser. | | |
| Vision Stratégique | Pertinence des améliorations proposées, **capacité à identifier des optimisations (cache CI)**. | | |

**Décision**

Go / No-Go / À revoir
