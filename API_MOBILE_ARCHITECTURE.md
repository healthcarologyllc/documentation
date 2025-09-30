# Architecture API Mobile - Application RH

## 📋 Vue d'ensemble

Les APIs mobiles suivent une architecture propre et modulaire basée sur API Platform 4.x avec le pattern State.

## 🏗️ Structure complète

```
src/
├── Api/
│   ├── Dto/                           # Data Transfer Objects
│   │   ├── LoginInputDto.php          # Input pour login
│   │   ├── LoginOutputDto.php         # Output pour login
│   │   ├── CheckinInputDto.php        # Input pour pointage
│   │   ├── CheckinOutputDto.php       # Output pour pointage
│   │   ├── PresenceOutputDto.php      # Output pour présences
│   │   └── PayslipOutputDto.php       # Output pour fiches de paie
│   │
│   ├── Resource/                      # 🆕 Définitions API Platform (séparées des entités)
│   │   ├── README.md                  # Documentation du dossier
│   │   ├── LoginResource.php          # Ressource login (autonome)
│   │   ├── PresenceResource.php       # Ressource présences (liée à Presence entity)
│   │   └── PayslipResource.php        # Ressource fiches de paie (liée à Payslip entity)
│   │
│   └── Service/                       # Services métier
│       └── CheckinValidationService.php  # Validation des règles de pointage
│
├── State/
│   ├── Provider/                      # Lecture (GET) - Récupération de données
│   │   ├── PresenceCollectionProvider.php   # Liste des présences (filtrée par agent)
│   │   ├── PresenceItemProvider.php         # Détail d'une présence (vérif ownership)
│   │   ├── PayslipCollectionProvider.php    # Liste des fiches (filtrée par agent)
│   │   └── PayslipItemProvider.php          # Détail d'une fiche (vérif ownership)
│   │
│   └── Processor/                     # Écriture (POST/PUT) - Traitement de données
│       ├── LoginProcessor.php         # Traitement login avec JWT
│       └── CheckinProcessor.php       # Traitement pointage avec validation
│
├── Security/
│   └── FlexibleUserProvider.php       # Login flexible (email/username/phoneNumber)
│
└── Entity/
    ├── Hr/Presence/
    │   └── Presence.php               # Entity Doctrine (SANS opérations API Platform)
    │
    └── Finance/
        └── Payslip.php                # Entity Doctrine (conserve routes admin)
```

## 🔄 Flux de données

### 1. Requête GET (Lecture)

```
┌─────────────┐
│   Client    │
│   Mobile    │
└──────┬──────┘
       │ GET /api/mobile/presences
       │ Authorization: Bearer TOKEN
       ▼
┌─────────────────────────┐
│  API Platform Router    │
│  (analyse la Resource)  │
└──────────┬──────────────┘
           │
           │ route vers
           ▼
┌──────────────────────────────┐
│  PresenceResource.php        │
│  - Définit les opérations    │
│  - Security checks           │
│  - Documentation OpenAPI     │
└──────────┬───────────────────┘
           │
           │ appelle
           ▼
┌──────────────────────────────┐
│  PresenceCollectionProvider  │
│  - Récupère user connecté    │
│  - Filtre par agent          │
│  - Applique filtres (date)   │
└──────────┬───────────────────┘
           │
           │ requête DB
           ▼
┌──────────────────────────────┐
│  Presence Repository         │
│  (Doctrine ORM)              │
└──────────┬───────────────────┘
           │
           │ retourne Presence[]
           ▼
┌──────────────────────────────┐
│  Provider transforme en      │
│  PresenceOutputDto[]         │
└──────────┬───────────────────┘
           │
           │ JSON
           ▼
┌─────────────┐
│   Client    │
│   reçoit    │
└─────────────┘
```

### 2. Requête POST (Écriture)

