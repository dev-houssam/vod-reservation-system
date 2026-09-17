# VOD — Application Web de gestion de réservations

> Projet de **Systèmes d’Information — Master 1 ILIADE & TIIL-A, UBO — 2025/2026**

## 📖 Présentation

Ce projet consiste à concevoir et développer une **application Web de gestion de réservations de films à la demande (VOD)**.

L'application permet aux utilisateurs de consulter un catalogue de films, leurs acteurs et réalisateurs, d'effectuer des réservations et de les payer via une API de pseudo-paiement. Les utilisateurs peuvent également évaluer les films qu'ils ont réservés.

L'application intègre également un espace d'administration permettant de gérer les films, les artistes et leur disponibilité à la location.

Le projet est réalisé **en groupe de trois étudiants** et repose sur une architecture composée de plusieurs **services REST développés avec différentes technologies**.

---

## 🎯 Objectifs

L'objectif est de mettre en pratique la conception d'un système d'information distribué combinant :

* plusieurs services REST ;
* différentes technologies backend ;
* plusieurs systèmes de gestion de bases de données ;
* une interface Web commune ;
* une séparation entre les couches d'accès aux données et les services métier ;
* le déploiement des services dans un environnement Docker.

---

# 🏗️ Architecture

L'application s'appuie sur plusieurs services spécialisés.

```text
                         ┌──────────────────────┐
                         │      Vue.js          │
                         │   Interface Web      │
                         └──────────┬───────────┘
                                    │
                         ┌──────────┴───────────┐
                         │       APIs REST      │
                         └──────────┬───────────┘
                                    │
             ┌──────────────────────┼──────────────────────┐
             │                      │                      │
             ▼                      ▼                      ▼
      ┌─────────────┐        ┌─────────────┐        ┌─────────────┐
      │    Spring   │        │    Spring   │        │   Servlet   │
      │    Films    │        │ Réservations│        │ Évaluations │
      └──────┬──────┘        └──────┬──────┘        └──────┬──────┘
             │                      │                      │
             │                      │                      │
             ▼                      ▼                      ▼
       ┌─────────────┐       ┌─────────────┐       ┌─────────────┐
       │     SQL     │       │     SQL     │       │   MongoDB   │
       │    + JPA    │       │    + JPA    │       │             │
       └─────────────┘       └─────────────┘       └─────────────┘

                         ┌─────────────────────┐
                         │ Servlet — Affiches  │
                         │      MongoDB         │
                         └─────────────────────┘
```

Les API d'accès aux **films et réservations** sont développées avec **Spring**, tandis que les API dédiées aux **évaluations et aux affiches** sont développées avec des **Servlets**.

---

# 🎬 Gestion des films

L'application permet aux utilisateurs de consulter le catalogue de films.

Chaque film peut notamment être associé à :

* un titre unique ;
* une année de réalisation ;
* un réalisateur ;
* plusieurs acteurs et actrices ;
* plusieurs genres ;
* un âge minimum ;
* une affiche ;
* un prix de location.

Les utilisateurs peuvent également rechercher les films :

* d'un réalisateur ;
* d'un acteur ou d'une actrice ;
* d'un genre.

Les films peuvent être ouverts ou fermés à la location par l'administrateur.

---

# 👤 Gestion des utilisateurs

Un utilisateur doit créer un compte afin d'accéder aux fonctionnalités de réservation.

Le compte contient notamment :

* un pseudo unique ;
* nom ;
* prénom ;
* âge ;
* adresse ;
* mot de passe.

Après authentification, l'utilisateur peut accéder à ses réservations et effectuer de nouvelles locations.

---

# 📺 Réservations

Un utilisateur connecté peut réserver des films disponibles à la location.

Certaines règles métier sont appliquées :

* un utilisateur peut avoir **au maximum trois films en réservation simultanément** ;
* l'âge de l'utilisateur doit respecter l'âge minimum du film ;
* une réservation doit être payée pour être finalisée ;
* une location peut être terminée à tout moment ;
* l'utilisateur peut consulter les films actuellement en location.

---

# 💳 Pseudo-paiement

Une API de **pseudo-paiement par carte bancaire** est intégrée à l'application.

Elle permet de simuler le processus de paiement d'une réservation, avec notamment des cas d'échec afin de reproduire différents comportements possibles d'un service de paiement.

> Il s'agit d'une **simulation** : aucune transaction bancaire réelle n'est effectuée.

---

# ⭐ Évaluations

