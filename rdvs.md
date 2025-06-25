# Gestion des rendez-vous Lolapp

## Vue d'ensemble

Le système de rendez-vous permet aux utilisateurs de visualiser et gérer leur planning via un calendrier.

## Endpoints des rendez-vous

### Récupération de tous les RDV (rdvs.index)

```http
GET /rdvs
```

**Description** : Récupère tous les rendez-vous pour l'utilisateur connecté

![Affichage de la page /rdvs](https://imgur.com/5uuf2P7.png)

**Paramètres optionnels** :

* `RDV_DEBUT` : Date de début de la période de recherche (format URL encodé)
* `RDV_FIN` : Date de fin de la période de recherche (format URL encodé)

**Exemple** :

```http
GET /rdvs?RDV_DEBUT=2025-06-16%2000%3A00&RDV_FIN=2025-06-23%2000%3A00
```

_Récupère tous les rendez-vous entre le 16 juin 2025 00:00 et le 23 juin 2025 00:00_

### Détails d'un RDV spécifique (rdvs.show)

```http
GET /rdvs/{id}
```

**Description** : Récupère les détails complets d'un rendez-vous quand l'utilisateur clique sur un élément du calendrier

![Affichage de la page /rdvs/{id}](https://imgur.com/wH8o6am.png)

**Paramètres** :

* `id` : Identifiant unique du rendez-vous

**Exemple** :

```http
GET /rdvs/30543
```

_Récupère les détails du rendez-vous avec l'ID 30543_

***

[← API & Endpoints](api.md) | [Retour à l'index](README.md) | [Espace client →](client-space.md)
