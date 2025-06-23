# Monitoring Uptime - Surveillance des Services

## Vue d'ensemble

Le système de monitoring Uptime permet de surveiller en temps réel la disponibilité et les performances des services critiques de l'infrastructure Lolapp.

## Accès au monitoring

🔗 [**Dashboard Uptime**](https://uptime.lola-france.fr/status/solis)

## Services surveillés

![Dashboard de monitoring des services - Vue d'ensemble des statuts de disponibilité](https://imgur.com/epCyW6p.png)

### 1. Webservice Lola France

**URL** : [https://ws-a.lola-france.fr/](https://ws-a.lola-france.fr/)

**Description** : Webservice principal Windev pour la nouvelle version

**Caractéristiques** :
- **Version** : WEBDEV 2025 Linux
- **Analyse** : Lola Version 2026.0
- **Compatibilité** : beta.lola-france.fr
- **Type** : Endpoint unique recevant les requêtes Laravel

### 2. Webservice Solisws

**URL** : [https://ws-a.solisws.fr](https://ws-a.solisws.fr)

**Description** : Webservice legacy pour l'ancienne version

**Caractéristiques** :
- **Version** : WEBDEV 28 Windows
- **Analyse** : Lola Version 2025.0
- **Compatibilité** : solisws.fr uniquement
- **Type** : Nombreux endpoints spécifiques

### 3. Application Solisws

**URL** : [https://solisws.fr/](https://solisws.fr/)

**Description** : Version legacy de l'application Lolapp

**Caractéristiques** :
- **Frontend** : Vue.js 2
- **Backend** : Laravel + Webservice Windev A
- **Base** : MySQL (Lolapp) + HFSQL (Lola)
- **Storage** : Minio S3

### 4. Frontend Lola France

**URL** : [https://admin.beta.lola-france.fr/](https://admin.beta.lola-france.fr/)

**Description** : Interface d'administration de la nouvelle version

**Caractéristiques** :
- **Frontend** : Vue.js 3
- **Fonction** : Administration et configuration
- **Accès** : Réservé aux administrateurs

### 5. API Backend Lola France

**URL** : [https://api.lola-france.fr/](https://api.lola-france.fr/)

**Description** : API REST pour la nouvelle version Lola France

**Caractéristiques** :
- **Backend** : Laravel
- **Type** : API REST moderne
- **Base** : MySQL (Lolapp) + HFSQL (Lola)

### 6. API Backend Solisws

**URL** : [https://api.solisws.fr/](https://api.solisws.fr/)

**Description** : API REST pour la version legacy Solisws

**Caractéristiques** :
- **Backend** : Laravel
- **Type** : API REST legacy
- **Base** : MySQL (Lolapp) + HFSQL (Lola)

## Architecture de monitoring

### Surveillance externe

- **Localisation** : Monitoring depuis plusieurs points géographiques
- **Fréquence** : Vérification toutes les 60 secondes
- **Protocoles** : HTTP/HTTPS

### Canaux d'alerte

- **Webhook** : Intégration avec outils tiers

Lien du Webhook : https://mattermost.solisws.fr/hooks/o9np599a6jniijdnbb4jj1q8oh

![Configuration webhook Mattermost - Interface de notification d'alertes](https://imgur.com/QNQPeaa.png)

ps : Le bot est un compte utilisateur qui a créer un webhook pour envoyer les alertes. 
- le mail : botmail@solis-system.com
- le mdp : 20252025!!


---

[← Problématiques](issues.md) | [Retour à l'index](./) | [Déploiement →](deployment.md)