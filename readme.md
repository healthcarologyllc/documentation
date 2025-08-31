Of course. Here is the fully detailed API documentation in English, structured with a table of contents and detailed descriptions for each endpoint as requested.

---

### API Documentation - `User` & `Auth` Resources

This documentation outlines the available endpoints for managing users and authentication for the application.

**Base URL:** `/api`

## Table of Contents
- [Table of Contents](#table-of-contents)
- [General Notes](#general-notes)
  - [Authentication](#authentication)
  - [Response Formats](#response-formats)
- [Authentication \& Account Lifecycle](#authentication--account-lifecycle)
  - [POST `/api/auth/register`](#post-apiauthregister)
  - [POST `/api/login`](#post-apilogin)
  - [POST `/api/auth/logout`](#post-apiauthlogout)
  - [POST `/api/users/{id}/change-password`](#post-apiusersidchange-password)
  - [POST `/api/auth/forgot-password`](#post-apiauthforgot-password)
  - [POST `/api/auth/reset-password`](#post-apiauthreset-password)
  - [POST `/api/auth/verify-email`](#post-apiauthverify-email)
  - [POST `/api/auth/resend-verification`](#post-apiauthresend-verification)
- [User Profile Management](#user-profile-management)
  - [GET `/api/users/profil`](#get-apiusersprofil)
  - [GET `/api/users/{id}`](#get-apiusersid)
  - [PUT `/api/users/{id}`](#put-apiusersid)
  - [GET `/api/users`](#get-apiusers)
  - [DELETE `/api/users/{id}`](#delete-apiusersid)
- [Special Case: Mandatory Password Change](#special-case-mandatory-password-change)
  - [Frontend Workflow](#frontend-workflow)
- [Full Route List Summary](#full-route-list-summary)
  - [Authentication \& Account Management](#authentication--account-management)
  - [User \& Profile](#user--profile)
  - [Patient-Specific Medical Data](#patient-specific-medical-data)
  - [Appointments](#appointments)
  - [Technical \& Other](#technical--other)
  - [Address \& Geolocation Endpoints](#address--geolocation-endpoints)
  - [Healthcare Zones Endpoints](#healthcare-zones-endpoints)
    - [Detailed Examples](#detailed-examples)
    - [GET `/api/countries`](#get-apicountries)
    - [GET `/api/countries/{id}/provinces`](#get-apicountriesidprovinces)

## General Notes

### Authentication

Most endpoints require authentication. To authenticate, you must first obtain a JWT by sending a `POST` request to the `/api/login` endpoint with the user's credentials.

**Example Login Request:**
```json
// The 'username' field can be either an email or a phone number.
{
  "username": "user@example.com",
  "password": "your_password"
}
```

Once you have the token, you must include it in the header of all subsequent requests:
`Authorization: Bearer YOUR_JWT_TOKEN`

### Response Formats

-   **Success:** Successful responses are typically in `JSON` format with a status code of `200 OK` (for GET/PUT), `201 Created` (for POST), or `204 No Content` (for DELETE).
-   **Error:** Validation errors return a `422 Unprocessable Entity` code with error details. Authentication or permission errors return `401 Unauthorized` or `403 Forbidden` codes.

All protected endpoints require a **JSON Web Token (JWT)** to be sent in the `Authorization` header.

**Step 1: Obtain a Token**
Send a `POST` request to the `/api/login` endpoint with the user's credentials.

-   **Request Body:**
    ```json
    // The 'username' field can be either an email or a phone number.
    {
      "username": "user@example.com",
      "password": "your_password"
    }
    ```
-   **Success Response:** The API will return a JWT.
    ```json
    {
      "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpYXQiOjE2..."
    }
    ```

**Step 2: Use the Token**
For every subsequent request to a protected endpoint, you must include the token in the `Authorization` header using the **`Bearer`** scheme.

-   **Header Format:**
    `Authorization: Bearer <YOUR_JWT_TOKEN>`

-   **Example Header:**
    `Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpYXQiOjE2...`

---

## Authentication & Account Lifecycle

### POST `/api/auth/register`
-   **Description:** Creates a new user account, including their address. Returns the new user's profile and authentication tokens.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "firstname": "John",            // Required, string, min 2 chars
      "name": "DOE",                  // Required, string, min 2 chars
      "lastname": "Smith",            // Optional, string, min 2 chars
      "email": "john.doe@example.com",// Required, string, valid email, unique
      "plainPassword": "password123", // Required, string, min 8 chars
      "birthday": "1990-01-15T00:00:00Z", // Required, string, ISO 8601 format, must be in the past
      "phoneNumber": "0812345678",    // Optional, string, unique
      "sexe": "Masculin",             // Required, string, must be 'Masculin' or 'Feminin'
      "address": {                    // Required, object
        "town": "/api/towns/123",     // Required, string, valid IRI for a Town resource
        "quarter": "Ma Campagne",     // Optional, string
        "avenue": "Des Aviateurs",    // Required, string
        "home": "42"                  // Optional, string
      }
    }
    ```
-   **Success Response (`201 Created`):** Returns the `User` object and `{ token, refreshToken }`.

### POST `/api/login`
-   **Description:** Authenticates a user with their identifier (email or phone number) and password.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "username": "user@example.com", // Required, string (can be email or phone number)
      "password": "your_password"     // Required, string
    }
    ```
-   **Success Response (`200 OK`):** Returns `{ token }` and a `user` object with public profile information.

### POST `/api/auth/logout`
-   **Description:** Logs out the current user. Since JWT is stateless, this is a semantic endpoint for client-side token removal.
-   **Permissions:** Authenticated User.
-   **Success Response (`200 OK`):** `{ "success": true }`

### POST `/api/users/{id}/change-password`
-   **Description:** Allows an authenticated user to change their own password.
-   **Permissions:** The authenticated user on their own resource.
-   **Request Body & Validation Rules:**
    ```json
    {
      "currentPassword": "my_old_password", // Required, string
      "newPassword": "my_new_secure_password" // Required, string, min 8 chars
    }
    ```
-   **Success Response (`200 OK`):** `{ "success": true }`
-   **Error Response (`400 Bad Request`):** If `currentPassword` is incorrect.

### POST `/api/auth/forgot-password`
-   **Description:** Initiates the password reset process by sending an email with a reset token.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "email": "user@example.com" // Required, string
    }
    ```
-   **Success Response (`200 OK`):** `{ "success": true }`. The response is always successful to prevent email enumeration attacks.

### POST `/api/auth/reset-password`
-   **Description:** Resets the user's password using the token received by email.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "token": "a1b2c3d4...",       // Required, string
      "newPassword": "my_new_password" // Required, string, min 8 chars
    }
    ```
-   **Success Response (`200 OK`):** `{ "success": true }`
-   **Error Response (`400 Bad Request`):** If the token is invalid or expired.

### POST `/api/auth/verify-email`
-   **Description:** Verifies a user's email address using a token.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "token": "e5f6g7h8..." // Required, string
    }
    ```
-   **Success Response (`200 OK`):** `{ "success": true }`

### POST `/api/auth/resend-verification`
-   **Description:** Resends the email verification link to a user.
-   **Permissions:** Public.
-   **Request Body & Validation Rules:**
    ```json
    {
      "email": "user@example.com" // Required, string
    }
    ```
-   **Success Response (`200 OK`):** `{ "success": true }`.

---

## User Profile Management

### GET `/api/users/profil`
-   **Description:** Retrieves the complete profile of the **currently authenticated user**.
-   **Permissions:** Authenticated User.
-   **Success Response (`200 OK`):** A detailed `User` object, including private information (`email`, `phoneNumber`, `address`, etc.).

### GET `/api/users/{id}`
-   **Description:** Retrieves the public profile of any user by their identifier (`slug`/`uuid`).
-   **Permissions:** Authenticated User.
-   **Success Response (`200 OK`):** A `User` object with public information only (`name`, `firstName`, etc.).

### PUT `/api/users/{id}`
-   **Description:** Updates a user's profile details.
-   **Permissions:** The user themselves or an administrator (`ROLE_ADMIN`).
-   **Request Body & Validation Rules:** A JSON object with the fields to update. The `town` field must be an IRI.
    ```json
    {
      "firstname": "Jane",            // String, min 2 chars
      "name": "DOE",                  // String, min 2 chars
      "phoneNumber": "0811111111",    // String, 9-13 digits
      "address": {                    // Object
        "town": "/api/towns/456",     // Required, valid IRI
        "avenue": "New Main Street"   // Required, string
      }
    }
    ```
-   **Success Response (`200 OK`):** The fully updated `User` object.

### GET `/api/users`
-   **Description:** Lists all users (paginated).
-   **Permissions:** Admin / Helpdesk roles only.
-   **Success Response (`200 OK`):** A Hydra collection of `User` objects with public information.

### DELETE `/api/users/{id}`
-   **Description:** Deletes a user account.
-   **Permissions:** Admin or the user themselves.
-   **Success Response (`204 No Content`):** No data is returned.

---

## Special Case: Mandatory Password Change

When a user logs in for the first time, or after an admin-initiated password reset, they **must** change their password before they can use the API.

Any attempt to call a protected API endpoint (except for logout and password change itself) will return a **`403 Forbidden`** error.

**Error Response (`403 Forbidden`):**
```json
{
  "code": 403,
  "message": "Password change required. Please use the password reset endpoint."
}
```

### Frontend Workflow
1.  After a successful login, make a call to a protected endpoint (e.g., `GET /api/users/profil`).
2.  If you receive a `403` response with the `Password change required` message, redirect the user to the "Change Password" screen in your application.
3.  On that screen, the user will use the `POST /api/users/{id}/change-password` endpoint to set their new password.
4.  Once the password has been changed successfully, all other API endpoints will become accessible.
5.  

Absolument. Voici la section `Full Route List Summary` complète, mise à jour pour inclure toutes les routes de votre entité `User` et des contrôleurs associés, le tout au format Markdown.

---

## Full Route List Summary

### Authentication & Account Management
| Method | Path                               | Description                               |
|--------|------------------------------------|-------------------------------------------|
| `POST` | `/api/login`                 | Log in a user (get JWT).                  |
| `POST` | `/api/auth/register`               | Register a new user account.              |
| `POST` | `/api/users/{id}/change-password`  | Change password while logged in.          |
| `POST` | `/api/auth/forgot-password`        | Request a password reset email.           |
| `POST` | `/api/auth/reset-password`         | Reset password using a token from email.  |
| `POST` | `/api/auth/verify-email`           | Verify user's email with a token.         |
| `POST` | `/api/auth/resend-verification`    | Resend the email verification link.       |
| `POST` | `/api/auth/logout`                 | Log out (client-side token removal).      |

### User & Profile
| Method | Path                               | Description                               |
|--------|------------------------------------|-------------------------------------------|
| `GET`  | `/api/users/profil`                | Get the current authenticated user's profile.|
| `GET`  | `/api/users`                       | List all users (Admin/Helpdesk).          |
| `GET`  | `/api/users/{id}`                  | Get a specific user's public profile.     |
| `PUT`  | `/api/users/{id}`                  | Update a user's profile.                  |
| `DELETE`| `/api/users/{id}`                 | Delete a user (Admin).                    |

### Patient-Specific Medical Data
| Method | Path                               | Description                               |
|--------|------------------------------------|-------------------------------------------|
| `GET`  | `/api/users/{id}/consultations`    | List all consultations for a patient.     |
| `GET`  | `/api/users/{id}/last_consultation`| Get the patient's last consultation.      |
| `GET`  | `/api/users/{id}/vital_signs_summary`| Get a summary of the patient's vital signs.|
| `POST` | `/api/users/{id}/pain_logs`        | Add a new pain log entry for the patient. |
| `POST` | `/api/users/{id}/mood_logs`        | Add a new mood log entry for the patient. |

### Appointments
| Method | Path                               | Description                               |
|--------|------------------------------------|-------------------------------------------|
| `GET`  | `/api/users/{id}/appointments`     | List all appointments for a patient.      |
| `POST` | `/api/users/{id}/request_appointment`| Request a new appointment for a patient.|

### Technical & Other
| Method | Path                               | Description                               |
|--------|------------------------------------|-------------------------------------------|
| `GET`  | `/api/users/wallet/{id}`           | Get a user's wallet (Developer).          |


---

### Address & Geolocation Endpoints

These endpoints allow you to retrieve geographical data in a hierarchical way (Country -> Province -> City -> Town). They are designed as **subresources**, meaning you first fetch a parent resource to get the URL for its children.

| Method | API Path                         | Description                                |
|--------|----------------------------------|--------------------------------------------|
| `GET`  | `/api/countries`                 | Lists all countries.                       |
| `GET`  | `/api/countries/{id}/provinces`  | Retrieves the provinces for a given country. |
| `GET`  | `/api/provinces/{id}/cities`     | Retrieves the cities for a given province.   |
| `GET`  | `/api/cities/{id}/towns`         | Retrieves the towns for a given city.        |

### Healthcare Zones Endpoints

These endpoints follow the same subresource pattern for healthcare-specific geographical areas.

| Method | API Path                         | Description                                  |
|--------|----------------------------------|----------------------------------------------|
| `GET`  | `/api/provinces/{id}/zones`      | Retrieves the health zones for a given province. |
| `GET`  | `/api/zones/{id}/areas`          | Retrieves the health areas for a given zone.   |

---
#### Detailed Examples

#### GET `/api/countries`
-   **Description:** Retrieves a list of all available countries. This is the starting point for the address cascade.
-   **Permissions:** Public or Authenticated User.
-   **Success Response (`200 OK`):** A Hydra collection of `Country` objects.
    ```json
    {
      "@context": "/api/contexts/Country",
      "@id": "/api/countries",
      "@type": "hydra:Collection",
      "hydra:member": [
        {
          "@id": "/api/countries/1",
          "@type": "Country",
          "id": 1,
          "name": "Democratic Republic of Congo"
        },
        {
          "@id": "/api/countries/2",
          "@type": "Country",
          "id": 2,
          "name": "France"
        }
      ],
      "hydra:totalItems": 2
    }
    ```

#### GET `/api/countries/{id}/provinces`
-   **Description:** Retrieves the collection of `Province` resources belonging to a specific `Country`.
-   **Permissions:** Public or Authenticated User.
-   **URL Parameters:**
    -   `{id}` (integer, **required**): The ID of the parent `Country`.
-   **Success Response (`200 OK`):** A Hydra collection of `Province` objects.
    ```json
    {
      "@context": "/api/contexts/Province",
      "@id": "/api/countries/1/provinces",
      "@type": "hydra:Collection",
      "hydra:member": [
        {
          "@id": "/api/provinces/12",
          "@type": "Province",
          "id": 12,
          "name": "Kinshasa"
        },
        {
          "@id": "/api/provinces/13",
          "@type": "Province",
          "id": 13,
          "name": "Kongo Central"
        }
      ],
      "hydra:totalItems": 2
    }
    ```
_Note: The other endpoints (`/provinces/{id}/cities`, `/cities/{id}/towns`, etc.) follow the same Hydra collection response format._
