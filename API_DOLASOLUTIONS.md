# Documentation API V1 - Pour l'équipe Frontend

**URL de base:** `https://hr.healthcarology.org/api/v1`  
**Documentation interactive:** https://hr.healthcarology.org/api/docs

Cette documentation détaille tous les endpoints, paramètres, et exemples pour intégrer l'API dans votre application frontend.

## Table des matières

1. [Vue d'ensemble des routes](#vue-densemble-des-routes)
2. [Authentification](#authentification)
3. [Profil utilisateur](#profil-utilisateur)
4. [Utilisateurs](#utilisateurs)
5. [Contrats](#contrats)
6. [Présences](#présences)
7. [Fiches de paie](#fiches-de-paie)
8. [Congés](#congés)
9. [Rapports de temps](#rapports-de-temps)
10. [Données géographiques](#données-géographiques)
11. [Pagination](#pagination)
12. [Gestion des erreurs](#gestion-des-erreurs)
13. [Exemples de code](#exemples-de-code)

---

## Vue d'ensemble des routes

### Routes d'authentification (publiques)

| Méthode | Route | Description | Corps de la requête | Auth |
|---------|-------|-------------|---------------------|------|
| `POST` | `/api/v1/login` | Authentification initiale | `{username, password}` | Non |
| `POST` | `/api/v1/refresh` | Renouveler le token JWT | `{refresh_token}` | Non |

### Routes profil utilisateur

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/me` | Profil utilisateur connecté | - | - | JWT |
| `GET` | `/api/v1/agents/search` | Recherche AJAX d'agents | `term, limit, company_id, direction_id, department_id, service_id` | - | JWT |

### Routes utilisateurs

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/users` | Liste des utilisateurs | `page, itemsPerPage, firstname, lastname, email, matricule, service.name, company.name, order[*]` | - | JWT |
| `GET` | `/api/v1/users/{id}` | Détails d'un utilisateur | `id` (path) | - | JWT |
| `POST` | `/api/v1/users` | Créer un utilisateur | - | `{firstname, lastname, email, password, matricule?, phoneNumber?, roles?, company}` | JWT |
| `PUT` | `/api/v1/users/{id}` | Modifier un utilisateur (complet) | `id` (path) | `{firstname, lastname, email, ...}` | JWT |
| `PATCH` | `/api/v1/users/{id}` | Modifier un utilisateur (partiel) | `id` (path) | `{champs à modifier}` | JWT |
| `DELETE` | `/api/v1/users/{id}` | Supprimer un utilisateur | `id` (path) | - | JWT |

### Routes contrats

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/contracts` | Liste des contrats | `page, itemsPerPage, agent.lastname, contractType, company.name, order[startDate]` | - | JWT |
| `GET` | `/api/v1/contracts/{id}` | Détails d'un contrat | `id` (path) | - | JWT |
| `POST` | `/api/v1/contracts` | Créer un contrat | - | `{contractType, startDate, endDate?, salary, agent, company}` | JWT |
| `PUT` | `/api/v1/contracts/{id}` | Modifier un contrat | `id` (path) | `{contractType, startDate, ...}` | JWT |

### Routes présences

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `POST` | `/api/v1/presences/checkin` | Enregistrer un pointage | - | `{latitude, longitude}` | JWT |
| `GET` | `/api/v1/presences` | Liste des présences | `page, itemsPerPage, month, year, status` | - | JWT |
| `GET` | `/api/v1/presences/{id}` | Détails d'une présence | `id` (path) | - | JWT |

### Routes fiches de paie

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/payslips` | Liste des fiches de paie | `page, itemsPerPage, month, year, status` | - | JWT |
| `GET` | `/api/v1/payslips/{id}` | Détails d'une fiche de paie | `id` (path) | - | JWT |

### Routes congés

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/leaves` | Liste des demandes de congé | `page, itemsPerPage, agent.id, status, leaveType, startDate[before], startDate[after]` | - | JWT |
| `GET` | `/api/v1/leaves/{id}` | Détails d'une demande de congé | `id` (path) | - | JWT |
| `POST` | `/api/v1/leaves` | Créer une demande de congé | - | `{leaveType, startDate, endDate, duration?, reason?}` | JWT |
| `PATCH` | `/api/v1/leaves/{id}` | Modifier le statut d'un congé | `id` (path) | `{status}` | JWT |

### Routes rapports de temps

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/time_reports` | Liste des rapports de temps | `page, itemsPerPage, agent.id, task.id, entryDate[before], entryDate[after]` | - | JWT |
| `GET` | `/api/v1/time_reports/{id}` | Détails d'un rapport | `id` (path) | - | JWT |
| `POST` | `/api/v1/time_reports` | Créer un rapport de temps | - | `{task, entryDate, startTime, endTime, comment?}` | JWT |
| `PUT` | `/api/v1/time_reports/{id}` | Modifier un rapport (complet) | `id` (path) | `{task, entryDate, startTime, endTime, comment?}` | JWT |
| `PATCH` | `/api/v1/time_reports/{id}` | Modifier un rapport (partiel) | `id` (path) | `{champs à modifier}` | JWT |

### Routes géographiques

| Méthode | Route | Description | Paramètres | Corps | Auth |
|---------|-------|-------------|------------|-------|------|
| `GET` | `/api/v1/countries` | Liste des pays | `page, itemsPerPage` | - | JWT |
| `GET` | `/api/v1/countries/{id}` | Détails d'un pays | `id` (path) | - | JWT |
| `GET` | `/api/v1/provinces` | Liste des provinces | `page, itemsPerPage, country` | - | JWT |
| `GET` | `/api/v1/provinces/{id}` | Détails d'une province | `id` (path) | - | JWT |
| `GET` | `/api/v1/cities` | Liste des villes | `page, itemsPerPage, province` | - | JWT |
| `GET` | `/api/v1/cities/{id}` | Détails d'une ville | `id` (path) | - | JWT |
| `GET` | `/api/v1/towns` | Liste des communes | `page, itemsPerPage, city` | - | JWT |
| `GET` | `/api/v1/towns/{id}` | Détails d'une commune | `id` (path) | - | JWT |
| `GET` | `/api/v1/addresses` | Liste des adresses | `page, itemsPerPage` | - | JWT |
| `GET` | `/api/v1/addresses/{id}` | Détails d'une adresse | `id` (path) | - | JWT |

**Total : 39 routes**
- 2 routes publiques (authentification)
- 37 routes protégées (JWT requis)

**Légende :**
- `?` : Paramètre optionnel
- `order[*]` : Tri possible sur plusieurs champs (ex: `order[lastname]=asc`)
- `Auth` : Type d'authentification requis (JWT = Bearer token, Non = route publique)

---

## Authentification

### 🔓 POST /api/v1/login

**Authentification initiale** - Obtenez votre token JWT et refresh token.

**URL:** `POST /api/v1/login`  
**Authentification:** Non requise

**Paramètres du body (JSON):**
```json
{
  "username": "string (requis)",
  "password": "string (requis)"
}
```

**Champs username acceptés:**
- Email: `user@example.com`
- Username: `john.doe`
- Téléphone: `+243991234567`

**Exemple de requête:**
```bash
curl -X POST https://hr.healthcarology.org/api/v1/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "user@example.com",
    "password": "mypassword123"
  }'
```

**Réponse (200 OK):**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "refreshToken": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c...",
  "userId": 123,
  "email": "user@example.com",
  "fullname": "John Doe",
  "service": "IT",
  "roles": ["ROLE_USER", "ROLE_AGENT"],
  "agent": {
    "id": 456,
    "firstname": "John",
    "lastname": "Doe",
    "matricule": "EMP001",
    "company": {
      "id": 1,
      "name": "ACME Corp"
    }
  }
}
```

**Durée de vie:**
- `token` (JWT): 1 année
- `refreshToken`: 30 jours

**Erreurs possibles:**
- `400 Bad Request`: Paramètres manquants
- `422 Unprocessable Entity`: Identifiants invalides

---

### 🔄 POST /api/v1/refresh

**Renouvellement du token** - Obtenez un nouveau JWT token avec votre refresh token.

**URL:** `POST /api/v1/refresh`  
**Authentification:** Non requise

**Paramètres du body (JSON):**
```json
{
  "refresh_token": "string (requis)"
}
```

**Exemple de requête:**
```bash
curl -X POST https://hr.healthcarology.org/api/v1/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refresh_token": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c..."
  }'
```

**Réponse (200 OK):**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "refresh_token": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c...",
  "refresh_token_expiration": 1729513200
}
```

**Notes:**
- Le refresh token est automatiquement renouvelé (30 jours supplémentaires)
- Si utilisé régulièrement, la session peut être maintenue indéfiniment

**Erreurs possibles:**
- `400 Bad Request`: Refresh token manquant
- `401 Unauthorized`: Refresh token invalide ou expiré

---

### 🔒 Utilisation du token JWT

Pour toutes les routes protégées, ajoutez le header:

```http
Authorization: Bearer {votre_token_jwt}
```

**Exemple:**
```bash
curl -X GET https://hr.healthcarology.org/api/v1/users \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

---

## Profil utilisateur

### GET /api/v1/me

**Profil de l'utilisateur connecté** - Récupère les informations détaillées de l'utilisateur authentifié.

**URL:** `GET /api/v1/me`  
**Authentification:** JWT requis

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/me" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "id": 123,
  "firstname": "John",
  "lastname": "Doe",
  "email": "john.doe@example.com",
  "phoneNumber": "+243991234567",
  "matricule": "EMP001",
  "commitmentDate": "2020-01-15T00:00:00+00:00",
  "service": {
    "id": 10,
    "name": "Développement Web"
  },
  "contracts": [
    {
      "id": "abc123",
      "jobTitle": "Développeur Senior",
      "startDate": "2022-05-01T00:00:00+00:00",
      "endDate": null,
      "isActive": true,
      "contractType": "CDI"
    }
  ]
}
```

---

### GET /api/v1/agents/search

**Recherche d'agents** - Endpoint pour l'autocomplétion et la recherche d'agents.

**URL:** `GET /api/v1/agents/search`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Obligatoire |
|-----------|------|-------------|-------------|
| `term` | string | Terme de recherche (min 2 caractères) | Oui |
| `limit` | integer | Nombre max de résultats (défaut: 10) | Non |
| `company_id` | integer/string | Filtrer par compagnie (ID ou slug) | Non |
| `direction_id` | integer | Filtrer par direction | Non |
| `department_id` | integer | Filtrer par département | Non |
| `service_id` | integer | Filtrer par service | Non |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/agents/search?term=john&limit=5" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
[
  {
    "id": 123,
    "text": "Doe, John (EMP001) - john.doe@example.com",
    "name": "John Doe",
    "email": "john.doe@example.com",
    "phoneNumber": "+243991234567",
    "matricule": "EMP001"
  },
  {
    "id": 456,
    "text": "Smith, Jane (EMP002) - jane.smith@example.com",
    "name": "Jane Smith",
    "email": "jane.smith@example.com",
    "phoneNumber": "+243991234568",
    "matricule": "EMP002"
  }
]
```

**Notes:**
- Si aucune compagnie n'est spécifiée, la compagnie de l'utilisateur connecté est utilisée
- Le terme de recherche doit contenir au moins 2 caractères
- Retourne un tableau vide si le terme est trop court

---

## Utilisateurs

### GET /api/v1/users

**Liste des utilisateurs** - Récupérez la liste paginée des utilisateurs.

**URL:** `GET /api/v1/users`  
**Authentification:** JWT requis

**Paramètres de requête (query params):**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page (défaut: 1) | `?page=2` |
| `itemsPerPage` | integer | Nombre d'éléments par page (défaut: 30, max: 100) | `?itemsPerPage=50` |
| `firstname` | string | Filtrer par prénom (recherche partielle) | `?firstname=John` |
| `lastname` | string | Filtrer par nom (recherche partielle) | `?lastname=Doe` |
| `email` | string | Filtrer par email | `?email=john@example.com` |
| `matricule` | string | Filtrer par matricule | `?matricule=EMP001` |
| `service.name` | string | Filtrer par service | `?service.name=IT` |
| `company.name` | string | Filtrer par entreprise | `?company.name=ACME` |
| `order[lastname]` | string | Trier par nom (asc/desc) | `?order[lastname]=asc` |
| `order[firstname]` | string | Trier par prénom | `?order[firstname]=desc` |
| `order[matricule]` | string | Trier par matricule | `?order[matricule]=asc` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/users?page=1&itemsPerPage=30&order[lastname]=asc" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/User",
  "@id": "/api/v1/users",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/users/1",
      "@type": "User",
      "id": 1,
      "firstname": "John",
      "lastname": "Doe",
      "email": "john.doe@example.com",
      "matricule": "EMP001",
      "phoneNumber": "+243991234567",
      "company": {
        "@id": "/api/v1/companies/1",
        "id": 1,
        "name": "ACME Corp"
      }
    }
  ],
  "hydra:totalItems": 150,
  "hydra:view": {
    "@id": "/api/v1/users?page=1",
    "@type": "hydra:PartialCollectionView",
    "hydra:first": "/api/v1/users?page=1",
    "hydra:last": "/api/v1/users?page=5",
    "hydra:next": "/api/v1/users?page=2"
  }
}
```

---

### GET /api/v1/users/{id}

**Détails d'un utilisateur** - Récupérez les informations complètes d'un utilisateur.

**URL:** `GET /api/v1/users/{id}`  
**Authentification:** JWT requis

**Paramètres:**
- `id` (path, requis): ID de l'utilisateur

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/users/123" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/User",
  "@id": "/api/v1/users/123",
  "@type": "User",
  "id": 123,
  "firstname": "John",
  "lastname": "Doe",
  "email": "john.doe@example.com",
  "matricule": "EMP001",
  "phoneNumber": "+243991234567",
  "roles": ["ROLE_USER", "ROLE_AGENT"],
  "company": {
    "@id": "/api/v1/companies/1",
    "id": 1,
    "name": "ACME Corp"
  }
}
```

---

### POST /api/v1/users

**Créer un utilisateur** - Créez un nouvel utilisateur.

**URL:** `POST /api/v1/users`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "firstname": "string (requis)",
  "lastname": "string (requis)",
  "email": "string (requis)",
  "password": "string (requis)",
  "matricule": "string (optionnel)",
  "phoneNumber": "string (optionnel)",
  "roles": ["array (optionnel)"],
  "company": "/api/v1/companies/1 (IRI, requis)"
}
```

**Exemple de requête:**
```bash
curl -X POST "https://hr.healthcarology.org/api/v1/users" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "firstname": "Jane",
    "lastname": "Smith",
    "email": "jane.smith@example.com",
    "password": "SecurePass123!",
    "matricule": "EMP002",
    "phoneNumber": "+243991234568",
    "company": "/api/v1/companies/1"
  }'
```

**Réponse (201 Created):**
```json
{
  "@context": "/api/v1/contexts/User",
  "@id": "/api/v1/users/124",
  "@type": "User",
  "id": 124,
  "firstname": "Jane",
  "lastname": "Smith",
  "email": "jane.smith@example.com",
  "matricule": "EMP002"
}
```

---

### PUT /api/v1/users/{id}

**Modifier un utilisateur (complet)** - Remplace toutes les données d'un utilisateur.

**URL:** `PUT /api/v1/users/{id}`  
**Authentification:** JWT requis

---

### PATCH /api/v1/users/{id}

**Modifier un utilisateur (partiel)** - Modifie uniquement les champs spécifiés.

**URL:** `PATCH /api/v1/users/{id}`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "phoneNumber": "+243991234569"
}
```

---

### DELETE /api/v1/users/{id}

**Supprimer un utilisateur**

**URL:** `DELETE /api/v1/users/{id}`  
**Authentification:** JWT requis

**Réponse (204 No Content)**

---

## Contrats

### GET /api/v1/contracts

**Liste des contrats** - Récupérez la liste paginée des contrats.

**URL:** `GET /api/v1/contracts`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `itemsPerPage` | integer | Éléments par page | `?itemsPerPage=30` |
| `agent.lastname` | string | Filtrer par nom agent | `?agent.lastname=Doe` |
| `contractType` | string | Type de contrat | `?contractType=CDI` |
| `company.name` | string | Filtrer par entreprise | `?company.name=ACME` |
| `order[startDate]` | string | Trier par date début | `?order[startDate]=desc` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/contracts?contractType=CDI" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/Contract",
  "@id": "/api/v1/contracts",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/contracts/1",
      "@type": "Contract",
      "id": 1,
      "contractType": "CDI",
      "startDate": "2024-01-01T00:00:00+00:00",
      "endDate": null,
      "salary": 2500000,
      "agent": {
        "@id": "/api/v1/users/123",
        "id": 123,
        "fullname": "John Doe"
      }
    }
  ],
  "hydra:totalItems": 45,
  "hydra:view": {
    "@id": "/api/v1/contracts?page=1",
    "@type": "hydra:PartialCollectionView"
  }
}
```

---

### GET /api/v1/contracts/{id}

**Détails d'un contrat**

**URL:** `GET /api/v1/contracts/{id}`  
**Authentification:** JWT requis

---

### POST /api/v1/contracts

**Créer un contrat**

**URL:** `POST /api/v1/contracts`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "contractType": "CDI|CDD|Stage",
  "startDate": "2024-01-01",
  "endDate": "2024-12-31 (optionnel pour CDI)",
  "salary": 2500000,
  "agent": "/api/v1/users/123 (IRI)",
  "company": "/api/v1/companies/1 (IRI)"
}
```

---

### PUT /api/v1/contracts/{id}

**Modifier un contrat**

**URL:** `PUT /api/v1/contracts/{id}`  
**Authentification:** JWT requis

---

## Présences

### POST /api/v1/presences/checkin

**Enregistrer un pointage** - Enregistre la présence de l'agent connecté.

**URL:** `POST /api/v1/presences/checkin`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "latitude": -4.3276,
  "longitude": 15.3136
}
```

**Exemple de requête:**
```bash
curl -X POST "https://hr.healthcarology.org/api/v1/presences/checkin" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "latitude": -4.3276,
    "longitude": 15.3136
  }'
```

**Réponse (201 Created):**
```json
{
  "@id": "/api/v1/presences/789",
  "@type": "Presence",
  "id": 789,
  "date": "2024-10-18T08:30:00+00:00",
  "status": "validated",
  "latitude": -4.3276,
  "longitude": 15.3136,
  "agent": {
    "@id": "/api/v1/users/123",
    "fullname": "John Doe"
  }
}
```

**Erreurs possibles:**
- `400 Bad Request`: Coordonnées hors zone autorisée
- `422 Unprocessable Entity`: Pointage déjà effectué aujourd'hui

---

### GET /api/v1/presences

**Liste des présences** - Récupérez l'historique des pointages.

**URL:** `GET /api/v1/presences`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `month` | integer | Mois (1-12) | `?month=10` |
| `year` | integer | Année | `?year=2024` |
| `status` | string | Statut | `?status=validated` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/presences?month=10&year=2024" \
  -H "Authorization: Bearer {token}"
```

---

### GET /api/v1/presences/{id}

**Détails d'une présence**

**URL:** `GET /api/v1/presences/{id}`  
**Authentification:** JWT requis

---

## Fiches de paie

### GET /api/v1/payslips

**Liste des fiches de paie** - Récupérez vos fiches de paie.

**URL:** `GET /api/v1/payslips`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `month` | integer | Mois (1-12) | `?month=9` |
| `year` | integer | Année | `?year=2024` |
| `status` | string | Statut | `?status=validated` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/payslips?month=9&year=2024" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/Payslip",
  "@id": "/api/v1/payslips",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/payslips/456",
      "@type": "Payslip",
      "id": 456,
      "month": 9,
      "year": 2024,
      "grossSalary": 2500000,
      "netSalary": 2125000,
      "status": "validated",
      "agent": {
        "@id": "/api/v1/users/123",
        "fullname": "John Doe"
      }
    }
  ],
  "hydra:totalItems": 12
}
```

---

### GET /api/v1/payslips/{id}

**Détails d'une fiche de paie** - Voir le détail complet avec déductions.

**URL:** `GET /api/v1/payslips/{id}`  
**Authentification:** JWT requis

---

## Congés

### GET /api/v1/leaves

**Liste des demandes de congé** - Récupérez les demandes de congé.

**URL:** `GET /api/v1/leaves`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `agent.id` | integer | Filtrer par agent | `?agent.id=123` |
| `status` | string | Statut (pending, approved, rejected, canceled, taken) | `?status=pending` |
| `leaveType` | string | Type de congé | `?leaveType=CONGE_PAYE` |
| `startDate[before]` | string | Avant une date | `?startDate[before]=2024-12-31` |
| `startDate[after]` | string | Après une date | `?startDate[after]=2024-01-01` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/leaves?status=pending" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/Leave",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/leaves/abc123",
      "@type": "Leave",
      "id": "abc123",
      "leaveType": "CONGE_PAYE",
      "startDate": "2024-01-10T00:00:00+00:00",
      "endDate": "2024-01-12T00:00:00+00:00",
      "duration": 3,
      "status": "pending",
      "reason": "Vacances annuelles",
      "paid": true,
      "agent": {
        "@id": "/api/v1/users/123",
        "fullname": "John Doe"
      }
    }
  ],
  "hydra:totalItems": 5
}
```

---

### GET /api/v1/leaves/{id}

**Détails d'une demande de congé**

**URL:** `GET /api/v1/leaves/{id}`  
**Authentification:** JWT requis

---

### POST /api/v1/leaves

**Créer une demande de congé** - L'employé crée une demande pour lui-même.

**URL:** `POST /api/v1/leaves`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "leaveType": "CONGE_PAYE",
  "startDate": "2024-01-10T00:00:00+00:00",
  "endDate": "2024-01-12T00:00:00+00:00",
  "duration": 3,
  "reason": "Vacances annuelles"
}
```

**Types de congé disponibles:**
- `CONGE_PAYE` - Congé payé
- `MALADIE` - Congé maladie
- `CONGE_SANS_SOLDE` - Congé sans solde
- `RTT` - Réduction du temps de travail
- `CONGE_MATERNITE` - Congé maternité
- `CONGE_PATERNITE` - Congé paternité
- `AUTRE` - Autre type

**Exemple de requête:**
```bash
curl -X POST "https://hr.healthcarology.org/api/v1/leaves" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "leaveType": "CONGE_PAYE",
    "startDate": "2024-01-10T00:00:00+00:00",
    "endDate": "2024-01-12T00:00:00+00:00",
    "duration": 3,
    "reason": "Vacances annuelles"
  }'
```

**Réponse (201 Created):**
```json
{
  "@id": "/api/v1/leaves/xyz789",
  "@type": "Leave",
  "id": "xyz789",
  "leaveType": "CONGE_PAYE",
  "startDate": "2024-01-10T00:00:00+00:00",
  "endDate": "2024-01-12T00:00:00+00:00",
  "duration": 3,
  "status": "pending",
  "reason": "Vacances annuelles",
  "agent": {
    "@id": "/api/v1/users/123",
    "fullname": "John Doe"
  }
}
```

---

### PATCH /api/v1/leaves/{id}

**Modifier le statut d'une demande** - Un manager approuve ou rejette une demande.

**URL:** `PATCH /api/v1/leaves/{id}`  
**Authentification:** JWT requis (rôle manager)

**Paramètres du body (JSON):**
```json
{
  "status": "approved"
}
```

**Statuts possibles:**
- `approved` - Approuvé
- `rejected` - Rejeté

**Exemple de requête:**
```bash
curl -X PATCH "https://hr.healthcarology.org/api/v1/leaves/xyz789" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "status": "approved"
  }'
```

---

## Rapports de temps

### GET /api/v1/time_reports

**Liste des rapports de temps** - Récupérez les rapports de temps.

**URL:** `GET /api/v1/time_reports`  
**Authentification:** JWT requis

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `agent.id` | integer | Filtrer par agent | `?agent.id=123` |
| `task.id` | string | Filtrer par tâche | `?task.id=task123` |
| `task.project.id` | string | Filtrer par projet | `?task.project.id=proj456` |
| `entryDate[before]` | string | Avant une date | `?entryDate[before]=2024-12-31` |
| `entryDate[after]` | string | Après une date | `?entryDate[after]=2024-01-01` |

**Exemple de requête:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/time_reports?agent.id=123" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/TimeReport",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/time_reports/report123",
      "@type": "TimeReport",
      "id": "report123",
      "entryDate": "2023-12-15",
      "startTime": "09:00:00",
      "endTime": "12:30:00",
      "durationInMinutes": 210,
      "comment": "Développement fonctionnalité X",
      "task": {
        "@id": "/api/v1/tasks/task123",
        "name": "Développement API"
      },
      "agent": {
        "@id": "/api/v1/users/123",
        "fullname": "John Doe"
      }
    }
  ],
  "hydra:totalItems": 25
}
```

---

### GET /api/v1/time_reports/{id}

**Détails d'un rapport de temps**

**URL:** `GET /api/v1/time_reports/{id}`  
**Authentification:** JWT requis

---

### POST /api/v1/time_reports

**Créer un rapport de temps** - L'agent soumet un rapport de temps.

**URL:** `POST /api/v1/time_reports`  
**Authentification:** JWT requis

**Paramètres du body (JSON):**
```json
{
  "task": "/api/v1/tasks/task123",
  "entryDate": "2023-12-15",
  "startTime": "09:00:00",
  "endTime": "12:30:00",
  "comment": "Développement fonctionnalité X"
}
```

**Exemple de requête:**
```bash
curl -X POST "https://hr.healthcarology.org/api/v1/time_reports" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "task": "/api/v1/tasks/task123",
    "entryDate": "2023-12-15",
    "startTime": "09:00:00",
    "endTime": "12:30:00",
    "comment": "Développement fonctionnalité X"
  }'
```

**Réponse (201 Created):**
```json
{
  "@id": "/api/v1/time_reports/report456",
  "@type": "TimeReport",
  "id": "report456",
  "entryDate": "2023-12-15",
  "startTime": "09:00:00",
  "endTime": "12:30:00",
  "durationInMinutes": 210,
  "comment": "Développement fonctionnalité X",
  "task": {
    "@id": "/api/v1/tasks/task123"
  },
  "agent": {
    "@id": "/api/v1/users/123"
  }
}
```

---

### PUT /api/v1/time_reports/{id}

**Modifier un rapport de temps (complet)** - Modification complète par le propriétaire.

**URL:** `PUT /api/v1/time_reports/{id}`  
**Authentification:** JWT requis

---

### PATCH /api/v1/time_reports/{id}

**Modifier un rapport de temps (partiel)** - Modification partielle.

**URL:** `PATCH /api/v1/time_reports/{id}`  
**Authentification:** JWT requis

---

## Données géographiques

### GET /api/v1/countries

**Liste des pays**

**URL:** `GET /api/v1/countries`  
**Authentification:** JWT requis

**Réponse (200 OK):**
```json
{
  "@context": "/api/v1/contexts/Country",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "@id": "/api/v1/countries/1",
      "@type": "Country",
      "id": 1,
      "name": "République Démocratique du Congo",
      "code": "CD"
    }
  ]
}
```

---

### GET /api/v1/provinces

**Liste des provinces** - Provinces filtrées par pays.

**URL:** `GET /api/v1/provinces`  
**Authentification:** JWT requis

**Paramètres de requête:**
- `country` (integer): ID du pays

**Exemple:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/provinces?country=1" \
  -H "Authorization: Bearer {token}"
```

