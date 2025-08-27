### API Documentation - `User` Resource

This documentation outlines the available endpoints for managing users (patients and staff) within the application.

**Base URL:** `/sante.healthcarology.org/api`

## General Notes

### Authentication

Most endpoints require authentication. To authenticate, you must first obtain a JWT token by sending a `POST` request to the `/api/login` endpoint with the user's credentials.

**Example Login Request:**
```json
{
  "username": "email@example.com",
  "password": "your_password"
}
```

Once you have the token, you must include it in the header of all subsequent requests:
`Authorization: Bearer YOUR_JWT_TOKEN`

### Response Formats

-   **Success:** Successful responses are typically in `JSON` format with a status code of `200 OK` (for GET), `201 Created` (for POST), `200 OK` (for PUT), or `204 No Content` (for DELETE).
-   **Error:** Validation errors return a `400 Bad Request` or `422 Unprocessable Entity` status code with details about the errors. Authentication or permission errors return `401 Unauthorized` or `403 Forbidden` codes.

---

## 1. Account Management & Authentication

### 1.1 Create a User Account
Creates a new patient record or user account.

-   **Endpoint:** `POST /api/users`
-   **Permissions:** Open to the public.
-   **Request Body:**
    ```json
    {
      "email": "new.patient@email.com",
      "plainPassword": "password123",
      "name": "LASTNAME",
      "lastname": "Midname",
      "firstname": "Firstname",
      "sexe": "Male",
      "phoneNumber": "0812345678",
      "birthday": "1990-01-15T00:00:00+00:00"
    }
    ```
-   **Success Response (`201 Created`):** A JSON object representing the newly created user.

### 1.2 Change Password
Allows an authenticated user to change their own password.

-   **Endpoint:** `POST /api/users/{id}/reset-password`
-   **Permissions:** The user must be authenticated and can only modify their own password (`{id}` must be their own ID).
-   **Request Body:**
    ```json
    {
      "oldPassword": "current_password",
      "newPassword": "new_secure_password"
    }
    ```
-   **Success Response (`200 OK`):**
    ```json
    {
      "message": "Password reset successfully."
    }
    ```

---

## 2. User Management (CRUD)

### 2.1 Get User Information (Profile)
Retrieves the complete details of a specific user.

-   **Endpoint:** `GET /api/users/{id}`
-   **Permissions:** Administrators (`ROLE_ADMIN`) or the user themselves.
-   **Success Response (`200 OK`):** A JSON object containing the user's detailed information, including their address, consultations, etc. (as defined by serialization groups).

### 2.2 Update a User
Updates a user's profile information. This uses a custom controller for advanced logic (e.g., handling profile picture uploads).

-   **Endpoint:** `PUT /api/users/{id}`
-   **Permissions:** Administrators (`ROLE_ADMIN`) or the user themselves.
-   **Request Body:** A JSON object containing the fields to be updated (e.g., `name`, `phoneNumber`).
-   **Success Response (`200 OK`):** The updated user JSON object.

### 2.3 List Users
Retrieves a paginated list of all users.

-   **Endpoint:** `GET /api/users`
-   **Permissions:** Administrators (`ROLE_ADMIN`) or Helpdesk staff (`ROLE_HELPDESK`).
-   **Success Response (`200 OK`):** A collection of user objects.

### 2.4 Delete a User
Deletes a user account.

-   **Endpoint:** `DELETE /api/users/{id}`
-   **Permissions:** Administrators (`ROLE_ADMIN`) or the user themselves.
-   **Success Response (`204 No Content`):** No data is returned.

---

## 3. Patient-Specific Operations

These endpoints provide access to specific medical data related to a patient.

### 3.1 Vital Signs Summary
Retrieves a summary of the latest vital signs recorded for a patient.

-   **Endpoint:** `GET /api/users/{id}/vital_signs_summary`
-   **Permissions:** Nursing staff (`ROLE_NURSE`), the patient themselves, or any healthcare professional.
-   **Success Response (`200 OK`):** A JSON object containing aggregated data (format defined by the `user:vitals:summary` group).

### 3.2 Last Consultation
Retrieves information about the patient's most recent consultation.

-   **Endpoint:** `GET /api/users/{id}/last_consultation`
-   **Permissions:** Any healthcare professional or the patient themselves.
-   **Success Response (`200 OK`):** A JSON object representing the `Consultation` entity.

### 3.3 Add a Pain Log
Records a new pain level entry for the patient.

-   **Endpoint:** `POST /api/users/{id}/pain_logs`
-   **Permissions:** The patient themselves.
-   **Request Body:**
    ```json
    {
      "level": 5,
      "description": "Acute pain in the lower back."
    }
    ```
-   **Success Response (`201 Created`):** The newly created `PainLog` object.

### 3.4 Request an Appointment
Allows a user to request an appointment.

-   **Endpoint:** `POST /api/users/{id}/request_appointment`
-   **Permissions:** The patient themselves or a staff member (`ROLE_STAFF`).
-   **Request Body:** An `AppointmentRequestDto` (the exact fields need to be specified).
-   **Success Response (`201 Created`):** The newly created `Appointment` object.

---

## 4. Technical Operations

### 4.1 Check Wallet
An endpoint reserved for developers to check a user's wallet.

-   **Endpoint:** `GET /api/users/wallet/{id}`
-   **Permissions:** Developers (`ROLE_DEVELOPER`) or the user themselves.
-   **Success Response (`200 OK`):** A JSON object representing the user's `Wallet`.
