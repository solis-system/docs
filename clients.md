# Gestion des Clients Lolapp

## Vue d'ensemble

L'API clients permet la gestion complète du cycle de vie des clients : recherche, création, mise à jour et suppression. Elle intègre une logique métier spécifique incluant la détection de doublons et la création automatique du premier projet.

## Endpoints principaux

### 1. Recherche de clients
```http
GET /clients
```

**Description** : Recherche des clients selon des critères spécifiques

**Paramètres de requête** :
- `NOM` : string, max 100 caractères (requis)
- `PRENOM` : string, max 100 caractères (optionnel)
- `VILLE` : string, max 100 caractères (optionnel)

**Limites** :
- Maximum 15 résultats
- Relations chargées : `adresse`

**Exemples d'URLs** :
```
GET /clients?NOM=Dupont
GET /clients?NOM=Dupont&PRENOM=Jean
GET /clients?NOM=Dupont&PRENOM=Jean&VILLE=Paris
GET /clients?NOM=Martin&VILLE=Lyon
```

### 2. Détails d'un client
```http
GET /clients/{id}
```

**Description** : Retourne les informations détaillées d'un client

**Paramètre** :
- `id` : Identifiant unique du client (IDCLIENT)

**Relations chargées** :
- `adresse` : Adresse principale du client
- `projets.adresse_chantier` : Projets avec adresses de chantier

**Exemples d'URLs** :
```
GET /clients/12345
GET /clients/67890
GET /clients/54321
```

### 3. Création d'un client
```http
POST /clients
```

**Description** : Créer un nouveau client avec son premier projet

**Validation** : `ClientStoreRequest`

**Fonctionnalités spéciales** :
- **Détection de doublons** : Vérification par email et téléphone
- **Création automatique** : Premier projet généré automatiquement
- **Gestion d'adresses** : Client, chantier et facturation
- **Support produits** : `TYPE_PRODUIT_GENERIQUE_IDS`

### 4. Mise à jour d'un client
```http
PUT /clients/{id}
PATCH /clients/{id}
```

**Description** : Met à jour les informations d'un client existant

### 5. Suppression d'un client
```http
DELETE /clients/{id}
```

**Description** : Supprime un client

**Exemple de requête** :
```http
DELETE /clients/12346
```

## Endpoints spécialisés

### Clients géolocalisés
```http
GET /clients/arround
```

**Description** : Recherche des clients autour d'un point géographique

**Paramètres requis** :
- `LATITUDE` : Latitude du point de recherche
- `LONGITUDE` : Longitude du point de recherche  
- `RAYON` : Rayon de recherche en kilomètres

**Filtres appliqués** :
- `IDETAT_CLIENT > 4` : Clients actifs uniquement
- Limite : 4000 clients maximum

**Exemples d'URLs** :
```
GET /clients/arround?LATITUDE=48.8566&LONGITUDE=2.3522&RAYON=10
GET /clients/arround?LATITUDE=45.7640&LONGITUDE=4.8357&RAYON=5
GET /clients/arround?LATITUDE=43.2965&LONGITUDE=5.3698&RAYON=15
GET /clients/arround?LATITUDE=50.6292&LONGITUDE=3.0573&RAYON=20
``

### Import IA
```http
POST /webhook/contact/create
```

**Description** : Endpoint d'import automatique via webservice Lola

**Usage** : Intégration avec systèmes externes et IA

---
[← Espace client](./client-space.md) | [Retour à l'index](./readme.md) | [Problématiques →](./issues.md)