---

### GET /api/v1/cities

**Liste des villes** - Villes filtrées par province.

**URL:** `GET /api/v1/cities`  
**Authentification:** JWT requis

**Paramètres de requête:**
- `province` (integer): ID de la province

**Exemple:**
```bash
curl -X GET "https://hr.healthcarology.org/api/v1/cities?province=5" \
  -H "Authorization: Bearer {token}"
```

---

### GET /api/v1/towns

**Liste des communes** - Communes filtrées par ville.

**URL:** `GET /api/v1/towns`  
**Authentification:** JWT requis

**Paramètres de requête:**
- `city` (integer): ID de la ville

---

### GET /api/v1/addresses

**Liste des adresses**

**URL:** `GET /api/v1/addresses`  
**Authentification:** JWT requis

---

## Pagination

Toutes les collections utilisent le format **Hydra** pour la pagination.

**Paramètres disponibles:**
- `page`: Numéro de page (commence à 1)
- `itemsPerPage`: Nombre d'éléments par page (défaut: 30, max: 100)

**Structure de réponse:**
```json
{
  "@context": "...",
  "@id": "...",
  "@type": "hydra:Collection",
  "hydra:member": [ /* éléments */ ],
  "hydra:totalItems": 150,
  "hydra:view": {
    "@id": "...",
    "@type": "hydra:PartialCollectionView",
    "hydra:first": "/api/v1/users?page=1",
    "hydra:last": "/api/v1/users?page=5",
    "hydra:previous": "/api/v1/users?page=1",
    "hydra:next": "/api/v1/users?page=3"
  }
}
```

