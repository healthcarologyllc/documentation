# Documentation de l'API - Ressource `User`

Cette documentation décrit les points d'accès (endpoints) disponibles pour la gestion des utilisateurs (patients et personnel) de l'application.

**URL de base :** `/api`

## Notes Générales

### Authentification

La plupart des endpoints nécessitent une authentification. Pour vous authentifier, vous devez d'abord obtenir un token JWT en envoyant une requête `POST` sur l'endpoint `/api/login` avec les identifiants de l'utilisateur.

**Exemple de requête de login :**
```json
{
  "username": "email@example.com",
  "password": "your_password"
}
```

Une fois le token obtenu, vous devez l'inclure dans l'en-tête de toutes vos requêtes suivantes :
`Authorization: Bearer VOTRE_TOKEN_JWT`

### Formats de Réponse

-   **Succès :** Les réponses de succès sont généralement au format `JSON` avec un code de statut `200 OK` (pour GET), `201 Created` (pour POST), `200 OK` (pour PUT), ou `204 No Content` (pour DELETE).
-   **Erreur :** Les erreurs de validation retournent un code `400 Bad Request` ou `422 Unprocessable Entity` avec les détails des erreurs. Les erreurs d'authentification ou de permission retournent des codes `401 Unauthorized` ou `403 Forbidden`.

---

## 1. Gestion du Compte et Authentification

### 1.1 Créer un compte utilisateur
Crée un nouveau dossier patient ou un compte utilisateur.

-   **Endpoint :** `POST /api/users`
-   **Permissions :** Ouvert à tous (Public).
-   **Données d'Entrée (Request Body) :**
    ```json
    {
      "email": "nouveau.patient@email.com",
      "plainPassword": "password123",
      "name": "NOM",
      "lastname": "Postnom",
      "firstname": "Prénom",
      "sexe": "Masculin",
      "phoneNumber": "0812345678",
      "birthday": "1990-01-15T00:00:00+00:00"
    }
    ```
-   **Réponse de Succès (`201 Created`) :** Un objet JSON représentant l'utilisateur nouvellement créé.

### 1.2 Changer le mot de passe
Permet à un utilisateur authentifié de changer son propre mot de passe.

-   **Endpoint :** `POST /api/users/{id}/reset-password`
-   **Permissions :** L'utilisateur doit être authentifié et ne peut modifier que son propre mot de passe (`{id}` doit être son propre ID).
-   **Données d'Entrée (Request Body) :**
    ```json
    {
      "oldPassword": "ancien_mot_de_passe",
      "newPassword": "nouveau_mot_de_passe_securise"
    }
    ```
-   **Réponse de Succès (`200 OK`) :**
    ```json
    {
      "message": "Mot de passe réinitialisé avec succès."
    }
    ```

---

## 2. Gestion des Utilisateurs (CRUD)

### 2.1 Récupérer les informations d'un utilisateur (Profil)
Récupère les détails complets d'un utilisateur spécifique.

-   **Endpoint :** `GET /api/users/{id}`
-   **Permissions :** Administrateurs ou l'utilisateur lui-même.
-   **Réponse de Succès (`200 OK`) :** Un objet JSON contenant les informations détaillées de l'utilisateur, y compris son adresse, ses consultations, etc. (selon les groupes de sérialisation).

### 2.2 Mettre à jour un utilisateur
Met à jour les informations d'un profil utilisateur. Utilise un contrôleur personnalisé pour une logique avancée (ex: gestion de l'upload d'image de profil).

-   **Endpoint :** `PUT /api/users/{id}`
-   **Permissions :** Administrateurs ou l'utilisateur lui-même.
-   **Données d'Entrée (Request Body) :** Un objet JSON contenant les champs à mettre à jour (ex: `name`, `phoneNumber`, etc.).
-   **Réponse de Succès (`200 OK`) :** L'objet JSON de l'utilisateur mis à jour.

### 2.3 Lister les utilisateurs
Récupère une liste paginée de tous les utilisateurs.

-   **Endpoint :** `GET /api/users`
-   **Permissions :** Administrateurs (`ROLE_ADMIN`) ou Support Technique (`ROLE_HELPDESK`).
-   **Réponse de Succès (`200 OK`) :** Une collection d'objets utilisateurs.

### 2.4 Supprimer un utilisateur
Supprime un compte utilisateur.

-   **Endpoint :** `DELETE /api/users/{id}`
-   **Permissions :** Administrateurs ou l'utilisateur lui-même.
-   **Réponse de Succès (`204 No Content`) :** Aucune donnée n'est retournée.

---

## 3. Opérations Spécifiques au Patient

Ces endpoints permettent d'accéder à des données médicales spécifiques liées à un patient.

### 3.1 Résumé des Signes Vitaux
Récupère un résumé des derniers signes vitaux enregistrés pour un patient.

-   **Endpoint :** `GET /api/users/{id}/vital_signs_summary`
-   **Permissions :** Personnel infirmier (`ROLE_NURSE`), le patient lui-même, ou tout professionnel de santé.
-   **Réponse de Succès (`200 OK`) :** Un objet JSON contenant les données agrégées (format défini par le groupe `user:vitals:summary`).

### 3.2 Dernière Consultation
Récupère les informations de la dernière consultation du patient.

-   **Endpoint :** `GET /api/users/{id}/last_consultation`
-   **Permissions :** Tout professionnel de santé ou le patient lui-même.
-   **Réponse de Succès (`200 OK`) :** Un objet JSON représentant l'entité `Consultation`.

### 3.3 Ajouter un suivi de douleur
Enregistre un nouveau relevé de douleur pour le patient.

-   **Endpoint :** `POST /api/users/{id}/pain_logs`
-   **Permissions :** Le patient lui-même.
-   **Données d'Entrée (Request Body) :**
    ```json
    {
      "niveau": 5,
      "description": "Douleur aiguë au niveau du dos."
    }
    ```
-   **Réponse de Succès (`201 Created`) :** L'objet `PainLog` nouvellement créé.

### 3.4 Demander un rendez-vous
Permet à un utilisateur de demander un rendez-vous.

-   **Endpoint :** `POST /api/users/{id}/request_appointment`
-   **Permissions :** Le patient lui-même ou un membre du personnel (`ROLE_STAFF`).
-   **Données d'Entrée (Request Body) :** Un DTO `AppointmentRequestDto` (les champs exacts doivent être précisés).
-   **Réponse de Succès (`201 Created`) :** L'objet `Appointment` nouvellement créé.

---

## 4. Opérations Techniques

### 4.1 Consulter le portefeuille
Endpoint réservé aux développeurs pour consulter le portefeuille d'un utilisateur.

-   **Endpoint :** `GET /api/users/wallet/{id}`
-   **Permissions :** Développeurs (`ROLE_DEVELOPER`) ou l'utilisateur lui-même.
-   **Réponse de Succès (`200 OK`) :** Un objet JSON représentant le `Wallet` de l'utilisateur.
