# Énoncé de l'Exercice Pratique - Coach en Qualité / Artisan Technique

**Durée : 1h30**

* **Création de l'API et du projet de test :** 60 minutes
* **Intégration Continue & Documentation :** 30 minutes

## Contexte & Scénario

Vous êtes notre nouveau Coach-Artisan. Votre toute première mission est de créer un kit de démarrage de test "clés en main" pour une de nos squads Python. Cette squad doit développer une nouvelle API REST et n'a aucune expérience en automatisation de tests.

Votre livrable doit être si simple et si bien documenté qu'un développeur junior puisse le prendre en main, comprendre la structure, et lancer les tests en moins de 10 minutes, à la fois **localement et dans la CI/CD**.

## Votre Mission : Créer la Solution de A à Z

Vous partez d'une feuille blanche. Vous devez créer à la fois une API minimaliste et le projet de test qui la valide, puis industrialiser l'exécution de ces tests. L'important n'est pas la complexité de l'API, mais la qualité, la simplicité et la robustesse de la solution globale que vous allez construire.

N'hésitez pas à poser des questions et à verbaliser vos pensées.

## Contraintes et Exigences

#### L'API (à créer) :

* Utilisez **FastAPI**.
* Elle doit exposer deux endpoints : `GET /items` et `POST /items`.
* Le code de l'API doit être dans un fichier `api.py`.

#### Le Projet de Test (à créer) :

* Le projet doit être dans un répertoire séparé.
* Utilisez un **environnement virtuel** Python.
* Utilisez **Pytest** comme framework de test et **requests** comme client HTTP.
* Les dépendances doivent être listées dans un fichier `requirements.txt`.
* Le projet doit être un dépôt **Git** initialisé.

#### Les Tests (à écrire) :

* Un test pour l'endpoint `GET /items`.
* Un test **paramétré** pour l'endpoint `POST /items`.
* La configuration de l'URL de l'API ne doit pas être en dur dans le code des tests.

#### L'Intégration Continue (à créer) :

* Créez un fichier `.gitlab-ci.yml`.
* Le pipeline doit contenir au minimum deux étapes (`stages`) : une pour le **lint** et une pour le **test**. Le stage de test ne doit s'exécuter que si le stage de lint a réussi.
* Le job de lint doit vérifier la qualité du code Python en utilisant **Ruff**.
* **La sortie du linter (son rapport) doit être archivée en tant qu'artefact GitLab.**
* Le job de test doit exécuter la suite de tests Pytest et générer un **rapport de couverture de code**, également intégré à GitLab.

#### La Documentation (à rédiger) :

* Un fichier `README.md` doit être créé. Il doit expliquer comment installer et lancer les tests localement.

## Déroulé Attendu

1.  Mise en place de la structure des répertoires.
2.  Développement de l'API minimaliste (`api.py`).
3.  Mise en place du projet de test (environnement virtuel, Git, dépendances).
4.  Écriture des tests.
5.  **Création du pipeline d'intégration continue (`.gitlab-ci.yml`).**
6.  Rédaction de la documentation (`README.md`).

À la fin, vous nous présenterez oralement votre solution en expliquant vos choix.

---

### Question Bonus pour le Debriefing

Pendant la présentation finale, soyez prêt à répondre à la question suivante :

* *"Votre pipeline réinstalle les dépendances Python à chaque exécution. Comment pourriez-vous optimiser ce comportement pour accélérer significativement les exécutions futures ?"*
