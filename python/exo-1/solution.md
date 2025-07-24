# Solution Détaillée de l'Exercice Pratique (Approche Dépôt Unifié)

Cette solution propose une structure de projet unifiée, où le code de l'API, les tests (unitaires et fonctionnels) et la configuration CI/CD cohabitent dans un seul et même dépôt Git.

## Structure Finale des Fichiers

Cette structure est simple, standard et facile à prendre en main pour un développeur.

```
projet_coach_artisan/
├── .git/
├── .gitlab-ci.yml
├── README.md
├── requirements.txt
├── src/
│   └── api/
│       ├── __init__.py
│       └── main.py
└── tests/
    ├── test_functional_endpoints.py
    └── test_unit_logic.py
```

* **`src/api/main.py`** : Le code source de l'application FastAPI.
* **`tests/test_unit_logic.py`** : Les tests unitaires, qui valident la logique interne sans serveur HTTP.
* **`tests/test_functional_endpoints.py`** : Les tests fonctionnels, qui valident les endpoints de l'API en utilisant le `TestClient` de FastAPI.
* **`.gitlab-ci.yml`, `README.md`, `requirements.txt`** : Fichiers de configuration et de documentation communs à tout le projet.

### Étape 1 : Code de l'API

Le code est placé dans une structure `src` pour une meilleure organisation.

**Fichier `src/api/main.py` :**
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Dict

app = FastAPI()
db: Dict[int, str] = {}

class Item(BaseModel):
    name: str

def init_db():
    """Fonction pour initialiser/réinitialiser la base de données en mémoire."""
    global db
    db = {1: "pomme", 2: "banane"}

@app.on_event("startup")
async def startup_event():
    """Initialise la base de données au démarrage de l'application."""
    init_db()

@app.get("/items", response_model=List[str])
def get_items():
    return list(db.values())

@app.post("/items", status_code=201)
def create_item(item: Item):
    if item.name in db.values():
        raise HTTPException(status_code=400, detail="Item already exists")
    new_id = max(db.keys() or [0]) + 1
    db[new_id] = item.name
    return {"id": new_id, "name": item.name}
```

### Étape 2 : Écriture des Tests

Les deux types de tests sont dans le même répertoire `tests/`.

**Fichier `tests/test_unit_logic.py` :**
```python
import pytest
from fastapi import HTTPException
from src.api import main

def test_create_item_logic_with_mock_db():
    # SETUP: On utilise une base de données propre pour ce test
    main.db = {10: "test_item"}
    
    # TEST: Cas nominal
    new_item = main.Item(name="orange")
    result = main.create_item(new_item)
    assert result["name"] == "orange"
    assert "orange" in main.db.values()

    # TEST: Cas d'erreur
    duplicate_item = main.Item(name="test_item")
    with pytest.raises(HTTPException) as excinfo:
        main.create_item(duplicate_item)
    assert excinfo.value.status_code == 400
```

**Fichier `tests/test_functional_endpoints.py` :**
```python
import pytest
from fastapi.testclient import TestClient
from src.api.main import app, init_db

# On crée un client de test qui interagit avec notre app FastAPI
client = TestClient(app)

@pytest.fixture(autouse=True)
def setup_and_teardown():
    """Ce fixture s'exécute avant chaque test pour garantir un état propre."""
    init_db()
    yield
    # Le code après 'yield' pourrait servir au nettoyage si nécessaire

def test_get_items():
    response = client.get("/items")
    assert response.status_code == 200
    json_response = response.json()
    assert isinstance(json_response, list)
    assert "pomme" in json_response
    assert "banane" in json_response

@pytest.mark.parametrize("item_name, expected_status", [
    ("raisin", 201),
    ("pomme", 400), # Item dupliqué
])
def test_post_item(item_name, expected_status):
    response = client.post("/items", json={"name": item_name})
    assert response.status_code == expected_status
    if expected_status == 201:
        # On vérifie que l'item a bien été ajouté
        get_response = client.get("/items")
        assert item_name in get_response.json()
```

### Étape 3 : Dépendances et Documentation

Un seul fichier pour toutes les dépendances.

**Fichier `requirements.txt` :**
```
fastapi
uvicorn[standard]
pytest
pytest-cov
ruff
```

**Fichier `README.md` :**
```markdown
# Kit de Démarrage API Python

Ce projet contient une API FastAPI et sa suite de tests (unitaires et fonctionnels).

## Installation

1.  Clonez le projet.
2.  Créez un environnement virtuel : `python3 -m venv venv`
3.  Activez-le : `source venv/bin/activate`
4.  Installez les dépendances : `pip install -r requirements.txt`

## Lancer l'API localement

```bash
uvicorn src.api.main:app --reload
```
L'API sera accessible à l'adresse `http://127.0.0.1:8000`.

## Lancer les Tests

Les tests n'ont pas besoin que l'API soit démarrée manuellement.
Depuis la racine du projet, lancez simplement :

```bash
pytest
```

Pour voir le rapport de couverture de code :
```bash
pytest --cov=src/api
```

### Étape 4 : Intégration Continue

Le pipeline est maintenant beaucoup plus simple.

**Fichier `.gitlab-ci.yml` :**
```yaml
image: python:3.9-slim

cache:
  key:
    files:
      - requirements.txt
  paths:
    - .cache/pip/

stages:
  - lint
  - test

before_script:
  - pip install -r requirements.txt

linting:
  stage: lint
  script:
    - ruff check . > ruff_report.txt 2>&1 || true
    - cat ruff_report.txt
    - ruff check .
  artifacts:
    paths:
      - ruff_report.txt
    when: always

testing:
  stage: test
  script:
    # On exécute tous les tests trouvés dans le répertoire tests/
    - pytest tests/ --cov=src/api --cov-report=term-missing --cov-report=xml
  coverage: '/(?i)total.*? (100(?:\.0+)?\%|[1-9]?\d(?:\.\d+)?\%)$/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml
```
