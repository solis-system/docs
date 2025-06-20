# Documentation Lolapp

**Lolapp** est une Progressive Web App (PWA) destinée aux clients professionnels utilisant le logiciel **Lola**. Elle permet d'accéder à distance aux fonctionnalités et données de Lola via une interface moderne et responsive.

## 📚 Navigation de la documentation

| Document | Description |
|----------|-------------|
| [Architecture technique](./architecture.md) | Stack technique, webservices et connecteurs |
| [Bases de données](./databases.md) | Structure des tables MySQL et HFSQL |
| [Authentification](./authentication.md) | Systèmes de connexion clients et personnel |
| [API & Endpoints](./api.md) | Documentation des API et webservices |
| [Gestion des clients](./clients.md) | API clients : CRUD, recherche et géolocalisation |
| [Gestion des rendez-vous](./rdvs.md) | Affichage des RDVs |
| [Système de types devis](./devis-types.md) | Filtrage et segmentation des devis |
| [Endpoint Ranking](./ranking.md) | Classement et analyse des performances commerciales |
| [Espace client](./client-space.md) | Fonctionnalités de l'espace client |
| [Problématiques actuelles](./issues.md) | Défis techniques et solutions proposées |

## 🚀 Démarrage rapide

### Environnements

- **Production legacy** : [solisws.fr](https://solisws.fr) (Vue.js 2)
- **Nouvelle version** : [beta.lola-france.fr](https://beta.lola-france.fr) (Vue.js 3)

### Stack technique

#### Version legacy (solisws.fr)
- Frontend : Vue.js 2
- Backend : Laravel + Webservice Windev A
- Base : MySQL + HFSQL
- Storage : Minio S3

#### Nouvelle version (beta.lola-france.fr)
- Frontend : Vue.js 3
- Backend : Laravel + Webservice Windev B
- Base : MySQL + HFSQL
- Storage : Minio S3

## 🔑 Authentification

Deux types d'utilisateurs avec des parcours distincts :

### Clients
- Authentification par SMS (code OTP)
- Accès aux projets, devis, factures
- Suivi des interventions

### Personnel
- Authentification email/mot de passe
- Gestion des interventions
- Tableaux de bord métier

## 📊 Architecture générale

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Frontend      │    │    Backend       │    │   Base Lola     │
│   Vue.js 3      │◄───┤   Laravel        │◄───┤   HFSQL         │
│   (PWA)         │    │   + Webservice   │    │   (Client)      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌──────────────────┐
                       │   Base Lolapp    │
                       │   MySQL          │
                       └──────────────────┘
```

## 🛠️ Développement

### Prérequis
- PHP 8+
- Node.js 18+
- Laravel 10+
- Vue.js 3
- Accès aux bases HFSQL clients

---

**🔗 Liens utiles**
- [API Documentation](https://api.lola-france.fr/docs/api)
- [Minio Storage](https://minio.solisws.fr)
- Support technique : [contact@lola-france.fr](mailto:contact@lola-france.fr)

## 📝 Historique

| Version | Date | Modifications |
|---------|------|---------------|
| 1.0 | 2025-06-18 | Documentation initiale restructurée |
| 1.1 | 2025-06-19 | Ducmention sur client / devis / datacache / ranking |