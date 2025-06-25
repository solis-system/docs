# Affichage des Entrées API avec Laravel Telescope

## Présentation

Pour le monitoring et le debug des requêtes API, les deux webservices principaux de Lolapp (solisws et lola-france) utilisent **Laravel Telescope**. Cet outil permet de visualiser en temps réel toutes les entrées HTTP, les requêtes, les erreurs, les jobs, les logs et bien plus.

## Accès aux interfaces Telescope

- **solisws** : [https://api.solisws.fr/telescope/requests](https://api.solisws.fr/telescope/requests)
- **lola-france** : [https://telescope.lola-france.fr/telescope/requests](https://telescope.lola-france.fr/telescope/requests)

## Fonctionnalités principales

- **Suivi des requêtes HTTP** :
  - Affichage de toutes les requêtes entrantes (GET, POST, etc.)
  - Détail des headers, payloads, réponses et temps d'exécution
- **Analyse des erreurs** :
  - Visualisation des exceptions et erreurs serveur
  - Stacktrace détaillée pour chaque erreur
- **Monitoring des jobs et queues** :
  - Statut, durée, payload et logs associés
- **Logs et événements** :
  - Affichage des logs applicatifs
  - Suivi des événements système (authentification, notifications, etc.)

## Utilisation

- **Debug en temps réel** :
  - Permet de diagnostiquer rapidement les problèmes d'API ou de logique métier
  - Utile pour le support technique et le développement
- **Audit de sécurité** :
  - Historique des accès et des actions utilisateurs
  - Détection d'anomalies ou de comportements suspects
- **Optimisation des performances** :
  - Analyse des temps de réponse
  - Identification des endpoints lents ou sur-sollicités

## Solution avec LogViewer

En complément de Telescope, **LogViewer** permet d'explorer facilement les logs générés par Laravel (`laravel.log`). Cet outil est particulièrement utile pour sélectionner et filtrer les logs, notamment pour l'analyse des erreurs.

- [Documentation officielle](https://laravelplug.com/laravel-log-viewer/)

### Installation

1. Installer le package :
   ```
   composer require arcanedev/log-viewer
   ```
2. Ajouter le provider dans `config/app.php` :
   ```php
   Arcanedev\LogViewer\LogViewerServiceProvider::class,
   ```
3. Publier les assets :
   ```
   php artisan log-viewer:publish
   ```
4. Accéder à l'interface :  
   `http://{votre-projet}/log-viewer`

Aperçu de l'interface :  
![Dashboard](https://laravelplug.com/wp-content/uploads/2022/01/1-dashboard.jpg)  
![Liste des logs](https://laravelplug.com/wp-content/uploads/2022/01/2-logs-list.jpg)

### Accès à LogViewer

- **solisws** : [https://api.solisws.fr/log-viewer](https://api.solisws.fr/log-viewer)
- **lola-france** : [https://telescope.lola-france.fr/log-viewer](https://telescope.lola-france.fr/log-viewer)

---

[← Retour à l'index](README.md)