```
┌─────────────┐
│   Client    │
│   Mobile    │
└──────┬──────┘
       │ POST /api/mobile/checkin
       │ Body: {type, latitude, longitude}
       │ Authorization: Bearer TOKEN
       ▼
┌─────────────────────────┐
│  API Platform Router    │
└──────────┬──────────────┘
           │
           │ validation DTO
           ▼
┌──────────────────────────────┐
│  CheckinInputDto             │
│  - Validation Symfony        │
└──────────┬───────────────────┘
           │
           │ route vers Resource
           ▼
┌──────────────────────────────┐
│  PresenceResource.php        │
│  - Security checks           │
└──────────┬───────────────────┘
           │
           │ appelle
           ▼
┌──────────────────────────────┐
│  CheckinProcessor            │
│  - Récupère user connecté    │
│  - Appelle ValidationService │
│  - Valide business rules     │
│  - Crée entity Presence      │
│  - Persist + Flush           │
└──────────┬───────────────────┘
           │
           │ retourne
           ▼
┌──────────────────────────────┐
│  CheckinOutputDto            │
│  - Transforme résultat       │
└──────────┬───────────────────┘
           │
           │ JSON
           ▼
┌─────────────┐
│   Client    │
│   reçoit    │
└─────────────┘
```

## 🔐 Sécurité multi-couches

### Niveau 1 : Resource (API Platform)

```php
#[ApiResource(
    operations: [
        new GetCollection(
            security: 'is_granted("ROLE_USER")',  // ← Vérifie le rôle
            // ...
        )
    ]
)]
```

### Niveau 2 : Provider/Processor

```php
public function provide(...): array
{
    $user = $this->security->getUser();

    if (!$user) {
        throw new BadRequestException('Utilisateur non authentifié');
    }

    // Filtrer uniquement les données de l'utilisateur connecté
    return $this->repository->findBy(['agent' => $user]);
}
```

### Niveau 3 : Validation des propriétés

```php
public function provide(...): PresenceOutputDto
{
    $presence = $this->repository->find($id);

    // Vérifier que la ressource appartient à l'utilisateur
    if ($presence->getAgent() !== $user) {
        throw new BadRequestException('Accès non autorisé');
    }

    return new PresenceOutputDto(...);
}
```

## 📡 Routes disponibles

| Méthode | Endpoint | Resource | Provider/Processor | Sécurité |
|---------|----------|----------|-------------------|----------|
| POST | `/api/mobile/login` | LoginResource | LoginProcessor | Public |
| POST | `/api/mobile/checkin` | PresenceResource | CheckinProcessor | ROLE_USER |
| GET | `/api/mobile/presences` | PresenceResource | PresenceCollectionProvider | ROLE_USER + filter agent |
| GET | `/api/mobile/presences/{id}` | PresenceResource | PresenceItemProvider | ROLE_USER + ownership check |
| GET | `/api/mobile/payslips` | PayslipResource | PayslipCollectionProvider | ROLE_USER + filter agent |
| GET | `/api/mobile/payslips/{id}` | PayslipResource | PayslipItemProvider | ROLE_USER + ownership check |

## 🎯 Principes de conception

### 1. Séparation des préoccupations

- **Resources** : Définition des opérations API et documentation
- **Entities** : Modèle de données pur (Doctrine)
- **DTOs** : Contrats d'interface (Input/Output)
- **Providers** : Logique de lecture
- **Processors** : Logique d'écriture
- **Services** : Logique métier réutilisable

### 2. Sécurité par défaut

Toutes les routes sont sécurisées par :
1. JWT Authentication (sauf login)
2. Vérification du rôle ROLE_USER
3. Filtrage automatique par agent connecté
4. Validation de l'ownership sur les GET par ID

### 3. Documentation complète

Chaque opération inclut :
- Summary et description
- Tags pour grouper dans Swagger
- Exemples de requêtes/réponses
- Schémas de validation
- Codes d'erreur possibles

## 🔧 Configuration

### API Platform

`config/packages/api_platform.yaml` :
```yaml
api_platform:
    mapping:
        paths:
            - '%kernel.project_dir%/src/Entity'
            - '%kernel.project_dir%/src/Api/Resource'  # ← Resources séparées
```

### JWT Authentication

- Bundle : `lexik/jwt-authentication-bundle`
- Provider : `FlexibleUserProvider` (email/username/phoneNumber)
- Token dans header : `Authorization: Bearer {token}`

## 📊 Exemples de code

### Créer une nouvelle Resource

