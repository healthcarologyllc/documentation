# API Mobile - Application RH

Documentation complète des APIs mobiles pour l'application de gestion des ressources humaines.

## 📚 Documentation

Consultez la documentation complète dans [`API_MOBILE_DOCUMENTATION.md`](./API_MOBILE_DOCUMENTATION.md)

## 🚀 Démarrage rapide

### 1. Authentification

```bash
curl -X POST https://hr.healthcarology.org/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "votre.email@example.com",
    "password": "votre_password"
  }'
```

Le champ `username` accepte:
- ✅ Email
- ✅ Username
- ✅ Numéro de téléphone

### 2. Pointage

```bash
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "type": "CHECKIN",
    "latitude": -4.322447,
    "longitude": 15.307045
  }'
```

### 3. Liste des présences

```bash
curl -X GET "https://hr.healthcarology.org/api/mobile/presences?month=9&year=2025" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 4. Fiches de paie

```bash
curl -X GET https://hr.healthcarology.org/api/mobile/payslips \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 📋 Endpoints disponibles

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| POST | `/api/mobile/login` | Authentification |
| POST | `/api/mobile/checkin` | Pointage (arrivée/sortie/pause) |
| GET | `/api/mobile/presences` | Liste des présences |
| GET | `/api/mobile/presences/{id}` | Détails d'une présence |
| GET | `/api/mobile/payslips` | Liste des fiches de paie |
| GET | `/api/mobile/payslips/{id}` | Détails d'une fiche de paie |

## 🔐 Authentification

Toutes les routes (sauf `/login`) nécessitent un token JWT dans le header:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
```

## 🎯 Types de pointage

| Type | Description | Règles |
|------|-------------|--------|
| `CHECKIN` | Arrivée | Une seule fois par jour |
| `BREAK_START` | Début de pause | 1ère pause après 4h, 2ème après 13h30, max 2/jour |
| `BREAK_END` | Fin de pause | Doit avoir une pause en cours |
| `CHECKOUT` | Sortie | Après 17h00, aucune pause en cours |

## 📱 Architecture de l'implémentation

```
src/
├── Api/
│   ├── Dto/                    # Data Transfer Objects
│   │   ├── LoginInputDto.php
│   │   ├── LoginOutputDto.php
│   │   ├── CheckinInputDto.php
│   │   ├── CheckinOutputDto.php
│   │   ├── PresenceOutputDto.php
│   │   └── PayslipOutputDto.php
│   ├── Resource/               # Resources autonomes
│   │   └── LoginResource.php
│   └── Service/                # Services métier
│       └── CheckinValidationService.php
├── State/
│   ├── Provider/               # Lecture (GET)
│   │   ├── PresenceCollectionProvider.php
│   │   ├── PresenceItemProvider.php
│   │   ├── PayslipCollectionProvider.php
│   │   └── PayslipItemProvider.php
│   └── Processor/              # Écriture (POST/PUT)
│       ├── LoginProcessor.php
│       └── CheckinProcessor.php
├── Security/
│   └── FlexibleUserProvider.php  # Login flexible
└── Entity/
    ├── Hr/Presence/Presence.php  # Entity avec #[ApiResource]
    └── Finance/Payslip.php       # Entity avec #[ApiResource]
```

## 🧪 Tests avec curl

### Scénario complet d'une journée

```bash
# 1. Login
TOKEN=$(curl -s -X POST https://hr.healthcarology.org/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{"username":"agent@example.com","password":"password123"}' \
  | jq -r '.token')

# 2. Pointage d'arrivée (8h15)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"type":"CHECKIN","latitude":-4.322447,"longitude":15.307045}'

# 3. Première pause (12h30)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"type":"BREAK_START","latitude":-4.322447,"longitude":15.307045}'

# 4. Fin de pause (13h00)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"type":"BREAK_END","latitude":-4.322447,"longitude":15.307045}'

# 5. Sortie (17h30)
curl -X POST https://hr.healthcarology.org/api/mobile/checkin \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"type":"CHECKOUT","latitude":-4.322447,"longitude":15.307045}'

# 6. Consulter mes présences du mois
curl -X GET "https://hr.healthcarology.org/api/mobile/presences?month=9&year=2025" \
  -H "Authorization: Bearer $TOKEN"

# 7. Consulter mes fiches de paie
curl -X GET https://hr.healthcarology.org/api/mobile/payslips \
  -H "Authorization: Bearer $TOKEN"
```

## 🌍 Validation géolocalisation

**Important:** La validation de distance (max 500m) est gérée **côté application mobile**.

```javascript
// Formule de Haversine pour calculer la distance
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

// Validation
const distance = calculateDistance(
  currentLat, currentLon,
  workplaceLat, workplaceLon
);

if (distance > 500) {
  alert("Vous êtes trop éloigné de votre lieu de travail");
  return;
}
```

## 📊 Codes de statut

| Code | Signification |
|------|---------------|
| 200 | Succès |
| 400 | Requête invalide |
| 401 | Non authentifié |
| 404 | Ressource non trouvée |
| 422 | Validation échouée |
| 500 | Erreur serveur |

## 🔗 Liens utiles

- **Documentation complète:** [`API_MOBILE_DOCUMENTATION.md`](./API_MOBILE_DOCUMENTATION.md)
- **API Docs (Swagger):** `https://hr.healthcarology.org/api/docs`
- **Base URL Production:** `https://hr.healthcarology.org/api`
- **Base URL Dev:** `https://dev-hr.healthcarology.org/api`

## 📞 Support

- **Email:** support@healthcarology.org
- **Slack:** #api-mobile-support

## 📝 Version

**v1.0.0** - 2025-09-30

Première version de l'API mobile avec authentification JWT, pointage avec géolocalisation, consultation des présences et fiches de paie.
