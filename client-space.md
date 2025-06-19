# Espace Client Lolapp

## Vue d'ensemble

L'espace client est une interface dédiée permettant aux clients d'accéder à leurs informations personnelles, projets, commandes et services via une authentification sécurisée par SMS.

## Architecture des composants

### Point d'entrée

#### Page de connexion
**Composant** : `spaceLoginPage.vue`

**Processus** :
1. Saisie du numéro de téléphone
2. Envoi code OTP par SMS
3. Validation du code
4. Accès à l'espace personnel

#### Layout principal
**Composant** : `SpaceLayout.vue`

**Vérifications** :
- Validité de la clé API entreprise
- Autorisations d'accès client
- Configuration spécifique espace

## Pages principales

### 1. Page d'accueil
**Composant** : `spaceHomePage.vue`

**Fonctionnalités** :
- **Informations commerciales** : `AppSpaceUserInformation`
  - Données commercial et agence
  - Contact direct
- **Liste des projets** : Navigation vers détails
- **Calendrier intégré** : RDV et interventions
- **Cartes d'accès** : Navigation rapide

### 2. Page projet
**Composant** : `spaceProjectPage.vue`

**Fonctionnalités** :
- Liste des commandes et devis par projet
- Navigation détaillée vers chaque élément
- Suivi d'avancement des commandes
- États des devis (en attente/signé)

### 3. Page commande/devis
**Composant** : `spaceOrderAndQuotePage.vue`

**Affichage conditionnel** :
- **`AppSpaceOrderPage`** : Si devis signé (commande)
- **`AppSpaceQuotePage`** : Si devis en attente

**Actions disponibles** :
- Signature électronique
- Validation et commentaires
- Téléchargement documents

## Espaces spécialisés

### 1. Espace Comptable
**Composant** : `spaceComptablePage.vue`

**Module** : `AppSpaceFactures`

**Fonctionnalités** :
- **Gestion des factures** : Liste et détails
- **Historique paiements** : Suivi règlements
- **Documents comptables** : Téléchargement attestations
- **Échéances** : Rappels et alertes

### 2. Espace Après-vente
**Composant** : `spaceApresVentePage.vue`

**Fonctionnalités** :
- **Demandes d'intervention** : Création et suivi SAV
- **Historique interventions** : Consultations passées
- **Garanties** : Suivi contrats maintenance
- **Tickets support** : Communication technique

## Sécurité et contrôles

### Contrôleur principal
**Contrôleur** : `SpaceController`

**Méthodes clés** :
- `authSpace` : Authentification client
- `getContact` : Informations client
- `getProjects` : Liste projets
- `getQuotes` : Devis et commandes

### Middleware de sécurité
**Middleware** : `RouteSpaceMiddleware`

**Contrôles** :
- Correspondance token ↔ client
- Isolation des données clients
- Vérification permissions

### Routing sécurisé
**Fichier** : `routes/api_client.php`

**Structure** :
- Préfixe : `/space/{idClient}`
- Groupe protégé : `route.space`
- Validation : idClient dans token

## Fonctionnalités par type d'utilisateur

### Client authentifié

#### 📋 Gestion de projets
- **Consultation** : Projets en cours et terminés
- **Navigation** : Accès aux détails par projet
- **Suivi** : États d'avancement en temps réel

#### 💰 Suivi comptable
- **Devis** : Consultation et signature électronique
- **Factures** : Historique et téléchargements
- **Paiements** : Suivi des règlements

#### 🔧 Service après-vente
- **Demandes** : Création tickets d'intervention
- **Planification** : Prise de RDV en ligne
- **Historique** : Consultations interventions passées

#### 📅 Planning personnel
- **Rendez-vous** : Visualisation des RDV programmés
- **Interventions** : Dates et créneaux
- **Notifications** : Rappels automatiques

#### 👥 Parrainage
- **Recommandations** : Système de parrainage
- **Récompenses** : Suivi des avantages
- **Partage** : Outils de recommandation

## Personnalisation par entreprise

### Configuration visuelle
- **Couleurs** : Thème selon `color` table customers
- **Logo** : Affichage logo entreprise
- **Background** : Image de fond personnalisée
- **Entête** : Documents avec entête spécifique

### Configuration fonctionnelle
- **Modules** : Activation/désactivation par client
- **Permissions** : Droits d'accès spécifiques
- **Workflow** : Processus métier personnalisés

## Responsive et PWA

### Progressive Web App
- **Installation** : App installable sur mobile
- **Offline** : Fonctionnement hors connexion partiel
- **Notifications** : Push notifications
- **Cache** : Mise en cache des données critiques

### Responsive design
- **Mobile first** : Interface optimisée smartphone
- **Tablette** : Adaptation écrans moyens
- **Desktop** : Version complète grand écran

## Intégrations externes

### Services SMS
- **OTP** : Codes de vérification
- **Notifications** : Alertes importantes
- **Confirmations** : Validations d'actions

### Email
- **Confirmations** : Actions importantes
- **Rapports** : Envois périodiques
- **Alertes** : Notifications critiques

### Stockage Minio
- **Documents** : PV, factures, contrats
- **Images** : Personnalisation interface
- **Backup** : Sauvegarde automatique

---
[← API & Endpoints](./api.md) | [Retour à l'index](./readme.md) | [Problématiques →](./issues.md)
