# Documentation API V1 - Cartographie Sanitaire et ESS

**Date de création**: 19 Décembre 2025
**Destinataire**: Daniel Mwema
**Version API**: v1
**Plateforme**: Healthcarology

---

## 📋 Résumé Exécutif

Cette documentation présente les **8 nouveaux endpoints API REST** créés pour exposer les données de cartographie sanitaire et des établissements de santé (ESS) pour l'intégration avec votre module.

**Commit**: `b36ac1f8`
**Branch**: `develop`

---

## 🚀 Endpoints Disponibles (Quick Reference)

### Endpoints Principaux (8)

| # | Endpoint | Description |
|---|----------|-------------|
| 1 | `GET /api/v1/type-of-companies` | Types d'ESS (hôpital, clinique, etc.) |
| 2 | `GET /api/v1/countries` | Pays |
| 3 | `GET /api/v1/provinces` | Provinces |
| 4 | `GET /api/v1/cities` | Villes |
| 5 | `GET /api/v1/towns` | Communes |
| 6 | `GET /api/v1/health-zones` | Zones de santé |
| 7 | `GET /api/v1/health-areas` | Aires de santé |
| 8 | `GET /api/v1/companies` | ESS (Établissements de santé) |

### Routes Imbriquées (12 nouveaux endpoints)

| # | Endpoint | Description |
|---|----------|-------------|
| 1 | `GET /api/v1/countries/{id}/provinces` | Toutes les provinces d'un pays |
| 2 | `GET /api/v1/countries/{id}/health-zones` | Toutes les zones de santé d'un pays |
| 3 | `GET /api/v1/provinces/{id}/cities` | Toutes les villes d'une province |
| 4 | `GET /api/v1/provinces/{id}/health-zones` | Toutes les zones de santé d'une province |
| 5 | `GET /api/v1/cities/{id}/towns` | Toutes les communes d'une ville |
| 6 | `GET /api/v1/health-zones/{id}/health-areas` | Toutes les aires de santé d'une zone |
| 7 | `GET /api/v1/health-areas/{id}/companies` | Tous les ESS d'une aire de santé |
| 8 | `GET /api/v1/type-of-companies/{id}/companies` | Tous les ESS d'un type donné |

**Exemples de filtres:**
```bash
# Endpoints classiques avec filtres
GET /api/v1/countries/1                  # Pays avec provinces et zones
GET /api/v1/provinces?country.id=1       # Provinces d'un pays
GET /api/v1/health-zones?province.id=5   # Zones d'une province
GET /api/v1/health-areas?zone.id=3       # Aires d'une zone
GET /api/v1/companies?area.id=1&active=true  # ESS actifs d'une aire

# Routes imbriquées (nouveauté)
GET /api/v1/countries/1/provinces        # Provinces du pays ID 1
GET /api/v1/countries/1/health-zones     # Zones de santé du pays ID 1
GET /api/v1/provinces/5/health-zones     # Zones de santé de la province ID 5
GET /api/v1/health-zones/3/health-areas  # Aires de santé de la zone ID 3
GET /api/v1/health-areas/1/companies     # ESS de l'aire de santé ID 1
GET /api/v1/type-of-companies/2/companies  # ESS du type ID 2 (ex: Centre de Santé)
```

---

## 📑 Table des Matières

