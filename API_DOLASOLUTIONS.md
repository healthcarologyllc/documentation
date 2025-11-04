# Documentation API V1 - Pour l'équipe Frontend

**URL de base:** `https://dolasolutions.com/api/v1`  
**Documentation interactive:** https://dolasolutions.com/api/docs

Cette documentation détaille tous les endpoints, paramètres, et exemples pour intégrer l'API dans votre application frontend.

## Table des matières

1. [Vue d'ensemble des routes](#vue-densemble-des-routes)
2. [Architecture API Platform](#architecture-api-platform)
3. [Authentification](#authentification)
4. [Profil utilisateur](#profil-utilisateur)
5. [Utilisateurs](#utilisateurs)
6. [Contrats](#contrats)
7. [Présences](#présences)
8. [Fiches de paie](#fiches-de-paie)
9. [Congés](#congés)
10. [Rapports de temps](#rapports-de-temps)
11. [Données géographiques](#données-géographiques)
12. [Pagination](#pagination)
13. [Gestion des erreurs](#gestion-des-erreurs)
14. [Exemples de code](#exemples-de-code)

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

**Total : 40+ routes**
- 2 routes publiques (authentification)
- 38+ routes protégées (JWT requis)

**Légende :**
- `?` : Paramètre optionnel
- `order[*]` : Tri possible sur plusieurs champs (ex: `order[lastname]=asc`)
- `Auth` : Type d'authentification requis (JWT = Bearer token, Non = route publique)

---

## Architecture API Platform

L'API est construite avec **API Platform 4.0** en utilisant une architecture resource-based modulaire.

### Structure des ressources

**Localisation des ApiResource:**
- `/src/ApiResource/V1/` - Toutes les configurations des ressources API
- `/src/State/Provider/` - Custom State Providers (GET operations)
- `/src/State/Processor/` - Custom State Processors (POST/PUT/PATCH operations)
- `/src/Dto/` - Data Transfer Objects pour les inputs/outputs

### Fonctionnalités principales

1. **Type-safe DTOs** - Utilisation de DTOs pour les inputs et outputs
2. **Custom State Management** - Providers et Processors pour la logique métier complexe
3. **OpenAPI/Swagger Documentation** - Documentation automatique et interactive à `/api/docs`
4. **Pagination Hydra** - Pagination standard via hydra:member, hydra:totalItems, hydra:view
5. **Filtres avancés** - SearchFilter, DateFilter, OrderFilter, RangeFilter
6. **Sécurité JWT** - Token-based authentication avec refresh token
7. **Groupes de sérialisation** - Contrôle fin des champs dans les réponses

### Configuration par défaut

```
- Pagination: 30 items/page (max 100)
- Format réponse: JSON-LD avec Hydra
- Authentification: JWT Bearer token
- Ordre par défaut: DESC pour les timestamps/dates
```

### Custom State Providers (pour les GET)

| Class | Ressource | Logique |
|-------|-----------|---------|
| `PresenceCollectionProvider` | `/presences` | Filtre les présences de l'agent connecté, supporte filtrage par month/year |
| `PresenceItemProvider` | `/presences/{id}` | Charge une présence spécifique avec vérification de propriété |
| `PayslipCollectionProvider` | `/payslips` | Filtre les fiches de paie de l'agent connecté, génère URLs PDF, supporte month/year |
| `PayslipItemProvider` | `/payslips/{id}` | Charge une fiche de paie avec propriété vérifiée, génère URL PDF |
| `MeProvider` | `/me` | Retourne le profil de l'agent authentifié avec services et contrats |

### Custom State Processors (pour POST/PUT/PATCH)

| Class | Ressource | Logique |
|-------|-----------|---------|
| `CheckinProcessor` | `POST /presences/checkin` | Valide pointage selon règles métier, vérifie localisation, crée Presence |
| `LoginProcessor` | `POST /login` | Authentifie utilisateur (email/username/phone), génère JWT + refresh token |
| `LeaveProcessor` | `POST/PATCH /leaves` | Associe congé à agent connecté, définit statut = PENDING |
| `TimeReportProcessor` | `POST/PUT/PATCH /time-reports` | Associe rapport à agent, calcule durée en minutes |

---

## Authentification

### 🔓 POST /api/v1/login

**Authentification initiale** - Authentification flexible avec JWT et refresh token.

**URL:** `POST /api/v1/login`
**Authentification:** Non requise
**ApiResource:** `App\ApiResource\V1\LoginResource`
**Processor:** `App\State\Processor\LoginProcessor`

**Paramètres du body (JSON) - LoginInputDto:**
```json
{
  "username": "string (requis)",
  "password": "string (requis)"
}
```

**Champs username acceptés:**
- Email: `user@example.com`
- Nom d'utilisateur: `john.doe`
- Téléphone: `+243991234567`

**Exemple de requête:**
```bash
curl -X POST https://dolasolutions.com/api/v1/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "kabongo@example.com",
    "password": "mypassword123"
  }'
```

**Réponse (200 OK) - LoginOutputDto:**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "userId": 42,
  "email": "kabongo@example.com",
  "fullname": "KABONGO Jean Mukendi",
  "service": "Service RH",
  "roles": ["ROLE_USER", "ROLE_AGENT"],
  "agent": {
    "id": 42,
    "firstname": "Jean",
    "lastname": "KABONGO",
    "middlename": "Mukendi",
    "fullname": "KABONGO Jean Mukendi",
    "email": "kabongo@example.com",
    "phoneNumber": "+243991234567",
    "matricule": "AG2025001",
    "service": {
      "id": "01H2X3Y4Z5...",
      "name": "Service RH"
    },
    "company": {
      "id": "01H2X3Y4Z5...",
      "name": "DOLASOLUTIONS SARL"
    }
  },
  "refreshToken": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c..."
}
```

**Champs de réponse (LoginOutputDto):**
- `token` (string): JWT token pour l'authentification aux requêtes protégées
- `userId` (integer): ID de l'utilisateur
- `email` (string): Email de l'utilisateur
- `fullname` (string): Nom complet (prénom + nom + middlename)
- `service` (string, nullable): Nom du service/département
- `roles` (array): Liste des rôles de l'utilisateur [ROLE_USER, ROLE_AGENT, ROLE_DIRECTOR, ROLE_ADMIN, etc.]
- `agent` (object, nullable): Données complètes de l'agent
  - `id` (integer): ID de l'agent
  - `firstname` (string): Prénom
  - `lastname` (string): Nom
  - `middlename` (string, nullable): Deuxième prénom
  - `fullname` (string): Nom complet
  - `email` (string): Email
  - `phoneNumber` (string, nullable): Numéro de téléphone
  - `matricule` (string, nullable): Matricule d'employé
  - `service` (object, nullable): Service de l'agent
    - `id` (string): UUID du service
    - `name` (string): Nom du service
  - `company` (object, nullable): Entreprise de l'agent
    - `id` (string): UUID de l'entreprise
    - `name` (string): Nom de l'entreprise
- `refreshToken` (string, nullable): Token pour renouveler le JWT

**Durée de vie:**
- `token` (JWT): 1 année
- `refreshToken`: 30 jours
- `refreshToken` est renouvelé automatiquement sur chaque `/refresh`

**Erreurs possibles:**
- `400 Bad Request`: Identifiants invalides ou compte désactivé
- `422 Unprocessable Entity`: Champs manquants ou invalides (email et password requis)

---

### 🔄 POST /api/v1/refresh

**Renouvellement du token** - Obtenez un nouveau JWT token avec votre refresh token.

**URL:** `POST /api/v1/refresh`
**Authentification:** Non requise
**ApiResource:** `App\ApiResource\V1\RefreshTokenResource`
**Controller:** `gesdinet.jwtrefreshtoken::refresh` (Gesdinet JWTRefreshTokenBundle)
**Entity:** `App\Entity\User\RefreshToken`

**Paramètres du body (JSON):**
```json
{
  "refresh_token": "string (requis)"
}
```

**Exemple de requête:**
```bash
curl -X POST https://dolasolutions.com/api/v1/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refresh_token": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c"
  }'
```

**Réponse (200 OK):**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpYXQiOjE2OTczNzI4MDAsImV4cCI6MTcyODk3MjgwMCwicm9sZXMiOlsiUk9MRV9VU0VSIl0sInVzZXJuYW1lIjoidXNlckBleGFtcGxlLmNvbSJ9...",
  "refresh_token": "5a3f8b9c2e1d4f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c",
  "refresh_token_expiration": 1729513200
}
```

**Champs de réponse:**
- `token` (string): Nouveau JWT token valide pour 1 année (31,536,000 secondes)
- `refresh_token` (string): Refresh token renouvelé avec 30 jours supplémentaires (si `ttl_update` activé)
- `refresh_token_expiration` (integer): Timestamp Unix d'expiration du refresh token

**Configuration du bundle:**
```yaml
gesdinet_jwt_refresh_token:
    ttl: 2592000              # 30 jours en secondes
    ttl_update: true          # Renouvelle automatiquement le TTL à chaque utilisation
    token_parameter_name: refresh_token
    return_expiration: true
    return_expiration_parameter_name: refresh_token_expiration
    single_use: false         # Les tokens peuvent être réutilisés
```

**Configuration JWT:**
```yaml
lexik_jwt_authentication:
    token_ttl: 31536000      # 1 année en secondes (token JWT)
```

**Sécurité:**
- Endpoint **public** (pas d'authentification requise)
- Firewall dédié: `api_refresh` avec security: false
- Access Control: `PUBLIC_ACCESS`

**Notes:**
- Le refresh token a une durée de vie de **30 jours**
- Le JWT token a une durée de vie de **1 année**
- Le refresh token est automatiquement renouvelé avec 30 jours supplémentaires à chaque utilisation
- Si utilisé régulièrement, la session peut être maintenue **indéfiniment**
- Les tokens peuvent être réutilisés (`single_use: false`)

**Erreurs possibles:**
- `400 Bad Request`: Refresh token manquant ou format invalide (code: 400, message: "Refresh token is required")
- `401 Unauthorized`: Refresh token invalide, expiré ou révoqué (code: 401, message: "Invalid refresh token")

---

### 🔒 Utilisation du token JWT

Pour toutes les routes protégées, ajoutez le header:

```http
Authorization: Bearer {votre_token_jwt}
```

**Exemple:**
```bash
curl -X GET https://dolasolutions.com/api/v1/users \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

---

## Profil utilisateur

### GET /api/v1/me

**Profil de l'utilisateur connecté** - Récupère les informations détaillées de l'utilisateur authentifié.

**URL:** `GET /api/v1/me`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\MeOperationResource`
**Provider:** `App\State\Provider\MeProvider`
**Output DTO:** `App\Dto\User\MeOutputDto`

**Exemple de requête:**
```bash
curl -X GET "https://dolasolutions.com/api/v1/me" \
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

**Champs de réponse (MeOutputDto):**
- `id` (integer): ID de l'utilisateur
- `firstname` (string): Prénom
- `lastname` (string): Nom
- `email` (string): Adresse email
- `phoneNumber` (string, nullable): Numéro de téléphone
- `matricule` (string, nullable): Matricule d'employé
- `commitmentDate` (datetime, nullable): Date d'engagement
- `service` (object, nullable): Informations du service
  - `id` (integer): ID du service
  - `name` (string): Nom du service
- `contracts` (array): Liste des contrats associés à l'utilisateur

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
curl -X GET "https://dolasolutions.com/api/v1/agents/search?term=john&limit=5" \
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
curl -X GET "https://dolasolutions.com/api/v1/users?page=1&itemsPerPage=30&order[lastname]=asc" \
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
curl -X GET "https://dolasolutions.com/api/v1/users/123" \
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

**Créer un utilisateur** - Créez un nouvel utilisateur avec validation complète.

**URL:** `POST /api/v1/users`
**Authentification:** JWT requis (rôle: CREATE_USER)
**ApiResource:** `App\ApiResource\V1\UserResource`
**Input DTO:** `App\Dto\Input\UserInputDto`
**Validation:** Groupes `user:create` + contraintes de base
**Groupes de sérialisation:** `user:write` pour l'input

**Paramètres du body (JSON) - UserInputDto:**
```json
{
  "email": "string (requis, format email)",
  "plainPassword": "string (requis, min 8 caractères)",
  "firstname": "string (requis)",
  "lastname": "string (requis)",
  "middlename": "string (optionnel)",
  "gender": "Male|Female|Other (optionnel)",
  "phoneNumber": "string (optionnel, format: +XXX ou 7-20 chiffres)",
  "matricule": "string (optionnel)",
  "serviceId": "string (optionnel, ID du service)"
}
```

**Exemple de requête:**
```bash
curl -X POST "https://dolasolutions.com/api/v1/users" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jane.smith@example.com",
    "plainPassword": "SecurePass123!",
    "firstname": "Jane",
    "lastname": "Smith",
    "gender": "Female",
    "phoneNumber": "+243991234568",
    "matricule": "EMP002",
    "serviceId": "10"
  }'
```

**Réponse (201 Created):**
```json
{
  "id": 124,
  "firstname": "Jane",
  "lastname": "Smith",
  "email": "jane.smith@example.com",
  "matricule": "EMP002"
}
```

**Validation des champs (UserInputDto):**
- `email`: Requis pour la création, doit être un email valide, doit être unique
- `plainPassword`: Requis pour la création, minimum 8 caractères
- `firstname`: Requis pour création et mise à jour
- `lastname`: Requis pour création et mise à jour
- `middlename`: Optionnel
- `gender`: Doit être "Male", "Female" ou "Other"
- `phoneNumber`: Format regex: `^\+?[0-9\s\-]{7,20}$`
- `matricule`: Optionnel
- `serviceId`: Optionnel, doit être un ID de service valide

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

**Paramètres du body (JSON) - UserInputDto (champs partiels):**
```json
{
  "phoneNumber": "+243991234569",
  "firstname": "Jean",
  "gender": "Male"
}
```

**Champs modifiables:**
- `email`: Email valide (doit être unique)
- `plainPassword`: Minimum 8 caractères
- `firstname`: Prénom requis
- `lastname`: Nom requis
- `middlename`: Second prénom
- `gender`: "Male", "Female" ou "Other"
- `phoneNumber`: Format valide
- `matricule`: Matricule d'employé
- `serviceId`: ID du service

**Réponse (200 OK):**
```json
{
  "id": 123,
  "firstname": "Jean",
  "lastname": "Doe",
  "email": "john.doe@example.com",
  "phoneNumber": "+243991234569",
  "gender": "Male"
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
curl -X GET "https://dolasolutions.com/api/v1/contracts?contractType=CDI" \
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

**Enregistrer un pointage** - Enregistre la présence de l'agent connecté avec validation métier.

**URL:** `POST /api/v1/presences/checkin`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\PresenceResource`
**Processor:** `App\State\Processor\CheckinProcessor`
**Input DTO:** `App\Dto\Presence\CheckinInputDto`
**Output DTO:** `App\Dto\Presence\CheckinOutputDto`

**Paramètres du body (JSON) - CheckinInputDto:**
```json
{
  "type": "CHECKIN|CHECKOUT|BREAK_START|BREAK_END (requis)",
  "latitude": "number (requis)",
  "longitude": "number (requis)",
  "comment": "string (optionnel)",
  "deviceInfo": "string (optionnel)"
}
```

**Types de pointage disponibles:**
- `CHECKIN`: Arrivée/Début de journée
- `CHECKOUT`: Départ/Fin de journée
- `BREAK_START`: Début de pause
- `BREAK_END`: Fin de pause

**Exemple de requête:**
```bash
curl -X POST "https://dolasolutions.com/api/v1/presences/checkin" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "CHECKIN",
    "latitude": -4.3276,
    "longitude": 15.3136,
    "comment": "Arrivée au bureau",
    "deviceInfo": "iPhone 12"
  }'
```

**Réponse (201 Created) - CheckinOutputDto:**
```json
{
  "id": 789,
  "type": "CHECKIN",
  "timestamp": "2024-10-18T08:30:00+00:00",
  "status": "validated",
  "locationValidated": true,
  "message": "Pointage enregistré avec succès",
  "nextAllowedActions": ["BREAK_START"]
}
```

**Champs de réponse (CheckinOutputDto):**
- `id` (integer): ID du pointage
- `type` (string): Type de pointage effectué
- `timestamp` (datetime): Date et heure du pointage
- `status` (string): Statut du pointage ("validated", "pending", etc.)
- `locationValidated` (boolean): Indique si la localisation est valide
- `message` (string, nullable): Message informatif
- `nextAllowedActions` (array): Liste des actions autorisées après ce pointage

**Validation des champs (CheckinInputDto):**
- `type`: Requis, doit être CHECKIN, CHECKOUT, BREAK_START ou BREAK_END
- `latitude`: Requis, doit être un nombre valide
- `longitude`: Requis, doit être un nombre valide
- `comment`: Optionnel
- `deviceInfo`: Optionnel

**Erreurs possibles:**
- `400 Bad Request`: Coordonnées invalides ou hors zone autorisée
- `422 Unprocessable Entity`: Pointage déjà effectué ou action non autorisée

---

### GET /api/v1/presences

**Liste des présences** - Récupérez l'historique des pointages de l'agent connecté.

**URL:** `GET /api/v1/presences`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\PresenceResource`
**Provider:** `App\State\Provider\PresenceCollectionProvider`
**Output DTO:** `App\Dto\Presence\PresenceOutputDto`

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `itemsPerPage` | integer | Éléments par page (défaut: 30, max: 100) | `?itemsPerPage=50` |
| `month` | integer | Mois (1-12) | `?month=10` |
| `year` | integer | Année | `?year=2024` |
| `status` | string | Statut du pointage | `?status=validated` |

**Exemple de requête:**
```bash
curl -X GET "https://dolasolutions.com/api/v1/presences?month=10&year=2024&page=1&itemsPerPage=30" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK) - PresenceOutputDto list:**
```json
{
  "@context": "/api/v1/contexts/Presence",
  "@id": "/api/v1/presences",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "id": 789,
      "checkin": "2024-10-18T08:30:00+00:00",
      "checkout": "2024-10-18T17:30:00+00:00",
      "type": "STANDARD",
      "status": "validated",
      "timestamp": "2024-10-18T08:30:00+00:00",
      "durationInMinutes": 540,
      "comment": "Journée normale",
      "locationValidated": true,
      "validationNote": null
    }
  ],
  "hydra:totalItems": 22,
  "hydra:view": {
    "@id": "/api/v1/presences?page=1",
    "@type": "hydra:PartialCollectionView"
  }
}
```

**Champs de réponse (PresenceOutputDto):**
- `id` (integer): ID du pointage
- `checkin` (datetime, nullable): Date et heure d'arrivée
- `checkout` (datetime, nullable): Date et heure de départ
- `type` (string, nullable): Type de présence
- `status` (string): Statut du pointage
- `timestamp` (datetime, nullable): Timestamp du pointage
- `durationInMinutes` (integer, nullable): Durée en minutes
- `comment` (string, nullable): Commentaire associé
- `locationValidated` (boolean): Indique si la localisation est valide
- `validationNote` (string, nullable): Note de validation

---

### GET /api/v1/presences/{id}

**Détails d'une présence** - Récupérez les détails complets d'une présence spécifique.

**URL:** `GET /api/v1/presences/{id}`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\PresenceResource`
**Provider:** `App\State\Provider\PresenceItemProvider`
**Output DTO:** `App\Dto\Presence\PresenceOutputDto`
**Sécurité:** Vérifie automatiquement que la présence appartient à l'agent connecté

**Réponse (200 OK) - PresenceOutputDto:**
```json
{
  "id": 789,
  "checkin": "2024-10-18T08:30:00+00:00",
  "checkout": "2024-10-18T17:30:00+00:00",
  "type": "STANDARD",
  "status": "validated",
  "timestamp": "2024-10-18T08:30:00+00:00",
  "durationInMinutes": 540,
  "comment": "Journée normale",
  "locationValidated": true,
  "validationNote": null
}
```

---

## Fiches de paie

### GET /api/v1/payslips

**Liste des fiches de paie** - Récupérez vos fiches de paie avec détails de calcul.

**URL:** `GET /api/v1/payslips`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\PayslipResource`
**Provider:** `App\State\Provider\PayslipCollectionProvider`
**Output DTO:** `App\Dto\Payroll\PayslipOutputDto`
**Ordre par défaut:** periodMonth DESC (derniers mois d'abord)

**Paramètres de requête:**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| `page` | integer | Numéro de page | `?page=1` |
| `itemsPerPage` | integer | Éléments par page (défaut: 30, max: 100) | `?itemsPerPage=30` |
| `month` | integer | Mois (1-12) | `?month=9` |
| `year` | integer | Année | `?year=2024` |
| `status` | string | Statut | `?status=validated` |

**Exemple de requête:**
```bash
curl -X GET "https://dolasolutions.com/api/v1/payslips?month=9&year=2024&page=1" \
  -H "Authorization: Bearer {token}"
```

**Réponse (200 OK) - PayslipOutputDto list:**
```json
{
  "@context": "/api/v1/contexts/Payslip",
  "@id": "/api/v1/payslips",
  "@type": "hydra:Collection",
  "hydra:member": [
    {
      "id": "456",
      "agentName": "John Doe",
      "matricule": "EMP001",
      "periodMonth": 9,
      "periodYear": 2024,
      "baseSalary": 2500000,
      "grossSalary": 2500000,
      "netSalary": 2125000,
      "workedHours": 160,
      "status": "validated"
    }
  ],
  "hydra:totalItems": 12,
  "hydra:view": {
    "@id": "/api/v1/payslips?page=1",
    "@type": "hydra:PartialCollectionView"
  }
}
```

**Champs de réponse (PayslipOutputDto - vue liste):**
- `id` (string): UUID de la fiche de paie
- `agentName` (string): Nom complet de l'agent
- `matricule` (string): Matricule d'employé
- `periodMonth` (integer): Mois de la paie (1-12)
- `periodYear` (integer): Année de la paie
- `baseSalary` (float): Salaire de base
- `grossSalary` (float): Salaire brut (avant déductions)
- `netSalary` (float): Salaire net (après déductions)
- `workedHours` (integer): Heures travaillées
- `status` (string): Statut de la fiche ("validated", "pending", "draft", etc.)

---

### GET /api/v1/payslips/{id}

**Détails d'une fiche de paie** - Voir le détail complet avec déductions, bonus et URL PDF.

**URL:** `GET /api/v1/payslips/{id}`
**Authentification:** JWT requis (ROLE_USER)
**ApiResource:** `App\ApiResource\V1\PayslipResource`
**Provider:** `App\State\Provider\PayslipItemProvider`
**Output DTO:** `App\Dto\Payroll\PayslipOutputDto`
**Sécurité:** Vérifie automatiquement que la fiche appartient à l'agent connecté

**Réponse (200 OK) - PayslipOutputDto (détails complets):**
```json
{
  "id": "456",
  "agentName": "John Doe",
  "matricule": "EMP001",
  "periodMonth": 9,
  "periodYear": 2024,
  "baseSalary": 2500000,
  "grossSalary": 2500000,
  "netSalary": 2125000,
  "workedHours": 160,
  "status": "validated",
  "calculationDetails": {
    "presenceMinutes": 9600,
    "timeReportMinutes": 0,
    "totalMinutes": 9600,
    "overtimeMinutes": 0,
    "baseSalaryAmount": 2500000,
    "overtimeAmount": 0,
    "bonusesAmount": 0,
    "employeeContributionsAmount": 250000,
    "incomeTaxAmount": 125000,
    "netPayableAmount": 2125000
  },
  "bonuses": [
    {
      "id": "bonus123",
      "name": "Performance Bonus",
      "amount": 150000,
      "date": "2024-09-30T00:00:00+00:00"
    }
  ],
  "deductions": [
    {
      "type": "employee_social",
      "name": "Cotisations sociales",
      "amount": 250000,
      "rate": 10.0
    },
    {
      "type": "income_tax",
      "name": "Impôt sur le revenu",
      "amount": 125000,
      "rate": 5.0
    }
  ],
  "pdfUrl": "/api/v1/payslips/456/pdf"
}
```

**Champs de réponse (PayslipOutputDto - détails complets):**
- `calculationDetails` (object, nullable): Détails des calculs
  - `presenceMinutes` (integer): Minutes de présence
  - `timeReportMinutes` (integer): Minutes de rapport temps
  - `totalMinutes` (integer): Total des minutes
  - `overtimeMinutes` (integer): Minutes supplémentaires
  - `baseSalaryAmount` (float): Montant salaire de base
  - `overtimeAmount` (float): Montant heures supplémentaires
  - `bonusesAmount` (float): Montant total des bonus
  - `employeeContributionsAmount` (float): Cotisations sociales
  - `incomeTaxAmount` (float): Impôt sur le revenu
  - `netPayableAmount` (float): Montant net à verser
- `bonuses` (array): Liste des bonus appliqués
  - `id` (string): ID du bonus
  - `name` (string): Nom du bonus
  - `amount` (float): Montant
  - `date` (datetime): Date du bonus
- `deductions` (array): Liste des déductions
  - `type` (string): Type de déduction ("employee_social", "income_tax", etc.)
  - `name` (string): Nom de la déduction
  - `amount` (float): Montant de la déduction
  - `rate` (float): Taux appliqué
- `pdfUrl` (string, nullable): URL pour télécharger le PDF de la fiche

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
curl -X GET "https://dolasolutions.com/api/v1/leaves?status=pending" \
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
curl -X POST "https://dolasolutions.com/api/v1/leaves" \
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
curl -X PATCH "https://dolasolutions.com/api/v1/leaves/xyz789" \
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
curl -X GET "https://dolasolutions.com/api/v1/time_reports?agent.id=123" \
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
curl -X POST "https://dolasolutions.com/api/v1/time_reports" \
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
curl -X GET "https://dolasolutions.com/api/v1/provinces?country=1" \
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
curl -X GET "https://dolasolutions.com/api/v1/cities?province=5" \
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
  static const String baseUrl = 'https://dolasolutions.com/api/v1';
  
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

  Future<Map<String, dynamic>> createUser({
    required String email,
    required String plainPassword,
    required String firstname,
    required String lastname,
    String? middlename,
    String? gender,
    String? phoneNumber,
    String? matricule,
    String? serviceId,
  }) async {
    return await apiRequest(
      '/users',
      method: 'POST',
      body: {
        'email': email,
        'plainPassword': plainPassword,
        'firstname': firstname,
        'lastname': lastname,
        if (middlename != null) 'middlename': middlename,
        if (gender != null) 'gender': gender,
        if (phoneNumber != null) 'phoneNumber': phoneNumber,
        if (matricule != null) 'matricule': matricule,
        if (serviceId != null) 'serviceId': serviceId,
      },
    );
  }

  Future<Map<String, dynamic>> checkin({
    required String type,
    required double latitude,
    required double longitude,
    String? comment,
    String? deviceInfo,
  }) async {
    return await apiRequest(
      '/presences/checkin',
      method: 'POST',
      body: {
        'type': type,
        'latitude': latitude,
        'longitude': longitude,
        if (comment != null) 'comment': comment,
        if (deviceInfo != null) 'deviceInfo': deviceInfo,
      },
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
  String _checkinType = 'CHECKIN';

  Future<void> _handleCheckin() async {
    setState(() => _isLoading = true);

    try {
      // Obtenir la position GPS
      final position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high,
      );

      // Envoyer le pointage avec le type approprié
      final data = await _apiService.checkin(
        type: _checkinType,
        latitude: position.latitude,
        longitude: position.longitude,
        comment: 'Pointage automatique',
        deviceInfo: 'Flutter App',
      );

      final message = data['message'] ?? 'Pointage enregistré avec succès';
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text(message)),
      );

      // Afficher les prochaines actions autorisées
      if (data['nextAllowedActions'] != null) {
        _showNextActionsDialog(data['nextAllowedActions']);
      }
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Erreur: $e')),
      );
    } finally {
      setState(() => _isLoading = false);
    }
  }

  void _showNextActionsDialog(List<dynamic> nextActions) {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('Actions disponibles'),
        content: Text('Prochaines actions: ${nextActions.join(", ")}'),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text('OK'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Pointage')),
      body: Column(
        children: [
          Padding(
            padding: EdgeInsets.all(16),
            child: DropdownButton<String>(
              value: _checkinType,
              isExpanded: true,
              items: ['CHECKIN', 'CHECKOUT', 'BREAK_START', 'BREAK_END']
                  .map((type) => DropdownMenuItem(
                    value: type,
                    child: Text(type),
                  ))
                  .toList(),
              onChanged: (value) {
                if (value != null) {
                  setState(() => _checkinType = value);
                }
              },
            ),
          ),
          Expanded(
            child: Center(
              child: ElevatedButton(
                onPressed: _isLoading ? null : _handleCheckin,
                child: Text(_isLoading ? 'Enregistrement...' : 'Pointer'),
              ),
            ),
          ),
        ],
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
https://dolasolutions.com/api/docs

**Contact:**  
Équipe Dev Healthcarology

---

**Version:** 1.0  
**Date:** 18 octobre 2024  
**Status:** Production ready
