# Gestion des Devis - Endpoints et Types

## Endpoints des Devis

### 1. Lister les devis
```http
GET /devis
```

**Paramètres de requête** :
- `TYPE` : Type de devis (`attente`, `ranking`, ou par défaut)
- `ALL` : Pour voir tous les devis (sinon filtrés par responsable de projet)
- `start` et `end` : Dates de début et fin pour filtrer

**Logique** :
- Par défaut, filtre les devis par responsable de projet de l'utilisateur connecté
- Pour `TYPE=attente` : devis non refusés, non signés, avec prix > 0
- Pour `TYPE=ranking` : devis signés ou refusés dans la période donnée
- Sinon : devis créés dans la période, triés par date de création

### 2. Afficher un devis
```http
GET /devis/{id}
```

**Retour** : Détails d'un devis avec ses relations (projet, client, lignes de devis)

### 3. Créer un devis
```http
POST /devis
```

Crée un nouveau devis avec la date de création automatique.

### 4. Mettre à jour un devis
```http
PUT /devis/{id}
```
Met à jour uniquement :
- `INDICE_CONFIANCE`
- `COMMENTAIRE`

### 5. Supprimer/Refuser un devis
```http
DELETE /devis/{id}
```

Ne supprime pas physiquement le devis mais marque `REFUSE = 1`.

## Modèles et Resources

### Modèle principal : `Devis`
- **Table** : `DEVIS`
- **Clé primaire** : `IDDEVIS`
- **Relations** : projet, client, intervention, ligne_devis, facture, sous_facture, rdvs

### Resource : `QuoteResource`
- Transforme les données du modèle pour l'API
- Inclut les calculs comme `NET_HT = PRIX_VENTE_HT - VALEUR_REMISE`

Les devis sont au cœur du système avec des liens vers les projets, clients, interventions et factures.

---

# Système de Types - Endpoint /devis

## Vue d'ensemble

Le système de types dans l'endpoint `/devis` permet de filtrer et segmenter les devis selon différents critères métier. Cette fonctionnalité répond à des besoins organisationnels spécifiques et améliore l'efficacité opérationnelle.

![Diagramme du système de types pour les devis](https://imgur.com/yPWysp9.png)

## Types disponibles

### 'attente' - Devis en attente

**Objectif** : Identifier les devis qui nécessitent une action ou une validation

**Critères de filtrage** :
- Devis non refusés
- Devis non signés
- Devis avec un montant TTC supérieur à 0
- Devis non variants

**Utilisation** : Suivi commercial, tableau de bord des actions en cours

### 'classement' - Devis de classement

**Objectif** : Rassembler les devis actifs pour établir des rankings et statistiques

**Critères de filtrage** :
- Devis non refusés uniquement

**Utilisation** : Analyses de performance, classements commerciaux

### 'ranking' - Devis pour ranking

**Objectif** : Analyser les performances sur une période donnée

**Critères de filtrage** :
- Devis avec date de signature ou de refus dans la période spécifiée
- Filtrage par responsable de projet ou client

**Utilisation** : Rapports de performance, évaluations périodiques

### 'all' - Tous les devis

**Objectif** : Vue globale sans restriction

## Motivations métier

### 1. Segmentation des responsabilités

- **Directeurs commerciaux** : Accès à leurs devis ou vue globale selon les droits
- **Responsables de projet** : Filtrage automatique sur leurs projets
- **Administrateurs** : Accès complet selon les besoins

### 2. Optimisation des performances

- Évite le chargement de tous les devis
- Requêtes ciblées selon le contexte d'utilisation
- Amélioration des temps de réponse

### 3. Gestion du cycle de vie commercial

- **Suivi des étapes** : De la création à la signature
- **Actions ciblées** : Relances, négociations, validations
- **Reporting** : Analyses par statut et période

### 4. Interface utilisateur adaptée

- Tableaux de bord personnalisés selon le rôle
- Écrans contextuels adaptés aux besoins spécifiques
- Navigation intuitive par type de devis

## Validation et sécurité

### Validation des paramètres

- Le paramètre `TYPE` est obligatoirement une chaîne de caractères
- Valeurs autorisées : `'attente'`, `'classement'`, `'all'`, `'ranking'`, `'birthday'`
- Validation stricte pour éviter les erreurs

### Contrôles d'accès

- Filtrage automatique selon les droits utilisateur
- Restriction aux projets de l'utilisateur sauf autorisation spéciale (directeurs)
- Respect de la hiérarchie organisationnelle

## Utilisation pratique

### Cas d'usage principaux

| Type | Contexte | Objectif |
|------|----------|----------|
| `attente` | Tableau de bord commercial | Actions prioritaires |
| `ranking` | Analyses de performance | Évaluations avec période définie |
| `classement` | Reporting général | Statistiques et analyses |
| `all` | Administration | Vue complète |

### Exemples d'appels API

```http
GET /devis?TYPE=attente
GET /devis?TYPE=ranking&DATE_DEBUT=2024-01-01&DATE_FIN=2024-12-31
GET /devis?TYPE=classement&IDRESPONSABLE=123
GET /devis?TYPE=all
```

---
[← Gestion des rendez-vous](./rdvs.md) | [Retour à l'index](./readme.md) | [Endpoint Ranking →](./ranking.md)
