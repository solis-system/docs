# Endpoint Ranking - Classement des Devis

## Vue d'ensemble

L'endpoint `/ranking` permet de récupérer un classement des devis (signés et refusés) sur une période donnée. Il est principalement utilisé pour analyser les performances commerciales d'une entreprise.

## Informations générales

| Propriété   | Valeur     |
| ----------- | ---------- |
| **URL**     | `/ranking` |
| **Méthode** | `GET`      |

## Paramètres de requête

| Paramètre    | Type | Obligatoire | Description                                      |
| ------------ | ---- | ----------- | ------------------------------------------------ |
| `DATE_DEBUT` | date | ✅           | Date de début de la période (format: YYYY-MM-DD) |
| `DATE_FIN`   | date | ✅           | Date de fin de la période (format: YYYY-MM-DD)   |

## Exemple de requête

```http
GET /ranking?DATE_DEBUT=2025-01-01&DATE_FIN=2025-06-19
```

## Logique métier

### Filtrage des données

L'endpoint récupère les devis selon les critères suivants :

* **Date de signature** comprise entre `DATE_DEBUT` et `DATE_FIN`

## Utilisation dans l'application

Cet endpoint est également utilisé dans le `QuoteController` avec le paramètre `TYPE === 'ranking'` pour des besoins de filtrage similaires.

## Notes techniques

### Traitement des dates

* Les dates sont automatiquement converties au format `Ymd` (ex: 20250619) pour la compatibilité avec la base de données
* Validation automatique du format de date côté Laravel

### Calculs automatiques

* Le calcul du `NET_HT` est effectué automatiquement dans la ressource
* Tous les montants sont retournés avec précision décimale

***

[← Système de types devis](devis-types.md) | [Retour à l'index](README.md) | [Espace client →](client-space.md)