**Accès aux données en Flutter:**
```dart
final response = await http.get(
  Uri.parse('$apiUrl/users?page=1&itemsPerPage=30'),
  headers: {'Authorization': 'Bearer $token'},
);
final data = jsonDecode(response.body);

// Récupérer les éléments
final users = data['hydra:member'] as List;

// Récupérer le total
final total = data['hydra:totalItems'] as int;

// Vérifier s'il y a une page suivante
final hasNext = data['hydra:view']['hydra:next'] != null;
```

---

## Gestion des erreurs

### Codes HTTP

| Code | Signification | Description |
|------|---------------|-------------|
| `200` | OK | Requête réussie |
| `201` | Created | Ressource créée |
| `204` | No Content | Suppression réussie |
| `400` | Bad Request | Paramètres invalides |
| `401` | Unauthorized | Token manquant/invalide |
| `403` | Forbidden | Permissions insuffisantes |
| `404` | Not Found | Ressource introuvable |
| `422` | Unprocessable Entity | Erreur de validation |
| `500` | Internal Server Error | Erreur serveur |

### Format des erreurs

**Erreur de validation (422):**
```json
{
  "@context": "/api/v1/contexts/ConstraintViolationList",
  "@type": "ConstraintViolationList",
  "hydra:title": "An error occurred",
  "hydra:description": "email: This value is already used.",
  "violations": [
    {
      "propertyPath": "email",
      "message": "This value is already used."
    }
  ]
}
```

