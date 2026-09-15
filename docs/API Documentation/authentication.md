# Authentication & Security

The application uses Auth0 (Google) for all authentication. No custom auth logic is written — Auth0 handles sign-up, sign-in, password reset, account deletion, and social login via its hosted Universal Login page.

Authentication is fully wired: the frontend sign-in/sign-out flows and session persistence are live, and every machine and share endpoint on the backend requires a valid JWT.

---

## Packages

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

## Claims and User Records

Auth0 access tokens carry only `sub` by default. The tenant's **post-login Action** adds the user's email and name under a namespaced claim prefix so they do not collide with standard OIDC claim names:

- Claim namespace: `https://automata-ev/` — i.e. `https://automata-ev/email` and `https://automata-ev/name`
- The backend reads the namespaced claims first and falls back to plain `email`/`name` if present (`src/config/claims.ts`)
- `GET /api/me` reflects the resolved `sub`, `email`, and `name` back to the frontend

**User records** (sprint 2, M1/F1): a middleware running after JWT validation on every protected route upserts a `users` collection document keyed on the unique `sub`, refreshing the email and name from the token. This directory is what lets an owner share a machine **by email**: the API resolves the typed email to the recipient's `sub` and stores that in the machine's `sharedWith` list.

- The collection holds **no credentials** — Auth0 owns identity
- A recipient must have used the app at least once before a machine can be shared with them
- Recording skips (and never fails a request) when MongoDB is not connected

See [Sharing](../Features/sharing.md) for the user-facing flow and [REST Endpoints](rest-endpoints.md) for the share endpoints.

---

## CORS Configuration

The backend uses the `cors` middleware with origin restricted to `CORS_ORIGIN` (defaults to `*`). In production this is set to the frontend's Azure URL.

---

## Development Without Auth0 Configured

If `AUTH0_ISSUER_BASE_URL` or `AUTH0_AUDIENCE` is unset (common before the tenant is set up), the auth middleware responds **503 Service Unavailable** with a clear message on protected routes instead of failing confusingly — the API still boots and serves `/health`.

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Data Models](data-models.md) | [Backend Architecture](../Technical%20Architecture/backend-architecture.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
