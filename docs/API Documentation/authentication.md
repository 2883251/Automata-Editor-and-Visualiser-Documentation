# Authentication & Security

The application uses Auth0 (Google) for all authentication. No custom auth logic is written — Auth0 handles sign-up, sign-in, password reset, account deletion, and social login via its hosted Universal Login page.

---

## Packages Installed

| Repo | Package | Version | Role |
|---|---|---|---|
| Frontend | `@auth0/auth0-react` | ^2.24.1 | React SDK — login/logout, token management, user profile |
| Backend | `express-oauth2-jwt-bearer` | ^1.10.0 | Express middleware — validates Auth0-issued JWTs |

---

## Authentication Flow

1. User clicks "Sign In" in the frontend
2. Auth0 Universal Login page handles credentials (Google, GitHub, email)
3. Frontend receives a JWT access token via `@auth0/auth0-react`
4. Token sent as `Authorization: Bearer <token>` header on API requests
5. Backend validates token signature, issuer, and audience via `express-oauth2-jwt-bearer`

---

## Configuration

### Frontend Environment Variables

| Variable | Purpose |
|---|---|
| `VITE_AUTH0_DOMAIN` | Auth0 tenant domain |
| `VITE_AUTH0_CLIENT_ID` | Auth0 application client ID |
| `VITE_AUTH0_AUDIENCE` | Auth0 API identifier — used to request an access token the backend will accept |

### Backend Environment Variables

| Variable | Purpose |
|---|---|
| `AUTH0_ISSUER_BASE_URL` | Auth0 tenant URL, used to validate incoming JWTs |
| `AUTH0_AUDIENCE` | Auth0 API identifier this server accepts tokens for |

---

## CORS Configuration

The backend uses the `cors` middleware with origin restricted to `CORS_ORIGIN` (defaults to `*`). In production this is set to the frontend's Azure URL.

---

!!! info "Status: Planned"
    Both packages are installed but route protection is not yet implemented:

    - The frontend sign-in route (`/sign-in`) renders a placeholder
    - The backend has no JWT middleware registered in `createApp()`
    - No routes currently require authentication

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Data Models](data-models.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