**Erreur d'authentification (401):**
```json
{
  "code": 401,
  "message": "Expired JWT Token"
}
```

---

## Exemples de code

### Flutter / Dart

**Configuration de base - lib/services/api_service.dart**

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'package:shared_preferences/shared_preferences.dart';

class ApiService {
  static const String baseUrl = 'https://hr.healthcarology.org/api/v1';
  
  // 1. Login
  Future<Map<String, dynamic>> login(String username, String password) async {
    final response = await http.post(
      Uri.parse('$baseUrl/login'),
      headers: {'Content-Type': 'application/json'},
      body: jsonEncode({
        'username': username,
        'password': password,
      }),
    );
    
    if (response.statusCode != 200) {
      throw Exception('Login failed');
    }
    
    final data = jsonDecode(response.body);
    
    // Stocker les tokens
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString('jwt_token', data['token']);
    await prefs.setString('refresh_token', data['refreshToken']);
    await prefs.setString('user_data', jsonEncode(data['agent']));
    
    return data;
  }
  
  // 2. Refresh token
  Future<String> refreshToken() async {
    final prefs = await SharedPreferences.getInstance();
    final refreshToken = prefs.getString('refresh_token');
    
    if (refreshToken == null) {
      throw Exception('No refresh token');
    }
    
    final response = await http.post(
      Uri.parse('$baseUrl/refresh'),
      headers: {'Content-Type': 'application/json'},
      body: jsonEncode({'refresh_token': refreshToken}),
    );
    
    if (response.statusCode != 200) {
      // Refresh token invalide, rediriger vers login
      await prefs.clear();
      throw Exception('Session expired');
    }
    
    final data = jsonDecode(response.body);
    
    // Stocker le nouveau token
    await prefs.setString('jwt_token', data['token']);
    if (data['refresh_token'] != null) {
      await prefs.setString('refresh_token', data['refresh_token']);
    }
    
    return data['token'];
  }
  
