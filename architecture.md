# Architecture technique Lolapp

## Vue d'ensemble

Lolapp utilise une architecture moderne basée sur une Progressive Web App (PWA) qui communique avec les bases de données clients via des webservices.

## Stack technique

### Version legacy (solisws.fr)

* **Frontend** : Vue.js 2
* **Backend** : Laravel (PHP) + Webservice Windev A
* **Base de données** : MySQL (Lolapp) + HFSQL (Lola)
* **Storage** : Minio (S3 compatible)

### Nouvelle version (beta.lola-france.fr)

* **Frontend** : Vue.js 3
* **Backend** : Laravel (PHP) + Webservice Windev B
* **Base de données** : MySQL (Lolapp) + HFSQL (Lola)
* **Storage** : Minio (S3 compatible)

## Diagrammes d'architecture

### Architecture générale

![Architecture générale](https://i.imgur.com/Meuu59F.png)

### Connecteur Lola vers Lolapp

![Connecteur Lola vers Lolapp](https://i.imgur.com/pJZwne7.png)

### Connexion HFSQL

![Connexion HFSQL](https://i.imgur.com/8awMfsf.png)

## Webservices Windev

### Webservice A (Legacy)

* **Version** : WEBDEV 28 Windows
* **Analyse** : Lola Version 2025.0
* **Caractéristiques** : Nombreux endpoints spécifiques
* **Compatibilité** : solisws.fr uniquement

### Webservice B (Nouveau)

* **Version** : WEBDEV 2025 Linux
* **Analyse** : Lola Version 2026.0
* **Caractéristiques** : Endpoint unique recevant les requêtes Laravel
* **Compatibilité** : beta.lola-france.fr

## Connexion aux bases clients

### Processus de connexion

#### Version legacy (solisws.fr)

1. Authentification via table `users` MySQL
2. Récupération `customer_id`
3. Lecture table `customer` pour identifiants HFSQL
4. Connexion au webservice A client
5. Requêtes via endpoints dédiés

#### Nouvelle version (beta.lola-france.fr)

1. Identification du client via sous-domaine
2. Lecture directe table `customers`
3. Authentification directe sur base HFSQL client
4. Requêtes Laravel transmises au webservice B
5. Endpoint unique de traitement

### Gestion des tokens

* Stockage dans `personal_access_tokens` (MySQL)
* Compatibilité Laravel Sanctum
* Types : Client, Personnel, Admin

## Stockage des fichiers

### Minio Object Storage

* **URL** : https://minio.solisws.fr
* **Organisation** : Un bucket par client (nom = apikey)
* **Types de fichiers** :
  * Images de personnalisation (background, logo, entête)
  * Documents générés (PV d'intervention)
  * Fichiers uploadés par les utilisateurs

### Structure des buckets

```
minio.solisws.fr/
├── {apikey-client-1}/
│   ├── background.jpg
│   ├── logo.png
│   ├── entete.jpg
│   └── documents/
└── {apikey-client-2}/
    ├── background.jpg
    └── ...
```

## Évolutions techniques récentes

### Migrations effectuées

* **HTTP/1 → HTTP/2** : Amélioration des performances
* **PHP 6 → PHP 8** : Modernisation du backend
* **Vue.js 2 → Vue.js 3** : Refonte du frontend
* **Endpoints Laravel** : Réécriture des webservices

### Problématiques actuelles

* **Perte de performance** : L'endpoint unique ralentit les échanges
* **Maintenance complexe** : Double gestion Laravel + Webservice
* **Debugging difficile** : Chaîne de traitement complexe

## Proposition d'amélioration

### Solution recommandée

1. **Déléguer** les webservices au développeur Windev
2. **Standardiser** les endpoints entre webservice A et B
3. **Simplifier** la consommation côté Laravel
4. **Optimiser** les performances réseau

### Impact sur le développement

* Modification mineure des appels API côté Laravel
* Conservation de la logique métier existante
* Amélioration significative des performances
* Simplification de la maintenance

***

[← Retour à l'index](./) | [Bases de données →](databases.md)
