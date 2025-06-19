# API & Endpoints Lolapp

## Documentation automatique

### Génération de la documentation

![Processus de génération de documentation](https://imgur.com/yg2vrxE.png)

#### Processus
1. **Définition OpenAPI** : Endpoints documentés avec OpenAPI 3.0
2. **Export** : Schéma généré depuis Windev
3. **Déploiement Redocly** : Documentation interactive
4. **Publication** : URL publique accessible

#### Avantages
- 📚 **Toujours à jour** : Synchronisation automatique
- 🎯 **Interface interactive** : Tests directs des endpoints
- 🔍 **Recherche intégrée** : Navigation facilitée
- 📱 **Responsive** : Accessible sur tous appareils

### Accès à la documentation
🔗 **[Documentation API complète](https://api.lola-france.fr/docs/api)**

## Génération de PDF d'intervention

### Endpoint principal
```http
POST /intervention/{id_intervention}/pdf
```

### Processus complet

![Processus de génération de PDF d'intervention](https://imgur.com/qoY22lB.png)

#### Étapes détaillées

1. **Requête Vue.js** : Soumission formulaire intervention
2. **Traitement Laravel** : Validation et récupération données HFSQL
3. **Génération PDF Express** : Création avec template `intervention_pv.blade.php`
4. **Stockage Minio** : Upload et chiffrement du fichier
5. **Enregistrement MySQL** : Métadonnées dans `drive_files`
6. **Finalisation** : Mise à jour intervention HFSQL

#### Paramètres
| Paramètre | Type | Description |
|-----------|------|-------------|
| `id_intervention` | integer | ID unique intervention |

#### Données générées
- **`PROCESVERBAL = 1`** : PV généré
- **`FINALISEE = 1`** : Intervention finalisée
- **Fichier PDF** : Stocké dans Minio avec nom chiffré

## API Espace Client

### Architecture des appels

![Architecture logique et séquence des appels - Espace Client](https://imgur.com/bK3nijU.png)

### Séquence d'authentification

1. **POST** `/sms/otp` - Génération et envoi code OTP
2. **POST** `/sms/otp/verify` - Vérification code OTP
3. **POST** `/space/auth` - Génération token d'accès

### Endpoints protégés (authentification requise)

#### Informations client
```http
GET /space/{idClient}/contact
```
**Réponse** : Données client, agence, commercial
**Resource** : `ClientResource`

#### Projets
```http
GET /space/{idClient}/project
```
**Réponse** : Liste complète des projets
**Resource** : `ProjetResource`

```http
GET /space/{idClient}/project/{idProject}
```
**Réponse** : Devis/commandes pour un projet
**Resource** : `QuoteResource`

#### Devis et commandes
```http
GET /space/{idClient}/project/{idProject}/order/{idOrder}
GET /space/{idClient}/project/{idProject}/quote/{idOrder}  
```
**Réponse** : Détail devis/commande
**Resource** : `QuoteResource`

#### Factures
```http
GET /space/{idClient}/factures
```
**Réponse** : Factures signées du client
**Tri** : Date décroissante

#### Planning
```http
GET /space/{idClient}/next-rdvs
```
**Réponse** : Prochains RDV et interventions
**Tri** : Date croissante

#### Communication
```http
POST /space/mail/send
```
**Fonction** : Envoi email de confirmation

### Sécurité API

#### Routes protégées
- **Préfixe** : `/space/{idClient}`
- **Middleware** : `route.space`
- **Contrôle** : Token ↔ idClient

#### Validation
- Correspondance token/client
- Isolation des données
- Permissions métier

## API Drive (Fichiers)

### Upload de fichiers
```http
POST /drive/upload
```
**Fonction** : Upload fichiers vers Minio
**Paramètres** :
- `files[]` : Fichiers à uploader
- `origin` : Origine de l'upload
- `client_id` : ID client associé

**Table associée** : `drive_files`

### Types d'origine (`origin`)
| Valeur | Description |
|--------|-------------|
| `intervention_sign_pv` | PV d'intervention signé |
| `quote_signature` | Signature de devis |
| `client_document` | Document client |

## Endpoints métier

### Interventions
```http
GET /interventions/{idPersonnel}
POST /intervention/{id}/finalize
PUT /intervention/{id}/update
```

## Codes de réponse

### Succès
- **200** : OK - Requête réussie
- **201** : Created - Ressource créée
- **204** : No Content - Suppression réussie

### Erreurs client
- **400** : Bad Request - Paramètres invalides
- **401** : Unauthorized - Token manquant/invalide
- **403** : Forbidden - Permissions insuffisantes
- **404** : Not Found - Ressource introuvable

### Erreurs serveur
- **500** : Internal Server Error - Erreur serveur
- **503** : Service Unavailable - Service temporairement indisponible

---
[← Authentification](./authentication.md) | [Retour à l'index](./readme.md) | [Espace client →](./client-space.md)
