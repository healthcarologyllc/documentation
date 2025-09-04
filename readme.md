### API Documentation - `User` & `Auth` Resources

This documentation outlines the available endpoints for managing users and authentication for the application.

**Base URL:** `/api`

## Table of Contents
- [Table of Contents](#table-of-contents)
- [General Notes](#general-notes)
- [https://sante.healthcarology.org](#httpssantehealthcarologyorg)
  - [Authentication](#authentication)
  - [Response Formats](#response-formats)
- [Authentication \& Account Lifecycle](#authentication--account-lifecycle)
  - [POST `/api/auth/register`](#post-apiauthregister)
  - [POST `/api/login`](#post-apilogin)
  - [POST `/api/auth/logout`](#post-apiauthlogout)
  - [POST `/api/auth/change-password`](#post-apiauthchange-password)
- [Password Reset Workflow (100% API)](#password-reset-workflow-100-api)
  - [Step 1: User requests a password reset](#step-1-user-requests-a-password-reset)
    - [POST `/api/auth/forgot-password`](#post-apiauthforgot-password)
  - [Step 2: User clicks the link and arrives on the Frontend](#step-2-user-clicks-the-link-and-arrives-on-the-frontend)
  - [Step 3: Frontend displays the form and sends the new password to the API](#step-3-frontend-displays-the-form-and-sends-the-new-password-to-the-api)
    - [POST `/api/auth/reset-password`](#post-apiauthreset-password)
  - [Step 4: Frontend displays the final result](#step-4-frontend-displays-the-final-result)
  - [POST `/api/auth/reset-password`](#post-apiauthreset-password-1)
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
- [User Health Data Endpoints](#user-health-data-endpoints)
  - [1. Get Last Consultation](#1-get-last-consultation)
    - [Authorization](#authorization)
    - [URL Parameters](#url-parameters)
    - [Success Response](#success-response)
    - [Error Responses](#error-responses)
  - [2. Get Vital Signs Summary](#2-get-vital-signs-summary)
    - [Authorization](#authorization-1)
    - [URL Parameters](#url-parameters-1)
    - [Success Response](#success-response-1)
    - [Error Responses](#error-responses-1)
  - [3. Get Vital Signs History](#3-get-vital-signs-history)
    - [Authorization](#authorization-2)
    - [URL Parameters](#url-parameters-2)
    - [Success Response](#success-response-2)
    - [Error Responses](#error-responses-2)
  - [3. Get All Consultations for a User](#3-get-all-consultations-for-a-user)
    - [Authorization](#authorization-3)
    - [URL Parameters](#url-parameters-3)
    - [Success Response](#success-response-3)
    - [Error Responses](#error-responses-3)
  - [4. Get Last Consultation for a User](#4-get-last-consultation-for-a-user)
    - [Authorization](#authorization-4)
    - [URL Parameters](#url-parameters-4)
    - [Success Response](#success-response-4)
    - [Error Responses](#error-responses-4)
  - [Patient Self-Reported Logs](#patient-self-reported-logs)
    - [A. Pain Logs](#a-pain-logs)
      - [1. List Pain Logs for a User](#1-list-pain-logs-for-a-user)
      - [2. Create a Pain Log for a User](#2-create-a-pain-log-for-a-user)
      - [3. Get a Specific Pain Log](#3-get-a-specific-pain-log)
      - [4. Update a Pain Log](#4-update-a-pain-log)
      - [5. Delete a Pain Log](#5-delete-a-pain-log)
    - [B. Mood Logs](#b-mood-logs)
- [Route Summary](#route-summary)
  - [User \& Profile](#user--profile)
  - [Patient-Specific Health Data](#patient-specific-health-data)
  - [Appointments](#appointments)
  - [Technical \& Other](#technical--other)
  - [Address \& Geolocation Endpoints](#address--geolocation-endpoints)
  - [Healthcare Zones Endpoints](#healthcare-zones-endpoints)
    - [Detailed Examples](#detailed-examples)
    - [GET `/api/countries`](#get-apicountries)
    - [GET `/api/countries/{id}/provinces`](#get-apicountriesidprovinces)

## General Notes
## https://sante.healthcarology.org
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

---

### POST `/api/auth/change-password`

-   **Description:** Allows a currently authenticated user to change their password.
-   **Permissions:** Authenticated User (`IS_AUTHENTICATED_FULLY`).
-   **Request Body:**
    ```json
    {
      "currentPassword": "my_current_secure_password",
      "newPassword": "my_new_even_more_secure_password"
    }
    ```
-   **Validation Rules:**
    -   `currentPassword` (string, **required**): Must match the user's current password.
    -   `newPassword` (string, **required**): Must be at least 8 characters long.
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true
    }
    ```
-   **Error Responses:**
    -   `400 Bad Request`: If `currentPassword` is incorrect or `newPassword` is too short.
    -   `401 Unauthorized`: If the user is not authenticated (missing or invalid JWT).

---

## Password Reset Workflow (100% API)

This workflow is fully decoupled. The user will only interact with your frontend application; the backend provides the necessary API endpoints.

### Step 1: User requests a password reset

The user enters their email in the frontend, which calls the API.

#### POST `/api/auth/forgot-password`
-   **Description:** Initiates the password reset process by sending an email.
-   **Permissions:** Public.
-   **Request Body:**
    ```json
    {
      "email": "user.who.forgot@example.com"
    }
    ```
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true
    }
    ```
    *The API sends an email containing a link like `https://your-frontend-app.com/reset-password?token=a1b2c3d4...`*

### Step 2: User clicks the link and arrives on the Frontend

The user opens their email and clicks the link. Their browser opens your frontend application at the specified URL (e.g., `/reset-password`).

### Step 3: Frontend displays the form and sends the new password to the API

Your frontend page at `/reset-password` must:
1.  Extract the `token` from the URL's query parameters.
2.  Display a form with "New Password" and "Confirm Password" fields.
3.  When the user submits this form, send the `token` and the `newPassword` to the backend API.

#### POST `/api/auth/reset-password`
-   **Description:** Sets a new password for a user using the token.
-   **Permissions:** Public.
-   **Request Body:**
    ```json
    {
      "token": "a1b2c3d4-e5f6-4a1b-9c8d-ef0a1b2c3d4e",
      "newPassword": "my_brand_new_password"
    }
    ```
-   **Validation Rules:** `newPassword` must be at least 8 characters long.
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true,
      "message": "Password has been reset successfully."
    }
    ```
-   **Error Response (`400 Bad Request`):**
    ```json
    {
      "success": false,
      "message": "Invalid or expired token."
    }
    ```

### Step 4: Frontend displays the final result

Based on the API's JSON response (`success: true` or `success: false`), your frontend application displays the appropriate success or error message to the user and provides a link to the login page.

### POST `/api/auth/reset-password`

-   **Description:** Sets a new password for a user using the token they received via the "forgot password" email.
-   **Permissions:** Public.
-   **Request Body:**
    ```json
    {
      "token": "a1b2c3d4-e5f6-4a1b-9c8d-ef0a1b2c3d4e",
      "newPassword": "my_brand_new_password"
    }
    ```
-   **Validation Rules:**
    -   `token` (string, **required**): The reset token from the email.
    -   `newPassword` (string, **required**): The user's new password (min. 8 characters).
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true
    }
    ```
-   **Error Responses:**
    -   `400 Bad Request`: If the token is invalid, expired, or not found.

---

### POST `/api/auth/verify-email`

-   **Description:** Verifies a user's email address using the token sent to them upon registration.
-   **Permissions:** Public.
-   **Request Body:**
    ```json
    {
      "token": "f1g2h3j4-k5l6-4m1n-9o8p-qrstuvwxyza"
    }
    ```
-   **Validation Rules:**
    -   `token` (string, **required**): The verification token from the email.
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true
    }
    ```
-   **Error Responses:**
    -   `400 Bad Request`: If the token is invalid or not found.

---

### POST `/api/auth/resend-verification`

-   **Description:** Resends the email verification link to a user who has not yet verified their email address.
-   **Permissions:** Public.
-   **Request Body:**
    ```json
    {
      "email": "unverified.user@example.com"
    }
    ```
-   **Validation Rules:**
    -   `email` (string, **required**): The email address to which the verification link should be resent.
-   **Success Response (`200 OK`):**
    ```json
    {
      "success": true
    }
    ```
    *Note: The response is **always** successful for security reasons, regardless of whether the email exists or is already verified.*

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

Of course. Here is professional, well-structured API documentation in Markdown format (and in English) for the new endpoints you've created.

You can copy and paste this directly into your `README.md` or a dedicated `API_DOCUMENTATION.md` file.

---

## User Health Data Endpoints
### 1. Get Last Consultation

Retrieves the full details of the most recent consultation for a specific user.

`GET /api/users/{id}/last_consultation`

#### Authorization

*   Requires authentication.
*   A standard user can only access their own consultation data (`object == user`).

#### URL Parameters

| Parameter | Type    | Description              |
| :-------- | :------ | :----------------------- |
| `id`      | Integer | **Required.** The ID of the user. |

#### Success Response

*   **Code:** `200 OK`
*   **Content:** A single `Consultation` object containing details of the last visit.

**Example Response:**
```json
{
    "@context": "/api/contexts/Consultation",
    "@id": "/api/consultations/152",
    "@type": "Consultation",
    "id": 152,
    "createdAt": "2024-05-21T14:30:00+00:00",
    "reason": "Follow-up checkup",
    "weight": "75.5",
    "temperature": "37.1",
    "consultBy": {
        "@id": "/api/users/5",
        "@type": "User",
        "fullName": "Dr. Alice Martin"
    },
    "monitoringVitalSigns": [
        {
            "@id": "/api/vital_signs/310",
            "@type": "VitalSign",
            "weight": "75.6"
        }
    ]
}
```

#### Error Responses

*   **Code:** `401 Unauthorized`
    *   **Reason:** Missing or invalid authentication token.
*   **Code:** `403 Forbidden`
    *   **Reason:** The authenticated user is trying to access another user's data without administrative privileges.
*   **Code:** `404 Not Found`
    *   **Reason:** The user with the specified `id` does not exist, or they have no consultations.

---

### 2. Get Vital Signs Summary

Retrieves a summary of the vital signs from the user's most recent consultation. This is a composite object that includes both the primary vital signs and any additional monitoring signs recorded.

`GET /api/users/{id}/vital_signs_summary`

#### Authorization

*   Requires authentication.
*   A standard user can only access their own data.
*   An administrator (`ROLE_ADMIN`) can access any user's data.

#### URL Parameters

| Parameter | Type    | Description              |
| :-------- | :------ | :----------------------- |
| `id`      | Integer | **Required.** The ID of the user. |

#### Success Response

*   **Code:** `200 OK`
*   **Content:** A single `VitalSignsSummaryDto` object.

**Example Response:**
```json
{
    "@context": "/api/contexts/VitalSignsSummaryDto",
    "@id": "/api/users/42/vital_signs_summary",
    "@type": "VitalSignsSummaryDto",
    "weight": "75.5",
    "temperature": "37.1",
    "cardiacFrequency": "80",
    "respiratoryRate": "16",
    "oxygenSaturation": "98",
    "bloodPressureUp": "120",
    "bloodPressureDown": "80",
    "pulsation": "80",
    "size": 180,
    "bloodSugar": "95",
    "headCircumference": null,
    "armCircumference": 32,
    "comment": "Patient feels well.",
    "monitoringVitalSigns": []
}
```

#### Error Responses

*   **Code:** `401 Unauthorized`
*   **Code:** `403 Forbidden`
*   **Code:** `404 Not Found`

---

### 3. Get Vital Signs History

Retrieves a chronological history of a user's vital signs, collected from all their past consultations. This endpoint is optimized for building charts and graphs.

`GET /api/users/{id}/vital_signs_history`

#### Authorization

*   Requires authentication.
*   A standard user can only access their own data.
*   An administrator (`ROLE_ADMIN`) can access any user's data.

#### URL Parameters

| Parameter | Type    | Description              |
| :-------- | :------ | :----------------------- |
| `id`      | Integer | **Required.** The ID of the user. |

#### Success Response

*   **Code:** `200 OK`
*   **Content:** An array of `VitalSignDataPointDto` objects, sorted by date in ascending order. If the user has no consultations, an empty array `[]` is returned.

**Example Response:**
```json
[
    {
        "date": "2024-01-15T10:00:00+00:00",
        "weight": "76.0",
        "temperature": "37.0",
        "cardiacFrequency": "82",
        "respiratoryRate": "16",
        "oxygenSaturation": "97",
        "bloodPressureUp": "122",
        "bloodPressureDown": "81",
        "pulsation": "82",
        "size": 180,
        "bloodSugar": null,
        "headCircumference": null,
        "armCircumference": 32
    },
    {
        "date": "2024-05-21T14:30:00+00:00",
        "weight": "75.5",
        "temperature": "37.1",
        "cardiacFrequency": "80",
        "respiratoryRate": "16",
        "oxygenSaturation": "98",
        "bloodPressureUp": "120",
        "bloodPressureDown": "80",
        "pulsation": "80",
        "size": 180,
        "bloodSugar": "95",
        "headCircumference": null,
        "armCircumference": 32
    }
]
```

#### Error Responses

*   **Code:** `401 Unauthorized`
*   **Code:** `403 Forbidden`
*   **Note:** This endpoint does not return `404 Not Found`. If the user exists but has no consultations, it will return `200 OK` with an empty array.
  

  Of course. Here is the Markdown documentation in English for those two specific routes. It's designed to be clear, concise, and easy for any developer on your team to understand.

This documentation accurately reflects the security rules and expected output for each endpoint based on the code you provided.

---

### 3. Get All Consultations for a User

Retrieves a list of all past consultations for a specific user. The consultations are returned as a collection.

`GET /api/users/{id}/consultations`

#### Authorization

*   Requires authentication.
*   A user can **only** access their own list of consultations. Attempting to access another user's consultations will result in a `403 Forbidden` error.
*   **Note:** Unlike other endpoints, administrators do not have special access to this route and are also bound by this rule.

#### URL Parameters

| Parameter | Type    | Description              |
| :-------- | :------ | :----------------------- |
| `id`      | Integer | **Required.** The ID of the user. |

#### Success Response

*   **Code:** `200 OK`
*   **Content:** An array of `Consultation` objects. If the user has no consultations, an empty array `[]` is returned.

**Example Response:**
```json
[
    {
        "@id": "/api/consultations/152",
        "@type": "Consultation",
        "id": 152,
        "createdAt": "2024-05-21T14:30:00+00:00",
        "reason": "Follow-up checkup"
    },
    {
        "@id": "/api/consultations/119",
        "@type": "Consultation",
        "id": 119,
        "createdAt": "2024-01-15T10:00:00+00:00",
        "reason": "Initial consultation"
    }
]
```

#### Error Responses

*   **Code:** `401 Unauthorized`
    *   **Reason:** Missing or invalid authentication token.
*   **Code:** `403 Forbidden`
    *   **Reason:** The authenticated user's ID does not match the `{id}` in the URL.
*   **Code:** `404 Not Found`
    *   **Reason:** The user with the specified `id` does not exist.

---

### 4. Get Last Consultation for a User

Retrieves the full details of the **most recent** consultation for a specific user. This endpoint uses a custom provider to find the latest record.

`GET /api/users/{id}/last_consultation`

#### Authorization

*   Requires authentication.
*   A standard user can only access their own consultation data.
*   An administrator (`ROLE_ADMIN`) can access any user's consultation data.

#### URL Parameters

| Parameter | Type    | Description              |
| :-------- | :------ | :----------------------- |
| `id`      | Integer | **Required.** The ID of the user. |

#### Success Response

*   **Code:** `200 OK`
*   **Content:** A single `Consultation` object containing the details of the last visit.

**Example Response:**
```json
{
    "@context": "/api/contexts/Consultation",
    "@id": "/api/consultations/152",
    "@type": "Consultation",
    "id": 152,
    "createdAt": "2024-05-21T14:30:00+00:00",
    "reason": "Follow-up checkup",
    "weight": "75.5",
    "temperature": "37.1",
    "consultBy": {
        "@id": "/api/users/5",
        "@type": "User",
        "fullName": "Dr. Alice Martin"
    }
}
```

#### Error Responses

*   **Code:** `401 Unauthorized`
    *   **Reason:** Missing or invalid authentication token.
*   **Code:** `403 Forbidden`
    *   **Reason:** The authenticated user is trying to access another user's data without administrative privileges.
*   **Code:** `404 Not Found`
    *   **Reason:** The user with the specified `id` does not exist, or they have no recorded consultations.
  
Of course. Here is the complete, merged API documentation in a single, well-organized summary.

I have integrated all the previous tables and added the new routes we created (`vital_signs_summary` and `vital_signs_history`), refining the descriptions for clarity.

---

Of course. Here is the documentation section for the new CRUD routes, written in English and formatted in Markdown.

This section is designed to be added to your existing API documentation. It covers all the operations for both `PainLog` and `MoodLog`, explaining how the subresources and direct resource routes work together.

---

### Patient Self-Reported Logs
#### A. Pain Logs

Full CRUD operations for managing a patient's pain log entries.

##### 1. List Pain Logs for a User

Retrieves a collection of all pain logs for a specific user.

`GET /api/users/{id}/pain_logs`

*   **Authorization:** A user can view their own logs. An administrator can view any user's logs.
*   **URL Parameters:**
    *   `id` (string, UUID): **Required.** The ID of the user.
*   **Success Response (`200 OK`):** An array of `PainLog` objects. Returns an empty array `[]` if the user has no logs.

##### 2. Create a Pain Log for a User

Creates a new pain log entry for a specific user. The `patient` is automatically assigned based on the URL.

`POST /api/users/{id}/pain_logs`

*   **Authorization:** A user can create a log for themselves. An administrator can create a log for any user.
*   **URL Parameters:**
    *   `id` (string, UUID): **Required.** The ID of the user for whom the log is being created.
*   **Request Body:** A JSON object representing the `PainLog`.

| Field             | Type      | Required | Description                                                |
| :---------------- | :-------- | :------- | :--------------------------------------------------------- |
| `level`           | Integer   | Yes      | Pain intensity, from 0 to 10.                              |
| `description`     | String    | No       | Location, type, or other details about the pain.           |
| `medicationTaken` | Boolean   | No       | `true` if medication was taken for this pain. Defaults to `false`. |
| `medicationName`  | String    | No       | The name of the medication, if `medicationTaken` is `true`.  |
| `loggedAt`        | String    | Yes      | The date and time the pain was felt. (ISO 8601 format, e.g., `2024-05-22T08:00:00Z`) |

**Example Body:**
```json
{
  "level": 7,
  "description": "Sharp pain in lower back after lifting.",
  "medicationTaken": true,
  "medicationName": "Ibuprofen 400mg",
  "loggedAt": "2024-05-22T08:00:00Z"
}
```
*   **Success Response (`201 Created`):** The newly created `PainLog` object.
*   **Error Response (`422 Unprocessable Entity`):** If validation fails (e.g., `level` is out of range).

##### 3. Get a Specific Pain Log

Retrieves a single pain log entry by its own unique ID.

`GET /api/pain_logs/{log_id}`

*   **Authorization:** The user must be the owner of the log or an administrator.
*   **URL Parameters:**
    *   `log_id` (string, UUID): **Required.** The ID of the `PainLog` itself.

##### 4. Update a Pain Log

Updates an existing pain log entry.

`PUT /api/pain_logs/{log_id}`

*   **Authorization:** The user must be the owner of the log or an administrator.
*   **URL Parameters:**
    *   `log_id` (string, UUID): **Required.** The ID of the `PainLog` to update.
*   **Request Body:** A JSON object with the fields to be updated.

##### 5. Delete a Pain Log

Deletes a pain log entry.

`DELETE /api/pain_logs/{log_id}`

*   **Authorization:** The user must be the owner of the log or an administrator.
*   **URL Parameters:**
    *   `log_id` (string, UUID): **Required.** The ID of the `PainLog` to delete.
*   **Success Response (`204 No Content`):** The log was successfully deleted.

---

#### B. Mood Logs

The API for Mood Logs follows the **exact same structure and logic** as Pain Logs.

*   Simply replace `/pain_logs` with `/mood_logs` in the endpoints.
*   The request and response bodies will use the fields defined in the `MoodLog` entity (e.g., `mood`, `triggers`, `notes`).

**Example Routes:**
*   `GET /api/users/{id}/mood_logs`
*   `POST /api/users/{id}/mood_logs`
*   `GET /api/mood_logs/{log_id}`
*   `PUT /api/mood_logs/{log_id}`
*   `DELETE /api/mood_logs/{log_id}`

## Route Summary

Routes for user registration, login, and password management.

| Method | Path                               | Description                               |
| :---   | :--------------------------------- | :---------------------------------------- |
| `POST` | `/api/login`                       | Log in a user to obtain a JWT.            |
| `POST` | `/api/auth/register`               | Register a new user account.              |
| `POST` | `/api/users/{id}/change-password`  | Change password while logged in.          |
| `POST` | `/api/auth/forgot-password`        | Request a password reset email.           |
| `POST` | `/api/auth/reset-password`         | Reset password using a token from email.  |
| `POST` | `/api/auth/verify-email`           | Verify user's email with a token.         |
| `POST` | `/api/auth/resend-verification`    | Resend the email verification link.       |
| `POST` | `/api/auth/logout`                 | Log out (client-side token removal).      |

### User & Profile

Routes for managing user profiles.

| Method   | Path                  | Description                                |
| :---     | :-------------------- | :----------------------------------------- |
| `GET`    | `/api/users/profil`   | Get the current authenticated user's profile.|
| `GET`    | `/api/users`          | List all users (Admin/Helpdesk).           |
| `GET`    | `/api/users/{id}`     | Get a specific user's public profile.      |
| `PUT`    | `/api/users/{id}`     | Update a user's profile.                   |
| `DELETE` | `/api/users/{id}`     | Delete a user.                     |

### Patient-Specific Health Data

Routes for accessing a user's personal medical records. The `{id}` always refers to the patient's ID.

| Method | Path                                  | Description                                           |
| :---   | :------------------------------------ | :---------------------------------------------------- |
| `GET`  | `/api/users/{id}/consultations`       | List all consultations for a patient.                 |
| `GET`  | `/api/users/{id}/last_consultation`   | Get the patient's most recent consultation.           |
| `GET`  | `/api/users/{id}/vital_signs_summary` | Get vital signs summary from the last consultation.   |
| `GET`  | `/api/users/{id}/vital_signs_history` | Retrieve historical vital signs data for charting.    |
| `GET` | `/api/users/{id}/pain_logs`            | show pain logs for the patient.                       |
| `POST` | `/api/users/{id}/pain_logs`           | Add a new pain log entry for the patient.             |

### Appointments

Routes for managing patient appointments.

| Method | Path                                  | Description                               |
| :---   | :------------------------------------ | :---------------------------------------- |
| `GET`  | `/api/users/{id}/appointments`        | List all appointments for a patient.      |
| `POST` | `/api/users/{id}/request_appointment` | Request a new appointment for a patient.  |

### Technical & Other

Internal or developer-specific routes.

| Method | Path                     | Description                      |
| :---   | :----------------------- | :------------------------------- |
| `GET`  | `/api/users/wallet/{id}` | Get a user's wallet (Developer). |

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