  // 3. API Request avec gestion automatique du refresh
  Future<dynamic> apiRequest(
    String endpoint, {
    String method = 'GET',
    Map<String, dynamic>? body,
  }) async {
    final prefs = await SharedPreferences.getInstance();
    String? token = prefs.getString('jwt_token');
    
    if (token == null) {
      throw Exception('Not authenticated');
    }
    
    // Première tentative
    http.Response response = await _makeRequest(endpoint, token, method, body);
    
    // Si 401, tenter refresh
    if (response.statusCode == 401) {
      try {
        token = await refreshToken();
        
        // Réessayer avec le nouveau token
        response = await _makeRequest(endpoint, token, method, body);
      } catch (e) {
        throw Exception('Session expired');
      }
    }
    
    if (response.statusCode >= 400) {
      final error = jsonDecode(response.body);
      throw Exception(error['message'] ?? 'API Error');
    }
    
    return jsonDecode(response.body);
  }
  
  Future<http.Response> _makeRequest(
    String endpoint,
    String token,
    String method,
    Map<String, dynamic>? body,
  ) async {
    final uri = Uri.parse('$baseUrl$endpoint');
    final headers = {
      'Authorization': 'Bearer $token',
      'Content-Type': 'application/json',
    };
    
    switch (method) {
      case 'POST':
        return http.post(uri, headers: headers, body: jsonEncode(body));
      case 'PUT':
        return http.put(uri, headers: headers, body: jsonEncode(body));
      case 'PATCH':
        return http.patch(uri, headers: headers, body: jsonEncode(body));
      case 'DELETE':
        return http.delete(uri, headers: headers);
      default:
        return http.get(uri, headers: headers);
    }
  }
  