```php
// src/Api/Resource/MonNouvelleResource.php
<?php

namespace App\Api\Resource;

use ApiPlatform\Metadata\ApiResource;
use ApiPlatform\Metadata\GetCollection;
use App\Entity\MonEntite;
use App\State\Provider\MonEntiteProvider;

#[ApiResource(
    class: MonEntite::class,
    operations: [
        new GetCollection(
            uriTemplate: '/mobile/mon-endpoint',
            provider: MonEntiteProvider::class,
            security: 'is_granted("ROLE_USER")',
            extraProperties: [
                'openapi' => [
                    'summary' => 'Description courte',
                    'description' => 'Description détaillée',
                    'tags' => ['Mobile - Ma fonctionnalité'],
                    'security' => [['bearerAuth' => []]]
                ]
            ]
        )
    ]
)]
class MonNouvelleResource
{
}
```

### Créer un Provider sécurisé

```php
// src/State/Provider/MonEntiteProvider.php
<?php

namespace App\State\Provider;

use ApiPlatform\Metadata\Operation;
use ApiPlatform\State\ProviderInterface;
use Symfony\Bundle\SecurityBundle\Security;
use Symfony\Component\HttpFoundation\Exception\BadRequestException;

class MonEntiteProvider implements ProviderInterface
{
    public function __construct(
        private readonly Security $security,
        private readonly MonRepository $repository
    ) {}

    public function provide(Operation $operation, array $uriVariables = [], array $context = []): array
    {
        $user = $this->security->getUser();

        if (!$user) {
            throw new BadRequestException('Utilisateur non authentifié');
        }

        // ✅ TOUJOURS filtrer par utilisateur connecté
        return $this->repository->findBy(['user' => $user]);
    }
}
```

## 🧪 Tests

```bash
# Vérifier les routes
php bin/console debug:router | grep mobile

# Exporter OpenAPI spec
php bin/console api:openapi:export --yaml > openapi.yaml

# Clear cache
php bin/console cache:clear

# Tester avec curl
curl -X POST http://localhost/api/mobile/login \
  -H "Content-Type: application/json" \
  -d '{"username":"test@example.com","password":"password"}'

# Tester une route protégée
TOKEN="eyJ0eXAiOiJKV1QiLCJhbGciOi..."
curl -X GET http://localhost/api/mobile/presences \
  -H "Authorization: Bearer $TOKEN"
```

## 📚 Documentation

- **API Mobile** : `API_MOBILE_DOCUMENTATION.md`
- **README Quick Start** : `API_MOBILE_README.md`
- **Resources** : `src/Api/Resource/README.md`

## ✅ Checklist pour nouvelle fonctionnalité

- [ ] Créer les DTOs (Input/Output) dans `src/Api/Dto/`
- [ ] Créer la Resource dans `src/Api/Resource/`
- [ ] Créer le Provider ou Processor dans `src/State/`
- [ ] Ajouter les services métier si nécessaire dans `src/Api/Service/`
- [ ] Vérifier la sécurité (JWT + filtrage par user)
- [ ] Ajouter la documentation OpenAPI complète
- [ ] Tester avec `php bin/console debug:router`
- [ ] Documenter dans `API_MOBILE_DOCUMENTATION.md`
- [ ] Clear cache et tester avec curl
- [ ] Commit et push

## 🎓 Points clés à retenir

1. **Resources séparées** : Les entités restent propres, les Resources contiennent les opérations API
2. **Sécurité systématique** : Toujours filtrer par agent connecté dans les Providers
3. **DTOs obligatoires** : Ne jamais exposer directement les entités
4. **Documentation complète** : Chaque opération doit avoir sa doc OpenAPI
5. **State pattern** : Provider pour GET, Processor pour POST/PUT/PATCH/DELETE

## 🚀 Performance

- Providers utilisent `findBy()` avec critères précis
- Pas de N+1 queries (vérifier avec Symfony Profiler)
- DTOs minimaux (seulement les champs nécessaires)
- Cache Redis possible pour les données peu changeantes

## 🔗 Liens utiles

- [API Platform Docs](https://api-platform.com/docs/)
- [Symfony Security](https://symfony.com/doc/current/security.html)
- [Lexik JWT Bundle](https://github.com/lexik/LexikJWTAuthenticationBundle)
