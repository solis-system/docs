# Monitoring Uptime - Surveillance des Services

## Vue d'ensemble

Le système de monitoring Uptime permet de surveiller en temps réel la disponibilité et les performances des services critiques de l'infrastructure Lolapp.

## Accès au monitoring

🔗 [**Dashboard Uptime**](https://uptime.lola-france.fr/status/solis)

## Services surveillés

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

### 4. Administration Lola France

**URL** : [https://admin.beta.lola-france.fr/](https://admin.beta.lola-france.fr/)

**Description** : Interface d'administration de la nouvelle version

**Caractéristiques** :
- **Frontend** : Vue.js 3
- **Backend** : Laravel + Webservice Windev B
- **Fonction** : Administration et configuration
- **Accès** : Réservé aux administrateurs

## Architecture de monitoring

### Surveillance externe

- **Localisation** : Monitoring depuis plusieurs points géographiques
- **Fréquence** : Vérification toutes les 60 secondes
- **Protocoles** : HTTP/HTTPS

### Canaux d'alerte

- **Webhook** : Intégration avec outils tiers

---

[← Problématiques](issues.md) | [Retour à l'index](./) | [Déploiement →](deployment.md)