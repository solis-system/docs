# Authentification Lolapp

## Vue d'ensemble

Lolapp propose deux parcours d'authentification distincts selon le type d'utilisateur :

![Séparation des chemins d'authentification](https://i.imgur.com/BO8KpTo.png)

## Authentification Client

### Processus de connexion

![Schéma processus client](https://i.imgur.com/ZkkvHnl.png)

#### Étapes d'authentification
1. **Saisie numéro** : Client renseigne son téléphone portable
2. **Envoi SMS** : Code OTP envoyé par SMS
3. **Validation code** : Client saisit le code reçu
4. **Génération token** : Token Laravel Sanctum créé

### Endpoints d'authentification client

#### Génération OTP
```http
POST /sms/otp
```
**Paramètres** :
- `phone` : Numéro de téléphone

**Réponse** :
- `session_id` : Identifiant de session OTP
- `message` : Confirmation envoi SMS

#### Vérification OTP
```http
POST /sms/otp/verify
```
**Paramètres** :
- `session_id` : ID session
- `code` : Code OTP saisi

**Réponse** :
- `valid` : Booléen de validation
- `client_id` : ID client si valide

#### Authentification finale
```http
POST /space/auth
```
**Paramètres** :
- `client_id` : ID client vérifié
- `session_id` : ID session OTP

**Réponse** :
- `token` : Token d'accès Sanctum
- `client` : Données client

### Fonctionnalités client authentifié

Une fois connecté, le client accède à :

- **📋 Projets** : Liste des projets en cours et terminés
- **💰 Comptabilité** : Devis, factures et paiements
- **🔧 SAV** : Service après-vente et interventions
- **📅 Planning** : Rendez-vous et interventions programmées
- **👥 Parrainage** : Système de recommandations

## Authentification Personnel

### Processus de connexion

![Schéma processus utilisateur](https://i.imgur.com/v9uiTIu.png)

#### Étapes d'authentification
1. **Saisie identifiants** : Email et mot de passe
2. **Vérification HFSQL** : Contrôle dans table `PERSONNEL`
3. **Génération token** : Token Sanctum avec type `Personnel`

### Endpoint d'authentification personnel

#### Connexion email/password
```http
POST /auth/personnel
```
**Paramètres** :
- `email` : Adresse email
- `password` : Mot de passe

**Réponse** :
- `token` : Token d'accès Sanctum
- `personnel` : Données personnel
- `permissions` : Droits utilisateur

### Fonctionnalités personnel authentifié

Le personnel accède aux outils métier :

- **📋 Affectations** : Projets et interventions assignés
- **🏆 Performances** : Tableaux de bord et statistiques
- **🔍 Interventions** : Gestion et suivi terrain
- **📊 Reporting** : Rapports d'activité

## Sécurité et tokens

### Middleware de sécurité

#### RouteSpaceMiddleware
- Vérifie correspondance token ↔ client
- Empêche accès cross-client
- Isolation des données par client

### Gestion des tokens

#### Table `personal_access_tokens`
```sql
tokenable_type = 'App\Models\Lola\Client'    -- Client final
tokenable_type = 'App\Models\Lola\Personnel' -- Collaborateur  
tokenable_type = 'App\Models\Laravel\User'   -- Admin
```

#### Expiration et renouvellement
- **Durée de vie** : Configurable par type d'utilisateur
- **Renouvellement** : Automatique via refresh token
- **Révocation** : Déconnexion manuelle ou expiration

## Différences entre versions

### Version legacy (solisws.fr)
- Authentification via table `users` MySQL
- Double validation : MySQL puis HFSQL
- Gestion utilisateurs dupliquée

### Nouvelle version (beta.lola-france.fr)
- Identification par sous-domaine
- Authentification directe HFSQL
- Gestion utilisateurs centralisée

## Contrôles de sécurité

### Validation côté client
- Format numéro de téléphone
- Validation email
- Complexité mot de passe (personnel)

### Validation côté serveur
- Vérification OTP avec expiration
- Contrôle existence utilisateur
- Validation permissions métier

### Protection CSRF
- Tokens CSRF Laravel
- Headers de sécurité
- Validation origine requêtes

---
[← Bases de données](./databases.md) | [Retour à l'index](./readme.md) | [API & Endpoints →](./api.md)