  // 4. Logout
  Future<void> logout() async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.clear();
  }
  
  // Méthodes métier
  Future<List<dynamic>> getUsers({int page = 1, int itemsPerPage = 30}) async {
    final data = await apiRequest('/users?page=$page&itemsPerPage=$itemsPerPage');
    return data['hydra:member'] as List;
  }
  
  Future<Map<String, dynamic>> getUser(int id) async {
    return await apiRequest('/users/$id');
  }
  
  Future<Map<String, dynamic>> checkin(double latitude, double longitude) async {
    return await apiRequest(
      '/presences/checkin',
      method: 'POST',
      body: {'latitude': latitude, 'longitude': longitude},
    );
  }
  
  Future<List<dynamic>> getPresences({int? month, int? year}) async {
    String query = '';
    if (month != null) query += 'month=$month&';
    if (year != null) query += 'year=$year';
    
    final data = await apiRequest('/presences?$query');
    return data['hydra:member'] as List;
  }
  
  Future<List<dynamic>> getPayslips({int? month, int? year}) async {
    String query = '';
    if (month != null) query += 'month=$month&';
    if (year != null) query += 'year=$year';
    
    final data = await apiRequest('/payslips?$query');
    return data['hydra:member'] as List;
  }
  
  Future<List<dynamic>> getCountries() async {
    final data = await apiRequest('/countries');
    return data['hydra:member'] as List;
  }
  
  Future<List<dynamic>> getProvinces(int countryId) async {
    final data = await apiRequest('/provinces?country=$countryId');
    return data['hydra:member'] as List;
  }
  
  Future<List<dynamic>> getCities(int provinceId) async {
    final data = await apiRequest('/cities?province=$provinceId');
    return data['hydra:member'] as List;
  }
  
  Future<List<dynamic>> getTowns(int cityId) async {
    final data = await apiRequest('/towns?city=$cityId');
    return data['hydra:member'] as List;
  }
}
```

**Utilisation dans un widget - lib/screens/login_screen.dart**

```dart
import 'package:flutter/material.dart';
import '../services/api_service.dart';

