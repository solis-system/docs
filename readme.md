## 1. Vue d'ensemble

**Lolapp** est une application **PWA** (Progressive Web App) destinée aux clients professionnels utilisant notre logiciel **Lola**.  
Elle permet d’accéder à distance à certaines fonctionnalités et données de Lola via une interface moderne, responsive et accessible depuis un navigateur le plus souvent d'un smartphone.

### Architecture technique

Sur l'ancienne version hébergée sur solisws.fr

- **Frontend** : Vue.js 2
- **Backend pour connexion et token** : Laravel (PHP)
- **Backend** : Webservice Windev
- **Base de données Lolapp** : MySQL
- **Stockage fichiers & images** : Minio (object storage compatible S3)

Sur la nouvelle version en cours de développement hébergée sur beta.lola-france.fr

- **Frontend** : Vue.js 3
- **Backend** : Laravel (PHP)
- **Connecteur base Lola** : Webservice Windev (Un seul endpoint où laravel transmet les requetes)
- **Base de données Lolapp** : MySQL
- **Stockage fichiers & images** : Minio (object storage compatible S3)

![Architecture générale](https://i.imgur.com/Meuu59F.png)

## 2. Connexion à la base Lola (HFSQL)

La base **HFSQL** du logiciel Lola est généralement hébergée **en local** chez le client (poste serveur).
Pour interagir avec cette base à distance, un **webservice Windev** (versions `A` ou `B`) sert de **connecteur entre Lolapp et Lola**.

![Connecteur Lola vers Lolapp](https://i.imgur.com/pJZwne7.png)

Depuis solisws.fr
Lorsque l'utilisateur saisit son mail et mot de passe, une requete est d'abord faite à la base MariaDB à la table users pour voir si c'est correct puis on récupère le customer_id.
Grâce à cela, on obtient dans la table customer :

- Les identifiants de connexion à la BASE HFSQL chez le client
- La version du webservice à utiliser (solisws.fr n'est compatible qu'avec le webservice A)
  Nous sommes contraints de créer un utilisateur avec ses rôles et autres droits et de maintenir en doublons la liste alors que ces utilisateurs sont dans la base HFSQL du client.
  Les requetes sont faites à travers des endpoint créés avec le Webservice A

Depuis beta.lola-france.fr
L'utilisateur se connecte de la même façon mais depuis un sous domaine dédiée à l'entreprise.
Pour ce faire, nous avons ajouté le nom du sous domaine dans le champ key de la table customer. (exemple sam.beta.lola-france.fr)
Cela permet de connaitre, directement au chargement de la page, avant même que l'utilisateur se connecte :

- Les identifiants de connexion à la BASE HFSQL chez le client
- La version du webservice à utiliser (beta.lola-france.fr est compatible avec le webservice A et le webservice B)
  les paramètres de connexions à la base HFSQL du client puisque dans la table customers,
  Ensuite on contrôle directement l'identification (email et mot de passe) via une requete sur la base HFSQL du client.
  Les requetes sont faites avec Laravel et une grande partie des endpoints a été recréée.
  Ces requetes sont ensuite transmises à l'unique endpoint

Dans les deux cas, les tokens sont gérés et enregistrés dans MariaDB dans la table personal_access_tokens

![Connexion HFSQL](https://i.imgur.com/8awMfsf.png)

## 3. Architecture des bases de données

### 3.1 Base de données Lolapp (MySQL/MariaDB)

La base MySQL de Lolapp gère :
- **Authentification et tokens** des utilisateurs
- **Configuration des entreprises clientes** (connexions, personnalisation)
- **Stockage des fichiers** uploadés via l'application
- **Comptes administrateurs** de la plateforme

### 3.2 Base de données Lola (HFSQL)

La base HFSQL de chaque client contient :
- **Données métier** (clients, projets, devis, interventions)
- **Personnel** de l'entreprise
- **Configuration** et paramètres spécifiques au client
- **Historique** des opérations commerciales

![Séparation des bases de données](https://i.imgur.com/architecture_bdd.png)

## 4. Détail des webservices A et B

Le webservice A correspond au serveur d'application WEBDEV 28 - Version Windows (c'est la version historique du projet Lolapp)
Ce webservice A a été installé avec l'analyse de notre version Lola actuelle (Version 2025.0)
De nombreux endpoints ont été créés dans cette version du serveur d'application.

Le webservice B correspond au serveur d'application WEBDEV 2025 - Version Linux
Ce webservice B a été installé avec l'analyse de notre future version Lola (Version 2026.0).
Dans ce webservice B, il n'y a qu'un seul Endpoint, celui qui récupère les requetes faites par Laravel et qui lui sont transmises à la fin.

## 5. Tables de la base Lolapp (MySQL)

### 5.1 Table `customers` (Modèle `Company`)

Chaque entreprise cliente est enregistrée dans cette table, contenant toutes les informations nécessaires pour établir la connexion entre Lolapp et sa base Lola.

#### Champs importants

| Champ             | Description                                                               |
| ----------------- | ------------------------------------------------------------------------- |
| `key`             | Sous-domaine de la PWA (ex. `sm30` → `https://sm30.beta.lola-france.fr/`) |
| `database_server` | IP publique du serveur HFSQL du client                                    |
| `database_name`   | Nom de la base HFSQL (défaut = `Données` si vide)                         |
| `webservice`      | `A` = version Windev 28 / `B` = version 2025                              |
| `apikey`          | Clé de licence identique à celle de la base Lola                          |
| `color`           | Code couleur HEX des boutons de l’interface (ex. `#FF5733`)               |
| `background`      | Nom de l'image de fond utilisée dans Lolapp                               |
| `logo`            | Nom du logo affiché dans Lolapp                                           |
| `entete`          | Nom de l'image utilisée en entête des documents (ex. PV de réception)     |
| `fb_url`          | Ancienne URL d’avis Facebook (non utilisée actuellement)                  |
| `ggl_review_url`  | Ancienne URL d’avis Google (non utilisée actuellement)                    |

### 5.2 Table `admin` (Modèle `User`)

Cette table gère les utilisateurs administratifs de la plateforme Lolapp elle-même (accès back-office, etc.).

#### Champs clés

| Champ              | Description                                                              |
| ------------------ | ------------------------------------------------------------------------ |
| `id`               | Clé primaire, identifiant unique de l'administrateur.                    |
| `name`             | Nom de l'administrateur.                                                 |
| `email`            | Adresse email de connexion de l'administrateur.                          |
| `password`         | Mot de passe hashé de l'administrateur.                                  |
| `idpersonnel_lola` | Identifiant du personnel Lola associé (si l'admin est aussi un user Lola). |
| `customer_id`      | Identifiant de l'entreprise (table `customers`) si admin spécifique.     |
| `created_at`       | Date de création du compte administrateur.                               |
| `updated_at`       | Date de dernière mise à jour du compte administrateur.                   |

### 5.3 Table `drive_files`

Cette table liste tous les fichiers uploadés sur Minio depuis Lolapp, principalement les documents générés et signés.

#### Champs clés

| Champ             | Description                                      |
| ----------------- | ------------------------------------------------ |
| `file_name`       | Nom du fichier dans Minio                        |
| `display_name`    | Nom affiché à l’utilisateur                      |
| `customer_id`     | ID de l’entreprise concernée                     |
| `origin`          | Origine de l’upload (ex. `intervention_sign_pv`) |
| `client_id`       | ID du client associé                             |
| `project_id`      | ID du projet associé                             |
| `quote_id`        | ID du devis associé                              |
| `invoice_id`      | ID de la sous-facture associée                   |
| `intervention_id` | ID de l’intervention associée                    |
| `public`          | Non utilisé pour le moment                       |
| `portfolio`       | Non utilisé pour le moment                       |

👉 **API associée** : [drive.uploadFiles – documentation API](https://api.lola-france.fr/docs/api#/operations/drive.uploadFiles)

### 5.4 Table `personal_access_tokens`

Cette table gère l'**authentification** via des tokens (type Laravel Sanctum). Un token peut être associé à différents types d'utilisateurs.

#### Champs importants

- `tokenable_type` : définit le type d'utilisateur associé au token
- `tokenable_id` : identifiant de l'utilisateur

#### Valeurs possibles pour `tokenable_type`

| Valeur                      | Utilisation                                                    |
| --------------------------- | -------------------------------------------------------------- |
| `App\Models\Lola\Client`    | Connexion d'un client final via numéro de téléphone + OTP SMS  |
| `App\Models\Lola\Personnel` | Connexion d'un collaborateur Lola via email + mot de passe     |
| `App\Models\Laravel\User`   | Connexion d'un administrateur (actuellement limité à `id = 1`) |

## 6. Tables de la base Lola (HFSQL)

### 6.1 Table `PERSONNEL`

Cette table contient les informations sur les collaborateurs de l'entreprise utilisant Lola.

#### Champs clés

| Champ                | Description                                                     |
| -------------------- | --------------------------------------------------------------- |
| `IDPERSONNEL`        | Clé primaire, identifiant unique du personnel.                  |
| `MAIL`               | Adresse email du personnel (utilisée pour la connexion Lolapp). |
| `MOT_DE_PASSE`       | Mot de passe du personnel (géré par Lola).                      |
| `NOM`                | Nom de famille du personnel.                                    |
| `PRENOM`             | Prénom du personnel.                                            |
| `TELEPHONE_PORTABLE` | Numéro de téléphone portable du personnel.                      |
| `IDAGENCE`           | Identifiant de l'agence de rattachement.                        |
| `DATE_SORTIE`        | Date de départ du personnel (0 si toujours actif).              |
| `INACTIF`            | Indicateur d'inactivité (0 si actif, 1 si inactif).             |

### 6.2 Table `CLIENT`

Cette table stocke les informations relatives aux clients finaux de l'entreprise.

#### Champs clés

| Champ             | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `IDCLIENT`        | Clé primaire, identifiant unique du client.           |
| `NOM`             | Nom du client.                                        |
| `PRENOM`          | Prénom du client.                                     |
| `MAIL`            | Adresse email du client.                              |
| `PORTABLE`        | Numéro de téléphone portable principal du client.     |
| `IDADRESSE`       | Identifiant de l'adresse principale du client.        |
| `IDCIVILITE`      | Identifiant de la civilité du client (M., Mme, etc.). |
| `IDAGENCE`        | Identifiant de l'agence associée au client.           |
| `IDPERSONNEL`     | Identifiant du personnel référent pour ce client.     |
| `IDETAT_CLIENT`   | Identifiant de l'état actuel du client.               |
| `DATE_CREATION`   | Date de création de la fiche client.                  |
| `DATE_MODIF`      | Date de dernière modification de la fiche client.     |

### 6.3 Table `PROJET`

Cette table centralise les informations concernant les projets ou chantiers.

#### Champs clés

| Champ                 | Description                                                        |
| --------------------- | ------------------------------------------------------------------ |
| `IDPROJET`            | Clé primaire, identifiant unique du projet.                        |
| `NOMADE_NUMPROJET`    | Numéro de projet unique pour l'application nomade (généré).        |
| `IDCLIENT`            | Identifiant du client associé au projet.                           |
| `IDADRESSECHANTIER`   | Identifiant de l'adresse du chantier.                              |
| `IDADRESSEFACTURATION`| Identifiant de l'adresse de facturation.                           |
| `IDORIGINE`           | Identifiant de l'origine du projet (ex: recommandation, salon).    |
| `DESCRIPTION`         | Description détaillée du projet.                                   |
| `OBSERVATION`         | Observations générales sur le projet.                              |
| `IDRESPONSABLEPROJET` | Identifiant du personnel responsable du projet.                    |
| `IDAGENCE`            | Identifiant de l'agence gérant le projet.                          |
| `IDETAT_PROJET`       | Identifiant de l'état actuel du projet.                            |
| `DATE_CREATION`       | Date de création du projet.                                        |
| `DATE_MODIF`          | Date de dernière modification du projet.                           |

### 6.4 Table `DEVIS`

Cette table contient les informations sur les devis établis pour les projets.

#### Champs clés

| Champ              | Description                                                      |
| ------------------ | ---------------------------------------------------------------- |
| `IDDEVIS`          | Clé primaire, identifiant unique du devis.                       |
| `NOMADE_NUMDEVIS`  | Numéro de devis unique pour l'application nomade (généré).       |
| `IDPROJET`         | Identifiant du projet auquel le devis est rattaché.              |
| `IDCLIENT`         | Identifiant du client concerné par le devis.                     |
| `NUMERO_DEVIS`     | Numéro officiel du devis.                                        |
| `LIBELLE`          | Libellé ou titre du devis.                                       |
| `DATE_SIGNATURE`   | Date à laquelle le devis a été signé par le client.              |
| `PRIX_TTC`         | Montant total Toutes Taxes Comprises du devis.                   |
| `DEVIS_SIGNE`      | Indicateur booléen si le devis est signé (1) ou non (0).         |
| `DATE_CREATION`    | Date de création du devis.                                       |
| `TEXTE_DEVIS`      | Contenu textuel détaillé du devis (souvent au format RTF).       |
| `COMMENTAIRE`      | Commentaires additionnels sur le devis (souvent au format RTF).  |

## 7. Gestion des images (background, logo, entête)

Les images personnalisables utilisées dans l’application sont stockées dans **Minio**, un object storage (type S3) accessible à l’adresse :

🔗 **https://minio.solisws.fr**

### Fonctionnement

- Chaque client dispose de son propre **bucket**, nommé selon sa `apikey` dans la table customers (sans espaces ni underscores).
- Les images renseignées dans les champs `background`, `logo` et `entete` de la table `customers` doivent être déposées dans ce bucket.
  (Ces images s’affichent dans Lolapp (écran d’accueil, documents PV de réception, etc.).)

## 8. Modèles utilisés

> 🛠️ **Note** : certains modèles sont encore présents dans le code mais **ne sont plus utilisés** activement.

| Modèle                      | Description                                       | Statut      |
| --------------------------- | ------------------------------------------------- | ----------- |
| `App\Models\Lola\Client`    | Client final dans la base Lola                    | ✅ Utilisé  |
| `App\Models\Lola\Personnel` | Collaborateur d’entreprise (base Lola)            | ✅ Utilisé  |
| `App\Models\Customer`       | Représentait les entreprises clientes dans Lolapp | ❌ Obsolète |
| `App\Models\User`           | Compte admin Laravel pour Lolapp                  | ❌ Obsolète |

## 9. Connexion Utilisateur / Clients

![Séparation des chemins d'authentification](https://i.imgur.com/BO8KpTo.png)

Sur la plateforme beta.lola-france.fr, deux parcours distincts sont disponibles pour les utilisateurs : d'une part, le parcours client qui est destiné aux personnes souhaitant accéder aux services en tant que clients, et d'autre part, le parcours utilisateur qui s'adresse aux personnes ayant un rôle différent dans l'utilisation de la plateforme.

### 9.1 Connexion et fonctionnalités Client

Les **clients** accèdent à Lolapp via un processus d'authentification par SMS.

![Schéma processus client](https://i.imgur.com/ZkkvHnl.png)

#### Processus de connexion Client
1. **Saisie du numéro de téléphone** : Le client renseigne son numéro de téléphone portable
2. **Envoi du code** : Un code de vérification à usage unique est envoyé par SMS
3. **Validation du code** : Le client saisit le code reçu pour confirmer son identité
4. **Génération du token** : Un token d'accès est créé dans la table `personal_access_tokens` avec `tokenable_type = App\Models\Lola\Client` (pas sur)

#### Fonctionnalités accessibles aux Clients
Une fois authentifié, le client accède aux fonctionnalités suivantes :

- **📋 Liste des projets** : Consultation de ses projets en cours et terminés
- **💰 Espace comptable** : Suivi des devis, factures et paiements
- **🔧 Service après-vente** : Demandes d'intervention et suivi SAV
- **📅 Calendrier** : Visualisation des rendez-vous et interventions programmées
- **👥 Mon parrain** : Système de parrainage et recommandations

### 9.2 Connexion utilisateur

Les **utilisateurs** utilisent une authentification classique par email/mot de passe.

![Schéma processus utilisateur](https://i.imgur.com/v9uiTIu.png)

#### Processus de connexion Utilisateur
1. **Saisie des identifiants** : Email et mot de passe du collaborateur
2. **Vérification HFSQL** : Contrôle des identifiants dans la table `PERSONNEL` de la base Lola
3. **Génération du token** : Un token d'accès est créé avec `tokenable_type = App\Models\Lola\Personnel`

#### Fonctionnalités accessibles au Personnel
Une fois connecté, le personnel accède aux fonctionnalités métier :

- **📋 Mes affectations** : Liste des projets et interventions assignés
- **🏆 Classement** : Tableau de bord des performances et statistiques
- **🔍 Suivi des interventions** : Gestion et suivi des interventions terrain

## 10. Documentation automatique des API

### 10.1 Génération de la documentation

La documentation des endpoint API de Lolapp est générée automatiquement grâce à l'intégration d'**OpenAPI** et **Redocly**.

![Processus de génération de documentation](https://imgur.com/yg2vrxE.png)

#### Processus de documentation

1. **Définition OpenAPI** : Les endpoints de l'API Laravel sont documentés en utilisant la spécification OpenAPI 3.0
2. **Export** : Le schéma OpenAPI est généré et exporté depuis l'application Windev
3. **Déploiement Redocly** : Le fichier OpenAPI est envoyé vers Redocly qui génère une documentation interactive
4. **Publication** : La documentation est rendue accessible via une URL publique

#### Avantages de cette approche

- **📚 Documentation toujours à jour** : Synchronisation automatique avec les modifications du code
- **🎯 Interface interactive** : Possibilité de tester les endpoints directement depuis la documentation
- **🔍 Recherche intégrée** : Navigation facilitée dans l'API
- **📱 Responsive** : Documentation accessible sur tous les appareils
- **🌐 Partage facile** : URL unique pour accéder à la documentation complète

### 10.2 Accès à la documentation

La documentation complète de l'API est disponible à l'adresse : **[URL à fournir]**

## 11. Génération de PDF d'Intervention

### Endpoint

**POST** `/intervention/{id_intervention}/pdf`

### Description

Cet endpoint permet de générer un procès-verbal (PV) d'intervention au format PDF. Le processus implique une chaîne complète de traitement depuis l'interface Vue.js jusqu'au stockage sécurisé.

![Processus de génération de PDF d'intervention](https://imgur.com/qoY22lB.png)

### Processus technique détaillé

#### 1. Requête depuis Vue.js
L'utilisateur remplit le formulaire d'intervention dans l'interface Vue.js et soumet les données via une requête POST à l'endpoint `/intervention/{id_intervention}/pdf`.

#### 2. Traitement Laravel
Laravel reçoit la requête et :
- Valide les données reçues
- Récupère les informations de l'intervention depuis la base HFSQL
- Prépare les données pour la génération du PDF

#### 3. Génération PDF avec Express
Laravel fait appel à Express pour générer le PDF :
- Express utilise les données formatées par Laravel
- Le PDF est créé avec le template `intervention_pv.blade.php`
- Le document inclut les signatures, observations et données d'intervention

#### 4. Chiffrement et stockage dans Minio
Une fois le PDF généré :
- Il est uploadé dans Minio 
- Le nom du fichier est transformé en nom chiffré

#### 5. Enregistrement dans MySQL (table drive_files)
Les métadonnées du fichier sont sauvegardées dans la table `drive_files` :
- **`display_name`** : Nom original du fichier (ex: "PV_Intervention_123.pdf")
- **`file_name`** : Nom chiffré du fichier stocké dans Minio
- **`origin`** : "intervention_sign_pv"
- **`customer_id`** : ID de l'entreprise
- **`intervention_id`** : ID de l'intervention concernée

#### 6. Finalisation de l'intervention
Laravel met à jour l'intervention dans la base HFSQL :
- `PROCESVERBAL = 1`
- `FINALISEE = 1`

### Paramètres d'URL

| Paramètre | Type | Description |
|-----------|------|-------------|
| `id_intervention` | integer | ID unique de l'intervention |

## 12. Espace Client

L'espace client est une interface dédiée permettant aux clients d'accéder à leurs informations personnelles, projets, commandes et services. Il est accessible via une authentification sécurisée par SMS.


#### 1. Authentification
- **Contrôleur** : `SpaceController`
- **Méthode** : `authSpace`

Le processus d'authentification fonctionne ainsi :
- Le client s'authentifie avec son numéro de téléphone
- Validation des informations du client (code OTP envoyé par SMS)
- Génération d'un token d'authentification Laravel Sanctum si les informations sont correctes
- Le token sécurise toutes les requêtes ultérieures à l'API de l'espace client

#### Séquence des appels API

![Architecture logique et séquence des appels - Espace Client](https://imgur.com/bK3nijU.png)

Voici l'ordre chronologique des appels pour l'authentification et l'accès à l'espace client :

1. **POST** `/sms/otp` - Génère et envoie un code OTP au numéro du client
2. **POST** `/sms/otp/verify` - Vérifie que le code saisi par le client est correct
3. **POST** `/space/auth` - Génère le token d'accès à l'espace client si le code OTP est correct

Une fois authentifié, le client peut accéder aux endpoints suivants :

4. **GET** `/space/{idClient}/contact` - Récupère les informations du client, de l'agence, du commercial
5. **GET** `/space/{idClient}/project` - Liste tous les projets du client
6. **GET** `/space/{idClient}/project/{idProject}` - Liste les devis/commandes pour un projet donné
7. **GET** `/space/{idClient}/project/{idProject}/order/{idOrder}` - Détail d'une commande (devis signé)
8. **GET** `/space/{idClient}/project/{idProject}/quote/{idOrder}` - Détail d'un devis (en attente de signature)
9. **GET** `/space/{idClient}/factures` - Liste des factures signées du client
10. **GET** `/space/{idClient}/next-rdvs` - Prochains rendez-vous et interventions
11. **POST** `/space/mail/send` - Envoi d'un mail de confirmation

#### 2. Routage et Sécurité
**Fichier de routes** : `routes/api_client.php`

**Structure des routes** :
- Toutes les routes sont préfixées par `/space/{idClient}`
- Protection par un groupe de middlewares incluant `route.space`

**Middleware de sécurité** : `RouteSpaceMiddleware`
- Vérifie que le token d'authentification correspond au client (`idClient`) demandé
- Empêche un client d'accéder aux informations d'un autre client
- Assure l'isolation des données entre clients

#### 3. Récupération des données
**Contrôleur principal** : `SpaceController`

#### 4. Formatage de la réponse


Les données sont automatiquement formatées en JSON avant envoi au frontend Vue.js.

### Endpoints API de l'espace client

#### Génération du code OTP par SMS
**POST** `/sms/otp`

Génère et envoie un code OTP par SMS au numéro de téléphone du client.
- **Fonction** : Génération d'un code à usage unique et envoi par SMS
- **Paramètres** : Numéro de téléphone du client
- **Retour** : Confirmation d'envoi du SMS avec identifiant de session

#### Vérification du code OTP
**POST** `/sms/otp/verify`

Vérifie le code OTP saisi par l'utilisateur et authentifie le client.
- **Fonction** : Validation du code OTP et génération du token d'authentification
- **Paramètres** : Code OTP saisi par l'utilisateur et identifiant de session
- **Retour** : Token d'authentification si le code est valide

#### Authentification espace client (OTP)
**POST** `/space/auth`

Authentifie un client via un code OTP envoyé par SMS.
- **Fonction** : Validation du code OTP et génération du token Sanctum
- **Retour** : Token d'authentification pour les requêtes suivantes

#### Détails du client
**GET** `/space/{idClient}/contact`

Récupère les informations complètes du client.
- **Fonction** : Informations du client, de l'agence, du commercial associé
- **Sécurité** : Vérification que le token correspond à l'idClient
- **Resource** : `ClientResource`

#### Liste des projets
**GET** `/space/{idClient}/project`

Retourne tous les projets du client authentifié.
- **Fonction** : Liste complète des projets (en cours et terminés)
- **Filtrage** : Seuls les projets du client sont retournés
- **Resource** : `ProjetResource`

#### Liste des devis/commandes d'un projet
**GET** `/space/{idClient}/project/{idProject}`

Liste les devis et commandes pour un projet spécifique.
- **Fonction** : Tous les devis/commandes rattachés au projet
- **Contrôle** : Vérification que le projet appartient au client
- **Resource** : `QuoteResource`

#### Détail d'un devis/commande
**GET** `/space/{idClient}/project/{idProject}/order/{idOrder}`

Affiche le détail d'une commande (devis signé).
- **Fonction** : Informations détaillées de la commande
- **Statut** : Pour les devis signés uniquement
- **Resource** : `QuoteResource`

**GET** `/space/{idClient}/project/{idProject}/quote/{idOrder}`

Affiche le détail d'un devis (en attente de signature).
- **Fonction** : Équivalent à l'endpoint précédent
- **Statut** : Pour les devis non signés
- **Resource** : `QuoteResource`

#### Factures
**GET** `/space/{idClient}/factures`

Liste des factures signées du client.
- **Fonction** : Toutes les factures validées et signées
- **Filtrage** : Seules les factures du client connecté
- **Tri** : Par date décroissante

#### Prochains RDVs et interventions
**GET** `/space/{idClient}/next-rdvs`

Retourne les prochains rendez-vous et interventions programmés.
- **Fonction** : Événements à venir (RDV, interventions, livraisons)
- **Période** : Événements futurs uniquement
- **Tri** : Par date croissante

### Point d'entrée et authentification

#### 1. Page de connexion
**Composant** : `spaceLoginPage.vue`

La connexion se fait via un processus d'authentification sécurisé :
- Le client saisit son numéro de téléphone
- Un code OTP (One-Time Password) est envoyé par SMS
- Le client valide le code pour accéder à son espace

#### 2. Vérification de la clé API
**Composant** : `SpaceLayout.vue`

Le layout principal vérifie :
- La validité de la clé API de l'entreprise
- Les autorisations d'accès du client
- La configuration spécifique de l'espace client

### Pages principales de l'espace client

#### 1. Page d'accueil
**Composant** : `spaceHomePage.vue`

**Fonctionnalités** :
- **Informations commerciales** : Affichage des données du commercial et de l'agence via `AppSpaceUserInformation`
- **Liste des projets** : Consultation des projets du client avec navigation vers les détails
- **Calendrier intégré** : Visualisation des rendez-vous et interventions programmées
- **Cartes d'accès** : Navigation rapide vers les différents espaces spécialisés

#### 2. Page projet
**Composant** : `spaceProjectPage.vue`

**Fonctionnalités** :
- **Liste des commandes et devis** : Affichage pour un projet spécifique
- **Navigation détaillée** : Accès aux détails de chaque commande ou devis
- **Suivi d'avancement** : État des commandes et devis en cours

#### 3. Page commande/devis
**Composant** : `spaceOrderAndQuotePage.vue`

**Fonctionnalités conditionnelles** :
- **`AppSpaceOrderPage`** : Affichage si le devis est signé (commande confirmée)
- **`AppSpaceQuotePage`** : Affichage pour les devis en attente de signature
- **Actions disponibles** : Signature électronique, validation, commentaires

### Espaces spécialisés

#### 1. Espace Comptable
**Composant** : `spaceComptablePage.vue`

**Fonctionnalités** :
- **Gestion des factures** : Affichage via `AppSpaceFactures`
- **Historique des paiements** : Suivi des règlements et échéances
- **Documents comptables** : Téléchargement des factures et attestations

#### 2. Espace Après-vente
**Composant** : `spaceApresVentePage.vue`

**Fonctionnalités** :
- **Demandes d'intervention** : Création et suivi des demandes SAV
- **Historique des interventions** : Consultation des interventions passées
- **Garanties** : Suivi des garanties et contrats de maintenance

## 13. La situation actuelle - Problématiques et interrogations

Un nouveau développeur Full Stack a repris le projet de l'ancien développeur.

Il avait du mal a débugger / maintenir et créer de nouvelles fonctionnalités, les webservices n'étaient pas trés bien écrits et les retours json étaient incompréhensibles.

Aussi il a fait évoluer le projet Lolapp
Migration de HTTP/1 vers HTTP/2
Migration de PHP6 vers PHP8
Puis
Migration vuejs2 en vuejs3
Il a réécrit les endpoints directement dans Laravel.
Malheureusement la base de données des clients étants en HFSQL, il a été contraint de garder un webservice webdev et utilise un seul endpoint auquel il transmet l'ensemble des requetes faites au préalables via Laravel.
Tout cela semblait trop beau mais finalement, on s'est vite rendu compte qu'on perdait en performance.

Est ce que la solution ne serait pas plutot de déléguer à notre développeur Windev l'écriture et la maintenance des webservices en WEBDEV et le développeur Web n'aura qu'à les consommer ?

Si on réécrit les endpoints Laravel en webdev et qu'il sont strictement identiques.
Quels sont les changements que doit faire le développeur de Lolapp pour faire appel à ces nouveaux endpoint ?
Y a t'il beaucoup de travail ?