Les utilisateurs peuvent évaluer les films qu'ils ont réservés.

Une évaluation comprend :

* une note entière de **0 à 5** ;
* un commentaire textuel facultatif ;
* le pseudo de l'utilisateur.

Un utilisateur ne peut évaluer un film que s'il l'a réservé, soit actuellement, soit dans le passé.

Les évaluations peuvent être consultées publiquement, sans authentification, et l'application permet également de récupérer la **moyenne des évaluations d'un film**.

Les évaluations sont stockées dans **MongoDB**.

---

# 🖼️ Affiches des films

Une affiche peut être associée à chaque film.

Les fichiers d'affiches sont stockés dans **MongoDB**, séparément des données métier conservées dans les bases SQL.

---

# 🛠️ Administration

L'administrateur dispose de fonctionnalités permettant notamment de :

* créer des films ;
* créer des artistes ;
* définir les informations d'un film ;
* ouvrir un film à la location ;
* fermer un film à la location ;
* définir le prix de location ;
* modifier le prix pendant la période de location.

Un film nouvellement créé est initialement **fermé à la location**.

La fermeture d'un film n'interrompt pas les locations déjà en cours : celles-ci restent actives jusqu'à leur terme.

---

# 🗄️ Gestion des données

Le projet utilise deux types de bases de données.

### SQL

Les informations relatives aux films et aux réservations sont stockées dans des bases **SQL**.

L'accès aux données est réalisé à l'aide de **JPA** et d'entités Java.

### MongoDB

**MongoDB** est utilisé pour stocker :

* les affiches des films ;
* les évaluations associées aux films.

Cette séparation permet d'utiliser différents modèles de stockage selon la nature des données manipulées.

---

# 🌐 Interface Web

L'ensemble des fonctionnalités est accessible depuis une interface Web développée avec **Vue.js**.

L'interface permet aux différents types d'utilisateurs d'interagir avec les services de l'application :

```text
Utilisateur
    │
    ├── Catalogue
    ├── Films
    ├── Acteurs / Réalisateurs
    ├── Réservations
    ├── Paiement
    └── Évaluations

Administrateur
    │
    ├── Films
    ├── Artistes
    ├── Locations
    └── Prix
```

---

# 🔌 APIs REST

L'application est composée de plusieurs **APIs REST**, réparties entre les différents services du projet.

Les technologies utilisées dépendent du service :

| Fonctionnalité   | Technologie |
| ---------------- | ----------- |
| Films            | Spring      |
| Réservations     | Spring      |
| Évaluations      | Servlet     |
| Affiches         | Servlet     |
| Interface client | Vue.js      |

Les services sont conçus pour pouvoir fonctionner avec les services développés par les autres groupes de travaux pratiques afin de constituer l'application complète.

---

# 🐳 Déploiement

Les différents services REST sont destinés à être déployés dans un environnement **Docker** mis à disposition pour le projet.

Docker permet notamment de reproduire plus facilement l'environnement nécessaire à l'exécution des différents composants.

---

# 🔄 Architecture des données

La conception distingue les différentes responsabilités du système :

```text
                    Application Web
                          │
                          ▼
                    APIs REST
                          │
              ┌───────────┴───────────┐
              │                       │
        Couche métier           Couche données
              │                       │
              └───────────┬───────────┘
                          │
                ┌─────────┴─────────┐
                │                   │
              SQL               MongoDB
             + JPA          Évaluations / Affiches
```

Une couche d'accès aux données est utilisée par les services Web afin de maintenir une séparation entre l'accès aux données et l'implémentation des APIs.

---

# 🧰 Technologies

### Backend

* **Java**
* **Spring**
* **Servlet**
* **API REST**
* **JPA**

### Frontend

* **Vue.js**

### Bases de données

* **SQL**
* **MongoDB**

### Infrastructure et outils

* **Docker**
* **Git**

---

# 👥 Travail en groupe

Projet réalisé **en groupe de trois étudiants** dans le cadre de l'UE **Systèmes d'Information**.

Le projet implique également l'intégration de services développés par différents trinômes afin de constituer une application complète.

---

# 🎓 Contexte académique

**Master 1 Informatique — ILIADE & TIIL-A**
**Université de Bretagne Occidentale (UBO)**
**UE : Systèmes d'Information**
**Année universitaire : 2025/2026**

**Projet : Application Web VOD — gestion des réservations**

**Période : février – mars 2026**


## LICENCE MIT

