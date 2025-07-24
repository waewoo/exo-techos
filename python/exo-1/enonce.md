# Énoncé de l'Exercice Pratique - Coach en Qualité / Artisan Technique

**Durée : 1h30**

## Contexte & Scénario

Vous êtes notre nouveau Coach-Artisan. Votre mission est de créer un kit de démarrage "clés en main" pour une squad Python. Ce kit doit illustrer une stratégie de test complète et moderne dans une structure de projet unifiée, facile à prendre en main par un développeur junior.

## Votre Mission : Créer la Solution de A à Z

En partant de zéro, vous devez créer un **dépôt Git unique** contenant :

1.  Une API minimaliste.
2.  Une stratégie de test à deux niveaux : **unitaire** et **fonctionnel**.
3.  Un pipeline d'intégration continue pour industrialiser les vérifications.

## Contraintes et Exigences

#### Structure du Projet

* Le projet doit être un **dépôt Git unique**.
* Le code source de l'application doit se trouver dans un répertoire `src/`.
* Tous les tests (unitaires et fonctionnels) doivent se trouver dans un répertoire `tests/` à la racine.

#### L'API et ses Tests

* **API (dans `src/`)** :
    * Utilisez **FastAPI**.
    * Exposez deux endpoints : `GET /items` et `POST /items`.
* **Tests Unitaires (dans `tests/`)** :
    * Écrivez au moins un test unitaire pour la logique métier, sans dépendre d'un serveur HTTP.
* **Tests Fonctionnels (dans `tests/`)** :
    * Validez les endpoints de l'API en utilisant le `TestClient` de FastAPI (sans démarrer de serveur externe).
    * Écrivez un test pour `GET /items` et un test paramétré pour `POST /items`.

#### L'Intégration Continue et la Documentation

* **Dépendances (`requirements.txt`)** :
    * Le fichier doit lister toutes les dépendances nécessaires (`fastapi`, `pytest`, `ruff`, etc.).
* **GitLab CI (`.gitlab-ci.yml`)** :
    * Le pipeline doit contenir les stages `lint` et `test`.
    * Le job de `lint` doit utiliser **Ruff**. Son rapport doit être archivé en tant qu'artefact.
    * Le job de `test` doit exécuter tous les tests et générer un rapport de couverture de code.
* **Documentation (`README.md`)** :
    * Le README doit expliquer comment installer les dépendances, lancer l'API localement et exécuter la suite de tests.

### Question Bonus pour le Debriefing
