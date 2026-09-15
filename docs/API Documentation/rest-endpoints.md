# REST Endpoints

Reference for all HTTP endpoints exposed by the backend API (Express 5, `Automata-Editor-and-Visualiser-Backend`). Machine persistence and sharing endpoints were added in sprint 2 (issue #6, M1/F1). All examples assume the API at `http://localhost:4000`.

---

## Public Endpoints

### `GET /health`

Liveness check used by deployment platforms and monitoring.

- **Response**: `200 OK` — `{"status":"ok"}`
- **Authentication**: Not required

```bash
curl http://localhost:4000/health
# {"status":"ok"}
```

---

## Authenticated Endpoints

All endpoints below require a valid Auth0 JWT sent as `Authorization: Bearer <token>`. The token's `sub` claim scopes every query; a machine **shared with** the caller can be read, but only its owner may modify or delete it, or manage its shares.

### `GET /api/me`

Returns the authenticated user's identity claims from the token.

```json
{
  "sub": "auth0|1234567890",
  "email": "student@example.com",
  "name": "Student Name"
}
```

`email` and `name` come from namespaced claims added by the tenant's Auth0 post-login Action (see [Authentication & Security](authentication.md)); they are `null` when absent.

### Machine Management (`/api/machines`)

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/machines` | List the caller's machines |
| `POST` | `/api/machines` | Create a machine |
| `GET` | `/api/machines/:id` | Get one machine |
| `PUT` | `/api/machines/:id` | Full-replacement update |
| `PATCH` | `/api/machines/:id` | Rename |
| `DELETE` | `/api/machines/:id` | Delete |
| `GET` | `/api/machines/shared-with-me` | Machines shared with the caller |

#### `GET /api/machines`

List items omit the heavy fields (`source`, `positions`) — fetch one machine by id for full content. Sorted by `updatedAt` descending.

```json
[
  {
    "id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "name": "Binary counter",
    "isOwner": true,
    "createdAt": "2026-09-13T10:00:00.000Z",
    "updatedAt": "2026-09-13T12:30:00.000Z"
  }
]
```

#### `POST /api/machines`

Creates a machine owned by the caller. Returns **201** with the full machine response.

```json
{
  "name": "Binary counter",
  "source": "states: ...\nstart: q0\n...",
  "positions": { "q0": { "x": 100, "y": 200 } }
}
```

- `name` is optional (defaults to `Untitled machine`, max 200 chars after trim); `source` and `positions` are required.

#### `GET /api/machines/:id`

Full machine response. `sharedWith` is only included when the caller is the owner — a recipient has no business knowing who else the machine was shared with.

```json
{
  "id": "64f1a2b3c4d5e6f7a8b9c0d1",
  "name": "Binary counter",
  "source": "states: ...\nstart: q0\n...",
  "positions": { "q0": { "x": 100, "y": 200 } },
  "owner": "auth0|1234567890",
  "isOwner": true,
  "sharedWith": ["auth0|9876543210"],
  "createdAt": "2026-09-13T10:00:00.000Z",
  "updatedAt": "2026-09-13T12:30:00.000Z"
}
```

#### `PUT /api/machines/:id`

Full-replacement update with the same body as create — matching the frontend's save behaviour. Returns the updated machine response.

#### `PATCH /api/machines/:id`

Rename only. Body: `{ "name": "New name" }` (1–200 chars after trimming, so a machine cannot be renamed to a blank string).

#### `DELETE /api/machines/:id`

Deletes the machine. Returns **204 No Content**.

---

## Sharing (`/api/machines/:id/shares`)

Only a machine's **owner** may view, add, or revoke its shares. Ownership is checked before any email lookup, so a caller cannot use someone else's machine id to probe which emails have accounts.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/machines/:id/shares` | List who the machine is shared with |
| `POST` | `/api/machines/:id/shares` | Share with a user by email |
| `DELETE` | `/api/machines/:id/shares/:sub` | Revoke one user's access |

#### `POST /api/machines/:id/shares`

```json
{ "email": "classmate@example.com" }
```

- The email is trimmed and lowercased, then resolved against the **users collection** — a recipient must have signed in to the app at least once. When two Auth0 identities share an email, the most recently active one wins.
- Storing the share is a set-union on the machine's `sharedWith` list: sharing with someone who already has access is a no-op. Sharing never changes the machine's `updatedAt` (it changes who can see the machine, not the machine).
- Returns the updated share list as user summaries:

```json
[
  { "sub": "auth0|9876543210", "email": "classmate@example.com", "name": "Classmate" }
]
```

#### `GET /api/machines/:id/shares`

Same response shape as above. A `sub` with no user record still appears, with `null` email and name.

#### `DELETE /api/machines/:id/shares/:sub`

Revokes that user's access. Returns **204** whether or not the user had access; **404** if the machine does not exist or the caller does not own it.

#### `GET /api/machines/shared-with-me`

Lists machines other users have shared with the caller — summaries (no `source` or `positions`) with the owner identified, sorted by `updatedAt` descending.

```json
[
  {
    "id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "name": "Binary counter",
    "owner": { "sub": "auth0|1234567890", "email": "owner@example.com", "name": "Owner" },
    "isOwner": false,
    "createdAt": "2026-09-13T10:00:00.000Z",
    "updatedAt": "2026-09-13T12:30:00.000Z"
  }
]
```

---

## Error Format

JSON error responses share a consistent shape:

```json
{
  "error": "MACHINE_ERROR_CODE",
  "detail": "Human-readable explanation"
}
```

| Code | Status | Meaning |
|---|---|---|
| `UNAUTHENTICATED` | `401` | A protected route was reached but the caller's claims could not be read from the token |
| `NOT_FOUND` | `404` | Machine not found, or caller does not own it (ownership failures are indistinguishable from missing machines) |
| `USER_NOT_FOUND` | `404` | No user with that email has signed in yet (sharing) |
| `CANNOT_SHARE_WITH_SELF` | `400` | The owner cannot be their own share recipient |
| `VALIDATION_ERROR` | `400` | Request body or path parameters failed Zod validation; includes `issues: [{ path, message }]` |

Two authentication edge cases are handled at the middleware level, before any controller runs:

- A **missing or invalid JWT** is rejected by the `express-oauth2-jwt-bearer` middleware with its own `401` response.
- When **Auth0 is not configured** (`AUTH0_ISSUER_BASE_URL` / `AUTH0_AUDIENCE` unset, common in early development), protected routes respond `503` with `{ "error": "Authentication is not configured", "detail": ... }` so the API can still boot.

Validation errors carry an additional `issues` array from Zod:

```json
{
  "error": "VALIDATION_ERROR",
  "detail": "Request body failed validation",
  "issues": [{ "path": ["email"], "message": "Invalid email address" }]
}
```

Malformed machine ids (not 24 hex characters) are rejected with `VALIDATION_ERROR` at the route level rather than reaching the database, where they would surface as a server error.

---

**Related**: [Authentication & Security](authentication.md) | [Data Models](data-models.md) | [Sharing](../Features/sharing.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
