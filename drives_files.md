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

Lors de cette upload ce passe en 2 étapes :

1. Le fichier est uploadé dans un bucket temporaire.
Durant cette étape, le fichier est enregistré avec un nom temporaire et une URL d’accès temporaire est générée. Cette URL permet de télécharger le fichier avant qu’il ne soit déplacé vers le bucket final.
2. Puis le fichier est signé grace a l'express qui est un serveur qui tourne a cette adresse : 

[express.solisws.fr](https://express.solisws.fr)

Puis sa enreistre finalement dans le minio avec un nom spécifique (cf. ). 

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

## Fonctionnalité : Authentification par OTP (One Time Password) via Express

1. **Saisie du numéro de téléphone**  
   L’utilisateur saisit son numéro de téléphone dans le champ prévu à cet effet sur la page de connexion.

2. **Recherche des clients associés**  
   Lors de la soumission du formulaire, la méthode `sendOtpByPhone` est appelée. Elle effectue une requête vers le store Vuex avec l’action `V2space/findClientsByPhone` en passant le numéro de téléphone.
   - Si plusieurs clients sont trouvés, une boîte de dialogue permet à l’utilisateur de choisir le bon client.
   - Si un seul client est trouvé, il est sélectionné automatiquement.

3. **Envoi de l’OTP**  
   Après la sélection du client, la méthode `startOTP` du composant `DialogOTP` est appelée pour envoyer un code OTP au numéro de téléphone de l’utilisateur.

4. **Vérification de l’OTP et connexion**  
   Lorsque l’utilisateur saisit le code OTP reçu, la méthode `login` est appelée :
   - Elle récupère les paramètres nécessaires via `sms/getParameters`.
   - Elle construit un objet payload contenant le numéro, l’ID du client, le code OTP, l’utilisateur et le mot de passe SMS.
   - Elle envoie ce payload à l’action `auth/loginPublic` du store, qui effectue l’appel au serveur Express pour vérifier le code et connecter l’utilisateur.
   - En cas de succès, l’espace client est vidé et l’utilisateur est redirigé vers la page d’accueil de son espace.

5. **Sécurité**  
   Toutes les étapes critiques (sélection du client, envoi et vérification de l’OTP) passent par des actions du store Vuex, qui elles-mêmes font des appels API vers le backend Express.

**Résumé technique :**  
L’appel au serveur Express se fait via des actions Vuex (`dispatch`) qui envoient les données nécessaires (numéro, code OTP, etc.) au backend. Le backend gère la logique d’envoi et de vérification du code OTP pour sécuriser l’accès à l’espace client.