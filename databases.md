# Bases de données Lolapp

## Architecture des bases

![Séparation des bases de données](https://imgur.com/v7dRqtB.png)

Lolapp utilise deux types de bases de données :

* **MySQL/MariaDB** : Gestion de l'application Lolapp

![Architecture Mysql Lolapp](https://imgur.com/HI71rQe.png)

* **HFSQL** : Données métier des clients (base Lola)

![Architecture HFSQL (lola)](https://imgur.com/Eu3bVcr.png)

## Base Lolapp (MySQL/MariaDB)

### Table `customers` (Modèle `Company`)

Configuration des entreprises clientes.

| Champ             | Type   | Description                                                         |
| ----------------- | ------ | ------------------------------------------------------------------- |
| `key`             | string | Sous-domaine PWA (ex: `sm30` → `https://sm30.beta.lola-france.fr/`) |
| `database_server` | string | IP publique du serveur HFSQL client                                 |
| `database_name`   | string | Nom base HFSQL (défaut = `Données`)                                 |
| `webservice`      | char   | `A` = Windev 28 / `B` = Windev 2025                                 |
| `apikey`          | string | Clé de licence identique à Lola                                     |
| `color`           | string | Code couleur HEX interface (ex: `#FF5733`)                          |
| `background`      | string | Image de fond Lolapp                                                |
| `logo`            | string | Logo affiché                                                        |
| `entete`          | string | Image entête documents                                              |

### Table `admin` (Modèle `User`)

Utilisateurs administratifs de la plateforme.

| Champ              | Type   | Description                       |
| ------------------ | ------ | --------------------------------- |
| `id`               | int    | Clé primaire                      |
| `name`             | string | Nom administrateur                |
| `email`            | string | Email de connexion                |
| `password`         | string | Mot de passe hashé                |
| `idpersonnel_lola` | int    | ID personnel Lola associé         |
| `customer_id`      | int    | ID entreprise si admin spécifique |

### Table `drive_files`

Fichiers uploadés dans Minio ([voir endpoints et process](./drives_files.md)).

| Champ             | Type   | Description                                 |
| ----------------- | ------ | ------------------------------------------- |
| `file_name`       | string | Nom fichier dans Minio                      |
| `display_name`    | string | Nom affiché utilisateur                     |
| `customer_id`     | int    | ID entreprise                               |
| `origin`          | string | Origine upload (ex: `intervention_sign_pv`) |
| `client_id`       | int    | ID client associé                           |
| `project_id`      | int    | ID projet associé                           |
| `quote_id`        | int    | ID devis associé                            |
| `intervention_id` | int    | ID intervention associée                    |

### Table `personal_access_tokens`

Gestion authentification Laravel Sanctum.

| Champ            | Type   | Description      |
| ---------------- | ------ | ---------------- |
| `tokenable_type` | string | Type utilisateur |
| `tokenable_id`   | int    | ID utilisateur   |
| `token`          | string | Token chiffré    |

#### Types d'utilisateurs (`tokenable_type`)

| Valeur                      | Usage                            |
| --------------------------- | -------------------------------- |
| `App\Models\Lola\Client`    | Client final (SMS + OTP)         |
| `App\Models\Lola\Personnel` | Collaborateur (email + password) |
| `App\Models\Laravel\User`   | Admin plateforme                 |

## Base Lola (HFSQL)

### Table `PERSONNEL`

Collaborateurs de l'entreprise.

| Champ                | Type    | Description              |
| -------------------- | ------- | ------------------------ |
| `IDPERSONNEL`        | int     | Clé primaire             |
| `MAIL`               | string  | Email connexion Lolapp   |
| `MOT_DE_PASSE`       | string  | Mot de passe             |
| `NOM`                | string  | Nom famille              |
| `PRENOM`             | string  | Prénom                   |
| `TELEPHONE_PORTABLE` | string  | Numéro portable          |
| `IDAGENCE`           | int     | ID agence rattachement   |
| `DATE_SORTIE`        | date    | Date départ (0 si actif) |
| `INACTIF`            | boolean | Statut activité          |

### Table `CLIENT`

Clients finaux de l'entreprise.

| Champ           | Type   | Description               |
| --------------- | ------ | ------------------------- |
| `IDCLIENT`      | int    | Clé primaire              |
| `NOM`           | string | Nom client                |
| `PRENOM`        | string | Prénom client             |
| `MAIL`          | string | Email client              |
| `PORTABLE`      | string | Numéro portable principal |
| `IDADRESSE`     | int    | ID adresse principale     |
| `IDPERSONNEL`   | int    | ID commercial référent    |
| `IDETAT_CLIENT` | int    | ID état client            |

### Table `PROJET`

Projets ou chantiers.

| Champ                 | Type   | Description                 |
| --------------------- | ------ | --------------------------- |
| `IDPROJET`            | int    | Clé primaire                |
| `NOMADE_NUMPROJET`    | string | Numéro projet unique nomade |
| `IDCLIENT`            | int    | ID client associé           |
| `IDADRESSECHANTIER`   | int    | ID adresse chantier         |
| `DESCRIPTION`         | text   | Description détaillée       |
| `IDRESPONSABLEPROJET` | int    | ID responsable projet       |
| `IDETAT_PROJET`       | int    | ID état projet              |

### Table `DEVIS`

Devis établis pour les projets.

| Champ             | Type    | Description                |
| ----------------- | ------- | -------------------------- |
| `IDDEVIS`         | int     | Clé primaire               |
| `NOMADE_NUMDEVIS` | string  | Numéro devis unique nomade |
| `IDPROJET`        | int     | ID projet rattaché         |
| `NUMERO_DEVIS`    | string  | Numéro officiel devis      |
| `DATE_SIGNATURE`  | date    | Date signature client      |
| `PRIX_TTC`        | decimal | Montant TTC                |
| `DEVIS_SIGNE`     | boolean | Statut signature           |
| `TEXTE_DEVIS`     | text    | Contenu détaillé (RTF)     |

## Modèles utilisés

| Modèle                      | Description               | Statut     |
| --------------------------- | ------------------------- | ---------- |
| `App\Models\Lola\Client`    | Client final base Lola    | ✅ Actif    |
| `App\Models\Lola\Personnel` | Collaborateur base Lola   | ✅ Actif    |
| `App\Models\Customer`       | Entreprise cliente Lolapp | ❌ Obsolète |
| `App\Models\User`           | Admin Laravel Lolapp      | ❌ Obsolète |

***

[← Architecture](architecture.md) | [Retour à l'index](./) | [Authentification →](authentication.md)
