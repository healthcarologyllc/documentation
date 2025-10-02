# Documentation API Mobile - Application RH

## Vue d'ensemble

Cette documentation décrit les APIs disponibles pour l'application mobile de gestion des ressources humaines. Les APIs sont construites avec API Platform et suivent les standards REST.

**Base URL:** `https://hr.healthcarology.org/api`

**Format de réponse:** JSON

**Authentication:** JWT (JSON Web Token)

---

## Table des matières

1. [Authentification](#1-authentification)
2. [Pointage](#2-pointage)
3. [Présences](#3-présences)
4. [Fiches de paie](#4-fiches-de-paie)
5. [Codes d'erreur](#5-codes-derreur)
6. [Exemples de flux](#6-exemples-de-flux)
7. [Bonnes pratiques d'intégration](#7-bonnes-pratiques-dintégration)
8. [Environnements](#8-environnements)
9. [Support](#9-support)
10. [Exemples de scénarios complets](#10-exemples-de-scénarios-complets)
11. [Changelog](#11-changelog)

---

## 1. Authentification

### 1.1 Connexion (Login)

Authentifie un agent et retourne un token JWT à utiliser pour toutes les autres requêtes.

**Endpoint:** `POST /api/mobile/login`

**Headers:**
```http
Content-Type: application/json
```

**Corps de la requête:**

Le champ `username` accepte **trois types d'identifiants** :
- Email de l'agent
- Username de l'agent
- Numéro de téléphone de l'agent

```json
{
  "username": "kabongo@example.com",
  "password": "password123"
}
```

**Exemples de requêtes valides:**

*Avec email:*
```json
{
  "username": "kabongo@example.com",
  "password": "password123"
}
```

*Avec username:*
```json
{
  "username": "jkabongo",
  "password": "password123"
}
```

*Avec numéro de téléphone:*
```json
{
  "username": "0991234567",
  "password": "password123"
}
```

**Exemple avec curl:**
```bash
curl -X POST https://hr.healthcarology.org/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "kabongo@example.com",
    "password": "password123"
  }'
```

**Réponse réussie (200 OK):**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "userId": 123,
  "email": "agent@example.com",
  "fullname": "Jean Kabongo Mukendi",
  "service": "Service Informatique",
  "roles": ["ROLE_USER", "ROLE_AGENT"],
  "agent": {
    "id": 123,
    "firstname": "Jean",
    "lastname": "Kabongo",
    "middlename": "Mukendi",
    "fullname": "Jean Kabongo Mukendi",
    "email": "agent@example.com",
    "phoneNumber": "099 123 4567",
    "matricule": "AG2025001",
    "service": {
      "id": 5,
      "name": "Service Informatique"
    },
    "company": {
      "id": 1,
      "name": "HEALTHCAROLOGY SARL"
    }
  }
}
```

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 400 | Identifiants invalides |
| 400 | Compte désactivé |

**Exemples d'erreurs:**

*Identifiants invalides (400):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Identifiants invalides"
}
```

*Champs manquants (422):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Validation failed",
  "violations": [
    {
      "propertyPath": "username",
      "message": "L'identifiant est requis"
    },
    {
      "propertyPath": "password",
      "message": "Le mot de passe est requis"
    }
  ]
}
```

**Exemple avec curl pour tester une erreur:**
```bash
# Requête avec identifiants invalides
curl -X POST https://hr.healthcarology.org/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "mauvais@email.com",
    "password": "mauvais_password"
  }'
```

---

## 2. Pointage

### 2.1 Effectuer un pointage

Enregistre un pointage (arrivée, sortie, début/fin de pause) avec géolocalisation.

**Endpoint:** `POST /api/mobile/checkin`

**Headers:**
```http
Content-Type: application/json
Authorization: Bearer {token}
```

**Corps de la requête:**
```json
{
  "type": "CHECKIN",
  "latitude": -4.322447,
  "longitude": 15.307045,
  "comment": "Pointage depuis l'entrée principale",
  "deviceInfo": "Android 12, Samsung Galaxy A52"
}
```

**Paramètres:**

| Champ | Type | Obligatoire | Description | Valeurs possibles |
|-------|------|-------------|-------------|-------------------|
| type | string | Oui | Type de pointage | `CHECKIN`, `CHECKOUT`, `BREAK_START`, `BREAK_END` |
| latitude | number | Oui | Latitude GPS | Coordonnée décimale |
| longitude | number | Oui | Longitude GPS | Coordonnée décimale |
| comment | string | Non | Commentaire optionnel | Texte libre |
| deviceInfo | string | Non | Informations sur l'appareil | Texte libre |

**Types de pointage:**

- **CHECKIN:** Pointage d'arrivée (début de journée)
- **BREAK_START:** Début de pause (2 pauses maximum par jour)
- **BREAK_END:** Fin de pause
- **CHECKOUT:** Pointage de sortie (fin de journée)

**Exemple avec curl:**
```bash
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..." \
  -d '{
    "type": "CHECKIN",
    "latitude": -4.322447,
    "longitude": 15.307045,
    "comment": "Pointage depuis l'\''entrée principale",
    "deviceInfo": "Android 12, Samsung Galaxy A52"
  }'
```

**Exemples de requêtes pour chaque type:**

*Pointage d'arrivée:*
```json
{
  "type": "CHECKIN",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

*Début de pause:*
```json
{
  "type": "BREAK_START",
  "latitude": -4.322447,
  "longitude": 15.307045,
  "comment": "Pause déjeuner"
}
```

*Fin de pause:*
```json
{
  "type": "BREAK_END",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

*Pointage de sortie:*
```json
{
  "type": "CHECKOUT",
  "latitude": -4.322447,
  "longitude": 15.307045,
  "comment": "Fin de journée"
}
```

**Réponse réussie (200 OK):**
```json
{
  "id": 456,
  "type": "CHECKIN",
  "timestamp": "2025-09-30T08:15:23+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Pointage d'arrivée autorisé",
  "nextAllowedActions": ["BREAK_START"]
}
```

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 400 | Type de pointage invalide |
| 400 | Vous avez déjà pointé votre arrivée aujourd'hui |
| 400 | Vous devez d'abord pointer votre arrivée |
| 400 | La première pause est autorisée après 4 heures de travail |
| 400 | La deuxième pause est autorisée à partir de 13h30 |
| 400 | Vous avez déjà pris vos deux pauses aujourd'hui |
| 400 | Vous avez déjà une pause en cours |
| 400 | Aucune pause en cours à terminer |
| 400 | Vous devez terminer votre pause avant de pointer la sortie |
| 400 | Le pointage de sortie est autorisé après 17h00 |
| 400 | Vous avez déjà pointé votre sortie aujourd'hui |
| 401 | Token d'authentification manquant ou invalide |

**Exemples d'erreurs détaillées:**

*Arrivée déjà pointée (400):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Vous avez déjà pointé votre arrivée aujourd'hui"
}
```

*Pause trop tôt (400):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "La première pause est autorisée après 4 heures de travail"
}
```

*Sortie trop tôt (400):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Le pointage de sortie est autorisé après 17h00"
}
```

*Pause non terminée (400):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Vous devez terminer votre pause avant de pointer la sortie"
}
```

*Token invalide (401):*
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "JWT Token not found"
}
```

**Exemples avec curl pour tester les erreurs:**

*Tenter de pointer deux fois l'arrivée:*
```bash
# Première arrivée (réussit)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"type": "CHECKIN", "latitude": -4.322447, "longitude": 15.307045}'

# Deuxième arrivée (échoue)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"type": "CHECKIN", "latitude": -4.322447, "longitude": 15.307045}'
```

*Tenter de sortir avant 17h00:*
```bash
# À 16h00 (échoue)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"type": "CHECKOUT", "latitude": -4.322447, "longitude": 15.307045}'
```

### 2.2 Règles de validation du pointage

#### a) Pointage d'arrivée (CHECKIN)
- ✅ Peut être effectué à tout moment
- ❌ Un seul pointage d'arrivée par jour

#### b) Première pause (BREAK_START)
- ✅ Autorisée après 4 heures de travail depuis l'arrivée
- ❌ Doit avoir pointé l'arrivée d'abord
- ❌ Pas de pause déjà en cours

#### c) Deuxième pause (BREAK_START)
- ✅ Autorisée à partir de 13h30
- ❌ La première pause doit être terminée

#### d) Fin de pause (BREAK_END)
- ✅ Possible uniquement s'il y a une pause en cours
- ❌ Doit avoir commencé une pause

#### e) Pointage de sortie (CHECKOUT)
- ✅ Autorisé après 17h00
- ❌ Doit avoir pointé l'arrivée
- ❌ Aucune pause en cours
- ❌ Un seul pointage de sortie par jour

### 2.3 Validation de la géolocalisation

**Important:** La validation de la géolocalisation est gérée **côté application mobile**.

L'application mobile doit :
1. Récupérer les coordonnées GPS du lieu d'affectation de l'agent
2. Calculer la distance entre la position actuelle et le lieu d'affectation
3. Autoriser le pointage uniquement si la distance est inférieure à 500 mètres
4. Afficher un message d'erreur si l'agent est trop éloigné

**Formule de calcul de distance (Haversine):**

```javascript
function calculateDistance(lat1, lon1, lat2, lon2) {
  const R = 6371000; // Rayon de la Terre en mètres
  const φ1 = lat1 * Math.PI / 180;
  const φ2 = lat2 * Math.PI / 180;
  const Δφ = (lat2 - lat1) * Math.PI / 180;
  const Δλ = (lon2 - lon1) * Math.PI / 180;

  const a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
            Math.cos(φ1) * Math.cos(φ2) *
            Math.sin(Δλ/2) * Math.sin(Δλ/2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));

  return R * c; // Distance en mètres
}

// Exemple d'utilisation
const distance = calculateDistance(
  -4.322447, 15.307045,  // Position actuelle
  -4.320000, 15.310000   // Lieu d'affectation
);

if (distance > 500) {
  alert("Vous êtes trop éloigné de votre lieu d'affectation");
  return;
}
```

---

## 3. Présences

### 3.1 Liste des présences de l'agent

Retourne toutes les présences de l'agent connecté.

**Endpoint:** `GET /api/mobile/presences`

**Headers:**
```http
Authorization: Bearer {token}
```

**Paramètres de requête (optionnels):**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| month | integer | Filtrer par mois (1-12) | `month=9` |
| year | integer | Filtrer par année | `year=2025` |

**Exemples de requêtes:**

```http
GET /api/mobile/presences
GET /api/mobile/presences?month=9&year=2025
GET /api/mobile/presences?year=2025
```

**Exemples avec curl:**

*Toutes les présences:*
```bash
curl -X GET https://hr.healthcarology.org/api/mobile/presences \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

*Filtrer par mois et année:*
```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/presences?month=9&year=2025" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

*Filtrer par année uniquement:*
```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/presences?year=2025" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

**Réponse réussie (200 OK):**
```json
[
  {
    "id": 456,
    "checkin": "2025-09-30T08:15:23+01:00",
    "checkout": "2025-09-30T17:30:45+01:00",
    "type": "CHECKIN",
    "status": "VALIDATED",
    "timestamp": "2025-09-30T08:15:23+01:00",
    "durationInMinutes": 555,
    "comment": "Journée normale",
    "locationValidated": true,
    "validationNote": "Présence validée automatiquement"
  },
  {
    "id": 457,
    "checkin": null,
    "checkout": null,
    "type": "BREAK_START",
    "status": "VALIDATED",
    "timestamp": "2025-09-30T12:30:00+01:00",
    "durationInMinutes": null,
    "comment": "Pause déjeuner",
    "locationValidated": true,
    "validationNote": null
  }
]
```

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 401 | Token d'authentification manquant ou invalide |

**Exemple d'erreur (401):**
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "JWT Token not found"
}
```

**Exemple avec curl pour tester sans token (erreur):**
```bash
curl -X GET https://hr.healthcarology.org/api/mobile/presences
# Réponse: 401 Unauthorized
```

### 3.2 Détails d'une présence

Retourne les détails d'une présence spécifique.

**Endpoint:** `GET /api/mobile/presences/{id}`

**Headers:**
```http
Authorization: Bearer {token}
```

**Paramètres d'URL:**

| Paramètre | Type | Description |
|-----------|------|-------------|
| id | integer | ID de la présence |

**Exemple de requête:**
```http
GET /api/mobile/presences/456
```

**Exemple avec curl:**
```bash
curl -X GET https://hr.healthcarology.org/api/mobile/presences/456 \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

**Réponse réussie (200 OK):**
```json
{
  "id": 456,
  "checkin": "2025-09-30T08:15:23+01:00",
  "checkout": "2025-09-30T17:30:45+01:00",
  "type": "CHECKIN",
  "status": "VALIDATED",
  "timestamp": "2025-09-30T08:15:23+01:00",
  "durationInMinutes": 555,
  "comment": "Journée normale",
  "locationValidated": true,
  "validationNote": "Présence validée automatiquement"
}
```

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 401 | Token d'authentification manquant ou invalide |
| 404 | Présence non trouvée |
| 400 | Accès non autorisé (présence d'un autre agent) |

**Exemple d'erreur (404):**
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Not Found"
}
```

**Exemple d'erreur (400 - accès non autorisé):**
```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Accès non autorisé à cette présence"
}
```

---

## 4. Fiches de paie

### 4.1 Liste des fiches de paie de l'agent

Retourne toutes les fiches de paie de l'agent connecté.

**Endpoint:** `GET /api/mobile/payslips`

**Headers:**
```http
Authorization: Bearer {token}
```

**Paramètres de requête (optionnels):**

| Paramètre | Type | Description | Exemple |
|-----------|------|-------------|---------|
| month | integer | Filtrer par mois (1-12) | `month=9` |
| year | integer | Filtrer par année | `year=2025` |

**Exemples de requêtes:**

```http
GET /api/mobile/payslips
GET /api/mobile/payslips?month=9&year=2025
GET /api/mobile/payslips?year=2025
```

**Exemples avec curl:**

*Toutes les fiches de paie:*
```bash
curl -X GET https://hr.healthcarology.org/api/mobile/payslips \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

*Filtrer par mois et année:*
```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/payslips?month=9&year=2025" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

*Filtrer par année uniquement:*
```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/payslips?year=2025" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

**Réponse réussie (200 OK):**
```json
[
  {
    "id": "01923456-7890-7c7e-9b4f-123456789abc",
    "agentName": "Jean Kabongo Mukendi",
    "matricule": "AG2025001",
    "periodMonth": 9,
    "periodYear": 2025,
    "baseSalary": 500000.0,
    "grossSalary": 550000.0,
    "netSalary": 425000.0,
    "workedHours": 176,
    "status": "PAID",
    "calculationDetails": {
      "presenceMinutes": 10560,
      "timesheetMinutes": 0,
      "totalMinutes": 10560,
      "baseSalary": 500000,
      "bonuses": [
        {
          "type": "Transport",
          "amount": 30000
        },
        {
          "type": "Prime de rendement",
          "amount": 20000
        }
      ],
      "deductions": [
        {
          "type": "INPP (5%)",
          "amount": 25000
        },
        {
          "type": "CNSS (3.5%)",
          "amount": 17500
        },
        {
          "type": "IPR",
          "amount": 82500
        }
      ]
    },
    "bonuses": [
      {
        "type": "Transport",
        "amount": 30000
      },
      {
        "type": "Prime de rendement",
        "amount": 20000
      }
    ],
    "deductions": [
      {
        "type": "INPP (5%)",
        "amount": 25000
      },
      {
        "type": "CNSS (3.5%)",
        "amount": 17500
      },
      {
        "type": "IPR",
        "amount": 82500
      }
    ],
    "pdfUrl": "https://hr.healthcarology.org/finance/payroll/01923456-7890-7c7e-9b4f-123456789abc/pdf"
  }
]
```

**Status de la fiche de paie:**

| Status | Description |
|--------|-------------|
| DRAFT | Brouillon (non finalisé) |
| VALIDATED | Validé par la direction |
| PAID | Payé à l'agent |

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 401 | Token d'authentification manquant ou invalide |

### 4.2 Détails d'une fiche de paie

Retourne les détails d'une fiche de paie spécifique.

**Endpoint:** `GET /api/mobile/payslips/{id}`

**Headers:**
```http
Authorization: Bearer {token}
```

**Paramètres d'URL:**

| Paramètre | Type | Description |
|-----------|------|-------------|
| id | string | ID UUID de la fiche de paie |

**Exemple de requête:**
```http
GET /api/mobile/payslips/01923456-7890-7c7e-9b4f-123456789abc
```

**Exemple avec curl:**
```bash
curl -X GET https://hr.healthcarology.org/api/mobile/payslips/01923456-7890-7c7e-9b4f-123456789abc \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
```

**Réponse réussie (200 OK):**
```json
{
  "id": "01923456-7890-7c7e-9b4f-123456789abc",
  "agentName": "Jean Kabongo Mukendi",
  "matricule": "AG2025001",
  "periodMonth": 9,
  "periodYear": 2025,
  "baseSalary": 500000.0,
  "grossSalary": 550000.0,
  "netSalary": 425000.0,
  "workedHours": 176,
  "status": "PAID",
  "calculationDetails": {
    "presenceMinutes": 10560,
    "timesheetMinutes": 0,
    "totalMinutes": 10560,
    "baseSalary": 500000,
    "bonuses": [
      {
        "type": "Transport",
        "amount": 30000
      },
      {
        "type": "Prime de rendement",
        "amount": 20000
      }
    ],
    "deductions": [
      {
        "type": "INPP (5%)",
        "amount": 25000
      },
      {
        "type": "CNSS (3.5%)",
        "amount": 17500
      },
      {
        "type": "IPR",
        "amount": 82500
      }
    ]
  },
  "bonuses": [
    {
      "type": "Transport",
      "amount": 30000
    },
    {
      "type": "Prime de rendement",
      "amount": 20000
    }
  ],
  "deductions": [
    {
      "type": "INPP (5%)",
      "amount": 25000
    },
    {
      "type": "CNSS (3.5%)",
      "amount": 17500
    },
    {
      "type": "IPR",
      "amount": 82500
    }
  ],
  "pdfUrl": "https://hr.healthcarology.org/finance/payroll/01923456-7890-7c7e-9b4f-123456789abc/pdf"
}
```

**Réponses d'erreur:**

| Code | Description |
|------|-------------|
| 401 | Token d'authentification manquant ou invalide |
| 404 | Fiche de paie non trouvée |
| 400 | Accès non autorisé (fiche de paie d'un autre agent) |

### 4.3 Télécharger le PDF d'une fiche de paie

Le lien PDF est fourni dans le champ `pdfUrl` de chaque fiche de paie.

**Endpoint:** Utiliser l'URL fournie dans `pdfUrl`

**Headers:**
```http
Authorization: Bearer {token}
```

**Exemple:**
```http
GET https://hr.healthcarology.org/finance/payroll/01923456-7890-7c7e-9b4f-123456789abc/pdf
```

**Exemple avec curl (téléchargement):**
```bash
curl -X GET "https://hr.healthcarology.org/finance/payroll/01923456-7890-7c7e-9b4f-123456789abc/pdf" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..." \
  -o fiche_paie_septembre_2025.pdf
```

**Exemple avec curl (affichage dans le navigateur):**
```bash
# Récupérer l'URL du PDF depuis l'API
curl -X GET https://hr.healthcarology.org/api/mobile/payslips/01923456-7890-7c7e-9b4f-123456789abc \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..." \
  | jq -r '.pdfUrl'

# Ensuite ouvrir cette URL dans le navigateur avec le token JWT
```

**Réponse:** Fichier PDF

**Content-Type:** `application/pdf`

---

## 5. Codes d'erreur

### Codes HTTP standards

| Code | Signification | Description |
|------|---------------|-------------|
| 200 | OK | Requête réussie |
| 201 | Created | Ressource créée avec succès |
| 400 | Bad Request | Requête invalide (données manquantes ou incorrectes) |
| 401 | Unauthorized | Non authentifié (token manquant ou invalide) |
| 403 | Forbidden | Accès interdit (permissions insuffisantes) |
| 404 | Not Found | Ressource non trouvée |
| 422 | Unprocessable Entity | Validation échouée |
| 500 | Internal Server Error | Erreur serveur |

### Format des erreurs

Toutes les erreurs suivent le format API Problem (RFC 7807):

```json
{
  "type": "https://tools.ietf.org/html/rfc2616#section-10",
  "title": "An error occurred",
  "detail": "Message d'erreur détaillé",
  "violations": [
    {
      "propertyPath": "email",
      "message": "L'email est requis"
    }
  ]
}
```

---

## 6. Exemples de flux

### 6.1 Flux complet d'une journée de travail

#### Étape 1: Connexion
```http
POST /api/mobile/login
{
  "email": "agent@example.com",
  "password": "password123"
}
```

**Réponse:**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "userId": 123,
  "fullname": "Jean Kabongo Mukendi",
  ...
}
```

**Action:** Stocker le token pour toutes les requêtes suivantes.

---

#### Étape 2: Pointage d'arrivée (8h15)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "CHECKIN",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

**Réponse:**
```json
{
  "id": 456,
  "type": "CHECKIN",
  "timestamp": "2025-09-30T08:15:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Pointage d'arrivée autorisé",
  "nextAllowedActions": []
}
```

**Note:** `nextAllowedActions` est vide car la première pause n'est pas encore autorisée (après 4h).

---

#### Étape 3: Début de première pause (12h30 - après 4h de travail)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "BREAK_START",
  "latitude": -4.322447,
  "longitude": 15.307045,
  "comment": "Pause déjeuner"
}
```

**Réponse:**
```json
{
  "id": 457,
  "type": "BREAK_START",
  "timestamp": "2025-09-30T12:30:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Début de pause autorisé",
  "nextAllowedActions": ["BREAK_END"]
}
```

---

#### Étape 4: Fin de première pause (13h00)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "BREAK_END",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

**Réponse:**
```json
{
  "id": 458,
  "type": "BREAK_END",
  "timestamp": "2025-09-30T13:00:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Fin de pause autorisée",
  "nextAllowedActions": []
}
```

---

#### Étape 5: Début de deuxième pause (14h00)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "BREAK_START",
  "latitude": -4.322447,
  "longitude": 15.307045,
  "comment": "Pause café"
}
```

**Réponse:**
```json
{
  "id": 459,
  "type": "BREAK_START",
  "timestamp": "2025-09-30T14:00:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Début de pause autorisé",
  "nextAllowedActions": ["BREAK_END"]
}
```

---

#### Étape 6: Fin de deuxième pause (14h15)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "BREAK_END",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

**Réponse:**
```json
{
  "id": 460,
  "type": "BREAK_END",
  "timestamp": "2025-09-30T14:15:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Fin de pause autorisée",
  "nextAllowedActions": []
}
```

---

#### Étape 7: Pointage de sortie (17h30)
```http
POST /api/mobile/checkin
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
{
  "type": "CHECKOUT",
  "latitude": -4.322447,
  "longitude": 15.307045
}
```

**Réponse:**
```json
{
  "id": 461,
  "type": "CHECKOUT",
  "timestamp": "2025-09-30T17:30:00+01:00",
  "status": "PENDING",
  "locationValidated": true,
  "message": "Pointage de sortie autorisé",
  "nextAllowedActions": []
}
```

---

### 6.2 Consultation de l'historique des présences

```http
GET /api/mobile/presences?month=9&year=2025
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
```

**Réponse:** Liste de toutes les présences du mois de septembre 2025.

---

### 6.3 Consultation des fiches de paie

```http
GET /api/mobile/payslips
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
```

**Réponse:** Liste de toutes les fiches de paie de l'agent.

---

## 7. Bonnes pratiques d'intégration

### 7.1 Gestion du token JWT

1. **Stockage sécurisé:** Utiliser le stockage sécurisé de l'appareil (Keychain iOS, Keystore Android)
2. **Expiration:** Le token expire après 24 heures. Implémenter un mécanisme de rafraîchissement automatique
3. **Déconnexion:** Supprimer le token lors de la déconnexion

### 7.2 Gestion des erreurs

1. **Codes HTTP:** Toujours vérifier le code de statut HTTP
2. **Messages d'erreur:** Afficher les messages d'erreur retournés par l'API
3. **Retry:** Implémenter une logique de retry pour les erreurs 500

### 7.3 Performance

1. **Cache:** Mettre en cache les données qui changent peu (informations de l'agent)
2. **Pagination:** Utiliser les filtres de date pour limiter les données récupérées
3. **Images:** Utiliser la mise en cache pour les images de profil

### 7.4 Sécurité

1. **HTTPS:** Toujours utiliser HTTPS en production
2. **Validation:** Valider les données côté client avant envoi
3. **Permissions:** Demander les permissions GPS uniquement quand nécessaire

### 7.5 Géolocalisation

1. **Précision:** Utiliser la meilleure précision disponible pour le GPS
2. **Timeout:** Implémenter un timeout si la géolocalisation prend trop de temps
3. **Fallback:** Prévoir un mode dégradé si le GPS n'est pas disponible

---

## 8. Environnements

### Production
- **Base URL:** `https://hr.healthcarology.org/api`
- **Documentation API:** `https://hr.healthcarology.org/api/docs`

### Développement
- **Base URL:** `https://dev-hr.healthcarology.org/api`
- **Documentation API:** `https://dev-hr.healthcarology.org/api/docs`

---

## 9. Support

Pour toute question ou problème:
- **Email:** support@healthcarology.org
- **Slack:** #api-mobile-support

---

## 10. Exemples de scénarios complets

### 10.1 Scénario: Premier lancement de l'application

**Étape 1: L'utilisateur ouvre l'app**
```bash
# L'app vérifie s'il y a un token stocké
# Si non, afficher l'écran de connexion
```

**Étape 2: Connexion**
```bash
curl -X POST https://hr.healthcarology.org/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "kabongo@example.com",
    "password": "password123"
  }'
```

**Réponse:**
```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "agent": {
    "id": 123,
    "fullname": "Jean Kabongo Mukendi",
    ...
  }
}
```

**Étape 3: Stocker le token**
```javascript
// Dans l'app mobile (exemple React Native)
import AsyncStorage from '@react-native-async-storage/async-storage';

const storeToken = async (token) => {
  try {
    await AsyncStorage.setItem('jwt_token', token);
  } catch (e) {
    console.error('Erreur lors du stockage du token', e);
  }
};
```

**Étape 4: Naviguer vers l'écran principal**

---

### 10.2 Scénario: Pointage d'arrivée avec validation GPS

**Étape 1: Récupérer la position GPS**
```javascript
// React Native
import Geolocation from '@react-native-community/geolocation';

Geolocation.getCurrentPosition(
  (position) => {
    const { latitude, longitude } = position.coords;
    // Passer à l'étape 2
  },
  (error) => {
    console.error(error);
  },
  { enableHighAccuracy: true, timeout: 15000, maximumAge: 10000 }
);
```

**Étape 2: Valider la distance (côté app)**
```javascript
// Calculer la distance entre position actuelle et lieu de travail
const distance = calculateDistance(
  currentLatitude, currentLongitude,
  workplaceLatitude, workplaceLongitude
);

if (distance > 500) {
  Alert.alert(
    'Erreur de géolocalisation',
    'Vous êtes trop éloigné de votre lieu de travail (distance: ' + Math.round(distance) + 'm)'
  );
  return;
}
```

**Étape 3: Envoyer le pointage**
```bash
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "type": "CHECKIN",
    "latitude": -4.322447,
    "longitude": 15.307045,
    "deviceInfo": "Android 12, Samsung Galaxy A52"
  }'
```

**Étape 4: Afficher confirmation**
```javascript
// Dans l'app
Alert.alert('Succès', 'Pointage d\'arrivée enregistré à ' + timestamp);
```

---

### 10.3 Scénario: Affichage de la fiche de paie du mois en cours

**Étape 1: Récupérer le mois et l'année en cours**
```javascript
const now = new Date();
const currentMonth = now.getMonth() + 1; // 1-12
const currentYear = now.getFullYear();
```

**Étape 2: Requête API**
```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/payslips?month=9&year=2025" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Étape 3: Afficher les données**
```javascript
// Dans l'app
{payslips.map((payslip) => (
  <View key={payslip.id}>
    <Text>Période: {payslip.periodMonth}/{payslip.periodYear}</Text>
    <Text>Salaire brut: {payslip.grossSalary} FC</Text>
    <Text>Salaire net: {payslip.netSalary} FC</Text>
    <Button title="Voir PDF" onPress={() => openPDF(payslip.pdfUrl)} />
  </View>
))}
```

**Étape 4: Télécharger/Ouvrir le PDF**
```javascript
// Utiliser react-native-pdf ou un navigateur web
import Pdf from 'react-native-pdf';

<Pdf
  source={{ uri: payslip.pdfUrl, headers: { Authorization: 'Bearer ' + token }}}
  onLoadComplete={(numberOfPages) => {
    console.log(`PDF chargé avec ${numberOfPages} pages`);
  }}
/>
```

---

### 10.4 Scénario: Gestion de l'expiration du token

**Intercepteur HTTP (exemple avec Axios):**
```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://hr.healthcarology.org/api',
});

// Ajouter le token à chaque requête
api.interceptors.request.use(async (config) => {
  const token = await AsyncStorage.getItem('jwt_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Gérer les erreurs 401 (token expiré)
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response && error.response.status === 401) {
      // Token expiré - rediriger vers login
      await AsyncStorage.removeItem('jwt_token');
      navigation.navigate('Login');
    }
    return Promise.reject(error);
  }
);

export default api;
```

---

### 10.5 Scénario: Affichage du résumé mensuel

**Requête combinée:**
```bash
# Récupérer toutes les présences du mois
curl -X GET "https://hr.healthcarology.org/api/mobile/presences?month=9&year=2025" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**Traitement dans l'app:**
```javascript
// Calculer les statistiques
const stats = {
  totalDays: presences.filter(p => p.type === 'CHECKIN').length,
  totalHours: presences.reduce((sum, p) => sum + (p.durationInMinutes || 0), 0) / 60,
  totalBreaks: presences.filter(p => p.type === 'BREAK_START').length,
  lateArrivals: presences.filter(p => {
    if (p.type === 'CHECKIN') {
      const hour = new Date(p.timestamp).getHours();
      return hour > 8; // Après 8h
    }
    return false;
  }).length
};

// Afficher
<View>
  <Text>Jours travaillés: {stats.totalDays}</Text>
  <Text>Heures totales: {stats.totalHours.toFixed(2)}h</Text>
  <Text>Pauses prises: {stats.totalBreaks}</Text>
  <Text>Retards: {stats.lateArrivals}</Text>
</View>
```

---

## 11. Changelog

### Version 1.0.0 (2025-09-30)
- Première version de l'API
- Authentification JWT flexible (email, username, phoneNumber)
- Pointage avec géolocalisation
- Consultation des présences avec filtres
- Consultation des fiches de paie avec PDF
- Documentation complète avec exemples curl et scénarios d'intégration