class LoginScreen extends StatefulWidget {
  @override
  _LoginScreenState createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _apiService = ApiService();
  final _usernameController = TextEditingController();
  final _passwordController = TextEditingController();
  bool _isLoading = false;
  
  Future<void> _handleLogin() async {
    setState(() => _isLoading = true);
    
    try {
      final data = await _apiService.login(
        _usernameController.text,
        _passwordController.text,
      );
      
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Bienvenue ${data['fullname']}')),
      );
      
      Navigator.pushReplacementNamed(context, '/home');
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Erreur: $e')),
      );
    } finally {
      setState(() => _isLoading = false);
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Connexion')),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _usernameController,
              decoration: InputDecoration(
                labelText: 'Email, Username ou Téléphone',
              ),
            ),
            SizedBox(height: 16),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: 'Mot de passe'),
              obscureText: true,
            ),
            SizedBox(height: 24),
            ElevatedButton(
              onPressed: _isLoading ? null : _handleLogin,
              child: Text(_isLoading ? 'Connexion...' : 'Se connecter'),
            ),
          ],
        ),
      ),
    );
  }
}
```

**Liste des utilisateurs - lib/screens/users_screen.dart**

```dart
import 'package:flutter/material.dart';
import '../services/api_service.dart';

class UsersScreen extends StatefulWidget {
  @override
  _UsersScreenState createState() => _UsersScreenState();
}

