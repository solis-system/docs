# DataCache - Système de Cache des Données de Référence

## Vue d'ensemble

Le DataCache est un système optimisé de mise en cache qui permet de récupérer en une seule requête toutes les données de référence nécessaires au fonctionnement de l'application. Il constitue l'épine dorsale informationnelle garantissant des performances optimales et une cohérence des données.

## Backend Laravel

### Endpoint

```
GET /datacache
```

### Authentification

Cet endpoint nécessite une authentification et fait partie des routes privées de l'entreprise.

### Réponse

L'endpoint retourne un objet JSON contenant toutes les données de référence mises en cache.

#### Données conditionnelles

Pour les entreprises ayant `webservice === "B"`, des données supplémentaires sont incluses :

- **RDV_DEBRIEF** - Données de débrief des rendez-vous
- **ORIGINE_CAMPAGNE** - Données supplémentaires des campagnes d'origine

### Fonctionnalités

#### Système de cache intelligent
- **Mise en cache automatique** : Les données sont automatiquement mises en cache lors de la première requête
- **Cache par entreprise** : Chaque entreprise dispose de son propre cache isolé grâce au système de tags
- **Invalidation automatique** : Le cache est automatiquement invalidé lors des modifications des données source

#### Optimisation des performances
- **Requête unique** : Toutes les données de référence sont récupérées en un seul appel
- **Réduction de la latence** : Évite les multiples roundtrips vers la base de données
- **Scalabilité** : Support de différents drivers de cache (Redis, File, Database)

### Utilisation typique

Cet endpoint est généralement appelé :
- Au démarrage de l'application frontend
- Après une connexion utilisateur
- Lors du rafraîchissement des données de référence

## Frontend Vue.js

### Endpoint principal

```
GET /datacache
```

Récupération de toutes les données de cache en une seule requête.

### Données disponibles

#### Rendez-vous et Nature
- **Nature des RDV (NATURE_RDV)** - Types de rendez-vous avec codes couleur
- **Catégories de débrief** - Statuts de suivi client (Non débrief, Non entrée, Non déballe, Non vendu, Vendu)

#### Données clients
- **Civilités (CIVILITE)** - M., Mme, Dr., etc. (avec filtrage particuliers/professionnels)
- **Origines (ORIGINE)** - Sources d'acquisition client
- **Campagnes marketing (ORIGINE_CAMPAGNE)** - Campagnes groupées par origine

#### Paramètres métier
- **Taux de TVA (TVA)** - Taux applicables
- **Agences (AGENCES)** - Succursales et points de vente
- **Types d'annulation (TYPE_ANNULATION)** - Motifs de sans-suite projet
- **Types d'intervention (TYPE_INTERVENTION)** - Catégories techniques
- **Tailles compartiments (BUCKET_SIZE)** - Configuration stockage

#### Données avancées (webservice "B")
- **Débrief RDV (RDV_DEBRIEF)** - Données étendues de débrief des rendez-vous
- **Campagnes étendues (ORIGINE_CAMPAGNE)** - Informations supplémentaires des campagnes marketing

### Architecture technique

#### Service API
Interface de communication avec le backend Laravel.

#### Store Pinia
Le `useDataCacheStore` centralise la gestion avec :

##### État
Stockage réactif des données de référence.

##### Getters optimisés
- **Index par ID** : `natureRdvById`, `origineById`, `typeAnnulationById`
- **Filtres spécialisés** : `civiliteParticulier`, `orgineCampagneByIdOrigine`
- **Structures enrichies** : `rdvDebrief` avec catégories et couleurs

### Utilisation dans l'application

- **Formulaires et listes déroulantes** : Alimentation des champs de sélection
- **Affichage et validation** : Contrôle de cohérence des données
- **Logique métier** : Règles et calculs basés sur les données de référence

### Fonctionnalités avancées

- **Indexation automatique** via `mapById()` pour un accès rapide
- **Groupement intelligent** via `groupById()` pour organiser les données
- **Persistance locale** via plugin IndexedDB
- **Intégration entreprise** - Mise à jour automatique du store entreprise