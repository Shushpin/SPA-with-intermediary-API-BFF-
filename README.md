# Keycloak Security Patterns: Backend-for-Frontend (BFF) pattern for SPA applications

This repository demonstrates security architecture patterns for web applications using Keycloak.  
It focuses on implementing the **Backend-for-Frontend (BFF)** pattern with the **Authorization Code Flow**.

---

## Overview

The **BFF (Backend-for-Frontend)** acts as an intermediary between the browser (SPA) and the authorization server (Keycloak).

Instead of exposing access and refresh tokens to the client, the Spring Boot backend:
- handles the OAuth2 flow,
- securely stores tokens on the server side (session),
- and returns only a secure `HttpOnly` session cookie (`JSESSIONID`) to the browser.

This significantly reduces the attack surface on the frontend.

---

## Architecture Flow

1. User accesses the frontend
2. BFF redirects the user to Keycloak for authentication
3. Keycloak returns an authorization code
4. BFF exchanges the code for tokens
5. Tokens are stored server-side (session)
6. Browser receives only a secure session cookie
7. All subsequent API calls go through the BFF

---

## Advantages & Trade-offs

### ✅ Advantages

- **Improved Security**  
  Tokens never reach the browser, making them inaccessible to XSS attacks.

- **Simplified Frontend**  
  No need to manage token storage, expiration, or refresh logic.

- **No CORS Issues**  
  All external API communication is handled by the backend.

---

### ⚠️ Trade-offs

- **Stateful Architecture**  
  Requires session storage (in-memory or external, e.g., Redis), which adds complexity to scaling.

- **Additional Backend Layer**  
  Introduces extra development and maintenance overhead.

---

## Keycloak Configuration

To run this project, configure Keycloak as follows:

I use Docker so write `docker run -p 8081:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:latest start-dev`in terminal

**Valid Redirect URI:**
  `http://localhost:8081`
User: admin,
Pass: admin
  
- **Realm:** `bff-realm`
- **Client ID:** `bff-client`
- **Client Type:** Confidential
- **Client Authentication:** Enabled
- **Authorization Flow:** Standard Flow (Authorization Code)

- **Valid Redirect URI:**
http://localhost:8080/login/oauth2/code/keycloak
## Creating a Test User

To successfully log in after starting the application, you need to create a user in Keycloak:

1. Open the Keycloak Admin Console and make sure the correct realm (`bff-realm`) is selected.
2. Navigate to **Users** and click **Add user**.
3. Enter a username (e.g., `testuser`) and save.
4. Open the **Credentials** tab for the newly created user.
5. Click **Set password**, enter a password, and **disable the "Temporary" option** (so that Keycloak does not require a password change on first login).
6. Save the changes.

---

## Getting Started

1. Start Keycloak (e.g., via Docker on port `8081`)
2. Set your `client-secret` in `application.yaml`
3. Run the Spring Boot application
4. Open in browser (preferably Incognito mode):http://localhost:8080/api/user

---

## Notes

- Ensure cookies are enabled in your browser
- For production, use HTTPS to secure cookies
- Consider external session storage (e.g., Redis) for scalability  