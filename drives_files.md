# Endpoints /drive

Voici plus de détails sur les endpoints `/drive` présents dans la codebase :

## Déclaration des routes

### 1. `/drive/upload`
- **Méthode** : POST
- **Contrôleur** : `DriveController@uploadFiles`
- **Description** : Permet d’uploader un fichier dans le drive de l’entreprise.
- **Paramètres attendus**  :
  - `file` (obligatoire)
  - `display_name` (optionnel)
  - `project_id` (optionnel, doit exister)
- **Réponse** :
  - `201` infos du fichier si succès
  - `400` si fichier manquant (`{ "message": "Vous devez choisir un fichier" }`)

### 2. `/drive/search`
- **Méthode** : POST
- **Contrôleur** : `DriveController@searchFiles`
- **Description** : Permet de rechercher/lister les fichiers du drive.
- **Paramètres optionnels** : pagination, tri, filtres par date, etc.
- **Réponse** : Liste paginée des fichiers, chaque fichier avec une URL temporaire d’accès.

### 3. `/drive`
- **Méthode** : POST
- **Contrôleur** : `DriveController@deleteFiles`
- **Description** : Permet de supprimer un ou plusieurs fichiers du drive.
- **Paramètre attendu** :
  - `file_ids` (array d’IDs de fichiers à supprimer)
- **Réponse** : JSON avec la liste des fichiers supprimés.