class _UsersScreenState extends State<UsersScreen> {
  final _apiService = ApiService();
  List<dynamic> _users = [];
  bool _isLoading = true;
  
  @override
  void initState() {
    super.initState();
    _loadUsers();
  }
  
  Future<void> _loadUsers() async {
    try {
      final users = await _apiService.getUsers(page: 1, itemsPerPage: 30);
      setState(() {
        _users = users;
        _isLoading = false;
      });
    } catch (e) {
      setState(() => _isLoading = false);
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Erreur: $e')),
      );
    }
  }
  
  @override
  Widget build(BuildContext context) {
    if (_isLoading) {
      return Center(child: CircularProgressIndicator());
    }
    
    return ListView.builder(
      itemCount: _users.length,
      itemBuilder: (context, index) {
        final user = _users[index];
        return ListTile(
          title: Text(user['fullname'] ?? ''),
          subtitle: Text(user['email'] ?? ''),
          leading: CircleAvatar(
            child: Text(user['firstname'][0] ?? ''),
          ),
        );
      },
    );
  }
}
```

**Pointage (Checkin) - lib/screens/checkin_screen.dart**

```dart
import 'package:flutter/material.dart';
import 'package:geolocator/geolocator.dart';
import '../services/api_service.dart';

class CheckinScreen extends StatefulWidget {
  @override
  _CheckinScreenState createState() => _CheckinScreenState();
}

class _CheckinScreenState extends State<CheckinScreen> {
  final _apiService = ApiService();
  bool _isLoading = false;
  
  Future<void> _handleCheckin() async {
    setState(() => _isLoading = true);
    
    try {
      // Obtenir la position GPS
      final position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high,
      );
      
      // Envoyer le pointage
      final data = await _apiService.checkin(
        position.latitude,
        position.longitude,
      );
      
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Pointage enregistré avec succès')),
      );
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Erreur: $e')),
      );
    } finally {
      setState(() => _isLoading = false);
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Pointage')),
      body: Center(
        child: ElevatedButton(
          onPressed: _isLoading ? null : _handleCheckin,
          child: Text(_isLoading ? 'Enregistrement...' : 'Pointer'),
        ),
      ),
    );
  }
}
```

**Dépendances à ajouter dans pubspec.yaml:**

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
  shared_preferences: ^2.2.0
  geolocator: ^10.1.0
```

---

## Support

**Documentation interactive Swagger:**  
https://hr.healthcarology.org/api/docs

**Contact:**  
Équipe Dev Healthcarology

---

**Version:** 1.0  
**Date:** 18 octobre 2024  
**Status:** Production ready