1. [📋 Résumé Exécutif](#-résumé-exécutif)
2. [🚀 Endpoints Disponibles (Quick Reference)](#-endpoints-disponibles-quick-reference)
3. [🎯 API Resources Créées](#-api-resources-créées)
4. [🏗️ Architecture et Organisation](#️-architecture-et-organisation)
   - [Structure des fichiers](#structure-des-fichiers)
   - [Hiérarchies de données](#hiérarchies-de-données)
5. [📡 Endpoints Détaillés](#-endpoints-détaillés)
   - [1. Types d'Établissements de Santé](#1-types-détablissements-de-santé)
   - [2. Pays](#2-pays)
   - [3. Provinces](#3-provinces)
   - [4. Villes](#4-villes)
   - [5. Communes](#5-communes)
   - [6. Zones de Santé](#6-zones-de-santé)
   - [7. Aires de Santé](#7-aires-de-santé)
   - [8. Établissements de Santé (ESS)](#8-établissements-de-santé-ess)
6. [⚡ Fonctionnalités Avancées](#-fonctionnalités-avancées)
   - [1. Pagination](#1-pagination)
   - [2. Option noLimit](#2-option-nolimit)
   - [3. Filtres de Recherche](#3-filtres-de-recherche)
   - [4. Tri (OrderFilter)](#4-tri-orderfilter)
   - [5. Filtres Booléens](#5-filtres-booléens)
   - [6. Combinaison de Filtres](#6-combinaison-de-filtres)
7. [🔧 Optimisations Techniques](#-optimisations-techniques)
   - [1. Eager Loading (Prévention N+1)](#1-eager-loading-prévention-n1)
   - [2. Serialization Groups](#2-serialization-groups)
   - [3. Custom Query Building](#3-custom-query-building)
8. [🔐 Authentification](#-authentification)
9. [📊 Cas d'Usage Typiques](#-cas-dusage-typiques)
10. [📝 Format de Réponse Standard](#-format-de-réponse-standard)
11. [🧪 Tests Recommandés](#-tests-recommandés)
12. [⚠️ Notes Importantes](#️-notes-importantes)
13. [🔍 Documentation Interactive](#-documentation-interactive)
14. [🚀 Prochaines Étapes](#-prochaines-étapes)
15. [📞 Support](#-support)

---

## 🎯 API Resources Créées

| # | Ressource | Endpoint | Entité | Description |
|---|-----------|----------|--------|-------------|
| 1 | **TypeOfCompanyResource** | `/api/v1/type-of-companies` | TypeOfCompany | Types d'établissements de santé |
| 2 | **CountryResource** | `/api/v1/countries` | Country | Pays |
| 3 | **ProvinceResource** | `/api/v1/provinces` | Province | Provinces |
| 4 | **CityResource** | `/api/v1/cities` | City | Villes |
| 5 | **TownResource** | `/api/v1/towns` | Town | Communes |
| 6 | **HealthZoneResource** | `/api/v1/health-zones` | Zone | Zones de santé |
| 7 | **HealthAreaResource** | `/api/v1/health-areas` | Area | Aires de santé |
| 8 | **CompanyResource** | `/api/v1/companies` | Company | Établissements de santé (ESS) |

---

## 🏗️ Architecture et Organisation

### Structure des fichiers:

```
src/
├── ApiResource/V1/
│   ├── Company/
│   │   ├── TypeOfCompanyResource.php
│   │   └── CompanyResource.php
│   ├── Address/
│   │   ├── CountryResource.php
│   │   ├── ProvinceResource.php
│   │   ├── CityResource.php
│   │   └── TownResource.php
│   └── Zone/
│       ├── HealthZoneResource.php
│       └── HealthAreaResource.php
│
└── State/Provider/
    ├── Company/
    │   ├── TypeOfCompanyProvider.php
    │   └── CompanyProvider.php
    ├── Address/
    │   ├── CountryProvider.php
    │   ├── ProvinceProvider.php
    │   ├── CityProvider.php
    │   └── TownProvider.php
    └── Zone/
        ├── HealthZoneProvider.php
        └── HealthAreaProvider.php
```

### Hiérarchies de données:

#### Hiérarchie Administrative:
```
Country (Pays)
  └── Province
      └── City (Ville)
          └── Town (Commune)
```

#### Hiérarchie Sanitaire:
```
Country (Pays)
  └── Province
      └── Zone (Zone de Santé)
          └── Area (Aire de Santé)
              └── Company (ESS)
```

---

## 📡 Endpoints Détaillés

### 1. Types d'Établissements de Santé

#### **GET /api/v1/type-of-companies**
Récupère la liste des types d'établissements de santé (Collection)

**Paramètres de requête:**
- `page` (int): Numéro de page (défaut: 1)
- `name` (string): Recherche partielle sur le nom
- `order[id]` (asc|desc): Tri par ID
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Récupérer tous les résultats sans pagination

**Exemple de requête:**
```bash
GET /api/v1/type-of-companies?name=hôpital&order[name]=asc
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/type-of-companies/1",
      "@type": "TypeOfCompany",
      "id": 1,
      "name": "Hôpital Général de Référence"
    },
    {
      "@id": "/api/v1/type-of-companies/2",
      "@type": "TypeOfCompany",
      "id": 2,
      "name": "Centre de Santé"
    }
  ],
  "hydra:totalItems": 2,
  "hydra:view": {
    "@id": "/api/v1/type-of-companies?page=1",
    "@type": "hydra:PartialCollectionView",
    "hydra:first": "/api/v1/type-of-companies?page=1",
    "hydra:last": "/api/v1/type-of-companies?page=1"
  }
}
```

#### **GET /api/v1/type-of-companies/{id}**
Récupère un type d'établissement spécifique (Item)

**Exemple de requête:**
```bash
GET /api/v1/type-of-companies/1
```

**Exemple de réponse:**
```json
{
  "@context": "/api/contexts/TypeOfCompany",
  "@id": "/api/v1/type-of-companies/1",
  "@type": "TypeOfCompany",
  "id": 1,
  "name": "Hôpital Général de Référence",
  "companies": [
    {
      "@id": "/api/v1/companies/1",
      "id": 1,
      "fullname": "Hôpital Général de Kinshasa"
    }
  ]
}
```

---

### 2. Pays

#### **GET /api/v1/countries**
Récupère la liste des pays

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `code` (string): Recherche exacte sur le code pays
- `order[id]` (asc|desc): Tri par ID
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/countries?name=congo&noLimit=true
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/countries/1",
      "@type": "Country",
      "id": 1,
      "name": "République Démocratique du Congo",
      "code": "CD"
    }
  ],
  "hydra:totalItems": 1
}
```

#### **GET /api/v1/countries/{id}**
Récupère un pays avec ses relations (provinces, zones)

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/countries/1",
  "@type": "Country",
  "id": 1,
  "name": "République Démocratique du Congo",
  "code": "CD",
  "provinces": [
    {
      "@id": "/api/v1/provinces/1",
      "id": 1,
      "name": "Kinshasa"
    }
  ],
  "zones": [
    {
      "@id": "/api/v1/health-zones/1",
      "id": 1,
      "name": "Zone de Santé de Kinshasa"
    }
  ]
}
```

---

### 3. Provinces

#### **GET /api/v1/provinces**
Récupère la liste des provinces

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `code` (string): Recherche sur le code
- `country.id` (int): Filtrer par pays
- `order[id]` (asc|desc): Tri par ID
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/provinces?country.id=1&order[name]=asc
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/provinces/1",
      "@type": "Province",
      "id": 1,
      "name": "Kinshasa",
      "code": "KIN",
      "country": {
        "@id": "/api/v1/countries/1",
        "id": 1,
        "name": "RDC"
      }
    }
  ],
  "hydra:totalItems": 1
}
```

#### **GET /api/v1/provinces/{id}**
Récupère une province avec ses villes et zones de santé

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/provinces/1",
  "@type": "Province",
  "id": 1,
  "name": "Kinshasa",
  "code": "KIN",
  "country": {
    "@id": "/api/v1/countries/1",
    "name": "RDC"
  },
  "cities": [
    {
      "@id": "/api/v1/cities/1",
      "id": 1,
      "name": "Kinshasa"
    }
  ],
  "zones": [
    {
      "@id": "/api/v1/health-zones/1",
      "id": 1,
      "name": "Zone de Santé de Kinshasa"
    }
  ]
}
```

---

### 4. Villes

#### **GET /api/v1/cities**
Récupère la liste des villes

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `province.id` (int): Filtrer par province
- `order[id]` (asc|desc): Tri par ID
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/cities?province.id=1
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/cities/1",
      "@type": "City",
      "id": 1,
      "name": "Kinshasa",
      "province": {
        "@id": "/api/v1/provinces/1",
        "id": 1,
        "name": "Kinshasa"
      }
    }
  ]
}
```

#### **GET /api/v1/cities/{id}**
Récupère une ville avec ses communes

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/cities/1",
  "@type": "City",
  "id": 1,
  "name": "Kinshasa",
  "province": {
    "@id": "/api/v1/provinces/1",
    "name": "Kinshasa"
  },
  "towns": [
    {
      "@id": "/api/v1/towns/1",
      "id": 1,
      "name": "Gombe"
    },
    {
      "@id": "/api/v1/towns/2",
      "id": 2,
      "name": "Limete"
    }
  ]
}
```

---

### 5. Communes

#### **GET /api/v1/towns**
Récupère la liste des communes

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `city.id` (int): Filtrer par ville
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/towns?city.id=1&order[name]=asc
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/towns/1",
      "@type": "Town",
      "id": 1,
      "name": "Gombe",
      "city": {
        "@id": "/api/v1/cities/1",
        "id": 1,
        "name": "Kinshasa"
      }
    }
  ]
}
```

---

### 6. Zones de Santé

#### **GET /api/v1/health-zones**
Récupère la liste des zones de santé

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `province.id` (int): Filtrer par province
- `country.id` (int): Filtrer par pays
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/health-zones?province.id=1&noLimit=true
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/health-zones/1",
      "@type": "Zone",
      "id": 1,
      "name": "Zone de Santé de Kinshasa Ouest",
      "province": {
        "@id": "/api/v1/provinces/1",
        "id": 1,
        "name": "Kinshasa"
      },
      "country": {
        "@id": "/api/v1/countries/1",
        "id": 1,
        "name": "RDC"
      }
    }
  ]
}
```

#### **GET /api/v1/health-zones/{id}**
Récupère une zone de santé avec ses aires de santé

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/health-zones/1",
  "@type": "Zone",
  "id": 1,
  "name": "Zone de Santé de Kinshasa Ouest",
  "province": {
    "@id": "/api/v1/provinces/1",
    "name": "Kinshasa"
  },
  "country": {
    "@id": "/api/v1/countries/1",
    "name": "RDC"
  },
  "areas": [
    {
      "@id": "/api/v1/health-areas/1",
      "id": 1,
      "name": "Aire de Santé Lemba"
    },
    {
      "@id": "/api/v1/health-areas/2",
      "id": 2,
      "name": "Aire de Santé Makala"
    }
  ]
}
```

---

### 7. Aires de Santé

#### **GET /api/v1/health-areas**
Récupère la liste des aires de santé

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom
- `zone.id` (int): Filtrer par zone de santé
- `order[name]` (asc|desc): Tri par nom
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/health-areas?zone.id=1
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/health-areas/1",
      "@type": "Area",
      "id": 1,
      "name": "Aire de Santé Lemba",
      "zone": {
        "@id": "/api/v1/health-zones/1",
        "id": 1,
        "name": "Zone de Santé de Kinshasa Ouest"
      }
    }
  ]
}
```

#### **GET /api/v1/health-areas/{id}**
Récupère une aire de santé avec ses établissements

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/health-areas/1",
  "@type": "Area",
  "id": 1,
  "name": "Aire de Santé Lemba",
  "zone": {
    "@id": "/api/v1/health-zones/1",
    "name": "Zone de Santé de Kinshasa Ouest"
  },
  "companies": [
    {
      "@id": "/api/v1/companies/1",
      "id": 1,
      "fullname": "Centre de Santé Lemba 1"
    }
  ]
}
```

---

### 8. Établissements de Santé (ESS)

#### **GET /api/v1/companies**
Récupère la liste des établissements de santé

**Paramètres de requête:**
- `page` (int): Numéro de page
- `name` (string): Recherche partielle sur le nom complet
- `abbreviation` (string): Recherche partielle sur l'abréviation
- `typeOf.id` (int): Filtrer par type d'établissement
- `area.id` (int): Filtrer par aire de santé
- `country.id` (int): Filtrer par pays
- `active` (bool): Filtrer par statut actif
- `order[id]` (asc|desc): Tri par ID
- `order[createdAt]` (asc|desc): Tri par date de création
- `noLimit` (bool): Sans pagination

**Exemple de requête:**
```bash
GET /api/v1/companies?area.id=1&active=true&order[createdAt]=desc
```

**Exemple de réponse:**
```json
{
  "hydra:member": [
    {
      "@id": "/api/v1/companies/1",
      "@type": "Company",
      "id": 1,
      "reference": "ESS-001",
      "fullname": "Centre de Santé Lemba 1",
      "abbreviation": "CS-L1",
      "actived": true,
      "typeOf": {
        "@id": "/api/v1/type-of-companies/2",
        "id": 2,
        "name": "Centre de Santé"
      },
      "area": {
        "@id": "/api/v1/health-areas/1",
        "id": 1,
        "name": "Aire de Santé Lemba"
      },
      "country": {
        "@id": "/api/v1/countries/1",
        "id": 1,
        "name": "RDC"
      }
    }
  ],
  "hydra:totalItems": 1
}
```

#### **GET /api/v1/companies/{id}**
Récupère un établissement de santé complet avec toutes ses relations

**Exemple de réponse:**
```json
{
  "@id": "/api/v1/companies/1",
  "@type": "Company",
  "id": 1,
  "reference": "ESS-001",
  "fullname": "Centre de Santé Lemba 1",
  "abbreviation": "CS-L1",
  "actived": true,
  "typeOf": {
    "@id": "/api/v1/type-of-companies/2",
    "id": 2,
    "name": "Centre de Santé"
  },
  "area": {
    "@id": "/api/v1/health-areas/1",
    "id": 1,
    "name": "Aire de Santé Lemba",
    "zone": {
      "@id": "/api/v1/health-zones/1",
      "name": "Zone de Santé de Kinshasa Ouest"
    }
  },
  "country": {
    "@id": "/api/v1/countries/1",
    "id": 1,
    "name": "RDC",
    "code": "CD"
  },
  "address": {
    "@type": "Address",
    "id": 1,
    "street": "Avenue Lemba",
    "number": "123",
    "town": {
      "@id": "/api/v1/towns/2",
      "name": "Limete"
    }
  }
}
```

---

## ⚡ Fonctionnalités Avancées

### 1. Pagination

Toutes les collections supportent la pagination automatique:

- **Items par page par défaut**: 30
- **Maximum d'items par page**: 100
- **Paramètre `page`**: Numéro de la page (commence à 1)

**Exemple:**
```bash
GET /api/v1/companies?page=2
```

**Navigation dans la réponse:**
```json
{
  "hydra:view": {
    "@id": "/api/v1/companies?page=2",
    "@type": "hydra:PartialCollectionView",
    "hydra:first": "/api/v1/companies?page=1",
    "hydra:last": "/api/v1/companies?page=5",
    "hydra:previous": "/api/v1/companies?page=1",
    "hydra:next": "/api/v1/companies?page=3"
  },
  "hydra:totalItems": 142
}
```

### 2. Option noLimit

Pour récupérer tous les résultats sans pagination (utile pour les listes de référence):

```bash
GET /api/v1/type-of-companies?noLimit=true
```

**⚠️ Attention**: Utiliser avec précaution sur les grandes collections.

### 3. Filtres de Recherche

#### Recherche partielle (ipartial):
```bash
GET /api/v1/companies?name=hôpital
# Retourne tous les établissements dont le nom contient "hôpital" (insensible à la casse)
```

#### Recherche exacte:
```bash
GET /api/v1/countries?code=CD
# Retourne uniquement le pays avec le code exact "CD"
```

#### Filtres relationnels:
```bash
GET /api/v1/companies?area.id=1&typeOf.id=2
# Retourne les établissements de l'aire 1 ET du type 2
```

### 4. Tri (OrderFilter)

Tri par un ou plusieurs champs:

```bash
GET /api/v1/companies?order[createdAt]=desc&order[fullname]=asc
# Tri par date de création décroissante, puis par nom croissant
```

Options de tri disponibles:
- `asc`: Ordre croissant (A→Z, 1→9, ancien→récent)
- `desc`: Ordre décroissant (Z→A, 9→1, récent→ancien)

### 5. Filtres Booléens

Pour les champs actifs/inactifs:

```bash
GET /api/v1/companies?active=true
# Uniquement les établissements actifs

GET /api/v1/companies?active=false
# Uniquement les établissements inactifs
```

### 6. Combinaison de Filtres

Tous les filtres peuvent être combinés:

```bash
GET /api/v1/companies?area.id=1&active=true&typeOf.id=2&order[fullname]=asc&page=1
```

Cette requête retourne:
- Établissements de l'aire de santé #1
- Qui sont actifs
- De type #2 (ex: Centre de Santé)
- Triés par nom alphabétiquement
- Page 1 des résultats

---

## 🔧 Optimisations Techniques

### 1. Eager Loading (Prévention N+1)

Tous les State Providers utilisent le pattern `leftJoin() + addSelect()` pour charger les relations en une seule requête:

```php
// Exemple: CompanyProvider
$queryBuilder = $this->repository->createQueryBuilder('c')
    ->leftJoin('c.typeOf', 't')
    ->addSelect('t')
    ->leftJoin('c.area', 'a')
    ->addSelect('a')
    ->leftJoin('c.country', 'co')
    ->addSelect('co');
```

**Avantage**: Réduction drastique du nombre de requêtes SQL (1 requête au lieu de N+1).

### 2. Serialization Groups

Deux niveaux de détail:
- **Collection** (`entity:read:collection`): Données minimales pour les listes
- **Item** (`entity:read:item`): Données complètes avec relations pour un élément unique

### 3. Custom Query Building

Chaque Provider implémente sa logique de filtrage optimisée sans passer par les filtres API Platform par défaut (plus performant).

---

## 🔐 Authentification

**Important**: Ces endpoints nécessitent une authentification JWT.

### Obtenir un token:

```bash
POST /api/login
Content-Type: application/json

{
  "username": "user@example.com",
  "password": "password"
}
```

**Réponse:**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
}
```

### Utiliser le token:

```bash
GET /api/v1/companies
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
```

---

## 📊 Cas d'Usage Typiques

### 1. Récupérer la hiérarchie complète d'un pays

```bash
# 1. Récupérer le pays avec ses provinces
GET /api/v1/countries/1

# 2. Pour chaque province, récupérer ses zones de santé
GET /api/v1/health-zones?province.id=1&noLimit=true

# 3. Pour chaque zone, récupérer ses aires
GET /api/v1/health-areas?zone.id=1&noLimit=true

# 4. Pour chaque aire, récupérer ses établissements
GET /api/v1/companies?area.id=1&active=true
```

### 2. Lister tous les hôpitaux actifs d'une province

```bash
# 1. Trouver l'ID du type "Hôpital"
GET /api/v1/type-of-companies?name=hôpital

# 2. Récupérer les zones de la province
GET /api/v1/health-zones?province.id=1&noLimit=true

# 3. Pour chaque zone, récupérer les hôpitaux actifs
GET /api/v1/companies?typeOf.id=1&active=true
```

### 3. Export complet pour cartographie

```bash
# Récupérer toutes les données sans pagination
GET /api/v1/countries?noLimit=true
GET /api/v1/provinces?noLimit=true
GET /api/v1/cities?noLimit=true
GET /api/v1/towns?noLimit=true
GET /api/v1/health-zones?noLimit=true
GET /api/v1/health-areas?noLimit=true
GET /api/v1/type-of-companies?noLimit=true
GET /api/v1/companies?active=true&noLimit=true
```

### 4. Recherche d'établissements par critères multiples

```bash
# Centres de Santé actifs de Kinshasa créés récemment
GET /api/v1/companies?country.id=1&typeOf.id=2&active=true&order[createdAt]=desc
```

---

## 📝 Format de Réponse Standard

Toutes les réponses suivent le format JSON-LD avec Hydra:

```json
{
  "@context": "/api/contexts/ResourceName",
  "@id": "/api/v1/resources",
  "@type": "hydra:Collection",
  "hydra:member": [ /* array of items */ ],
  "hydra:totalItems": 100,
  "hydra:view": { /* pagination links */ },
  "hydra:search": { /* available filters */ }
}
```

### Métadonnées Hydra:

- `@context`: Contexte sémantique de la ressource
- `@id`: IRI de la collection/item
- `@type`: Type de la ressource
- `hydra:member`: Tableau des éléments
- `hydra:totalItems`: Nombre total d'éléments (sans pagination)
- `hydra:view`: Liens de navigation (first, last, previous, next)

---

## 🧪 Tests Recommandés

### 1. Test de connectivité

```bash
curl -X GET "http://localhost/api/v1/countries" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/ld+json"
```

### 2. Test de filtrage

```bash
curl -X GET "http://localhost/api/v1/companies?active=true&page=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 3. Test de récupération item

```bash
curl -X GET "http://localhost/api/v1/companies/1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 4. Test noLimit

```bash
curl -X GET "http://localhost/api/v1/type-of-companies?noLimit=true" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## ⚠️ Notes Importantes

1. **Read-Only**: Tous ces endpoints sont en lecture seule (GET uniquement). Aucune modification n'est possible via l'API.

2. **Performance**: Pour les grandes collections, toujours utiliser la pagination. L'option `noLimit` est réservée aux listes de référence (types, pays, provinces).

3. **Relations**: Les relations sont chargées automatiquement (eager loading) pour éviter les problèmes de performance N+1.

4. **Encodage**: Toutes les réponses sont en UTF-8. Les caractères spéciaux (accents, etc.) sont correctement gérés.

5. **CORS**: Si vous accédez à l'API depuis un domaine différent, assurez-vous que CORS est correctement configuré.

6. **Cache**: Les réponses peuvent être mises en cache côté client pour améliorer les performances.

---

## 🔍 Documentation Interactive

Une fois l'API déployée, vous pouvez accéder à la documentation interactive Swagger:

```
http://localhost/api/docs
```

Cette interface permet de:
- Visualiser tous les endpoints
- Tester les requêtes directement
- Voir les schémas de données
- Essayer différents filtres

---

## 🚀 Prochaines Étapes

1. **Tester les endpoints** avec Postman/curl
2. **Valider les données** retournées correspondent à vos besoins
3. **Intégrer dans votre module** les appels API
4. **Configurer le cache** si nécessaire pour optimiser les performances
5. **Mettre en place le monitoring** pour suivre l'utilisation

---

## 📞 Support

Pour toute question ou demande d'évolution:
- **Contact**: Équipe de développement Healthcarology
- **Commit de référence**: `b36ac1f8`
- **Branch**: `develop`

---

**Fin de la documentation** ✅

Bon développement !
