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

All endpoints below require a valid Auth0 JWT sent as `Authorization: Bearer <token>`. The token's `sub` claim scopes every query; a machine **shared with** the caller can be read. An **editor** may also update it (`PUT`), but only its owner may rename or delete it, or manage its shares.

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

List items omit the heavy fields (`source`, `positions`, `testCases`) — fetch one machine by id for full content. Sorted by `updatedAt` descending.

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

With `?include=preview`, each item also carries its `source` and `positions`, enough to draw a thumbnail of its diagram without fetching machines one by one. Any other `include` value is refused with `VALIDATION_ERROR`, and without `include` the response is as above.

#### `POST /api/machines`

Creates a machine owned by the caller. Returns **201** with the full machine response.

```json
{
  "name": "Binary counter",
  "source": "states: ...\nstart: q0\n...",
  "positions": { "q0": { "x": 100, "y": 200 } },
  "testCases": [{ "id": "t1", "input": "0110", "expectation": { "kind": "accepts" } }]
}
```

- `name` is optional (defaults to `Untitled machine`, max 200 chars after trim); `source` and `positions` are required.
- `testCases` is optional (defaults to none) and at most 500 entries. Each needs an `id` (up to 100 characters), an `input` (may be empty, up to 10,000 characters), and an `expectation` whose `kind` is `accepts`, `rejects`, or `final-tape` — the last with a `tape` (up to 10,000 characters). Only the shape is checked; a malformed case fails the whole request with `VALIDATION_ERROR`.

#### `GET /api/machines/:id`

Full machine response, including its `testCases` — a recipient sees the owner's test cases too. `role` is the caller's own: `owner`, `editor` or `viewer`. `sharedWith` is only included when the caller is the owner — a recipient has no business knowing who else the machine was shared with.

```json
{
  "id": "64f1a2b3c4d5e6f7a8b9c0d1",
  "name": "Binary counter",
  "source": "states: ...\nstart: q0\n...",
  "positions": { "q0": { "x": 100, "y": 200 } },
  "testCases": [{ "id": "t1", "input": "0110", "expectation": { "kind": "accepts" } }],
  "owner": "auth0|1234567890",
  "isOwner": true,
  "role": "owner",
  "sharedWith": [{ "sub": "auth0|9876543210", "role": "editor" }],
  "createdAt": "2026-09-13T10:00:00.000Z",
  "updatedAt": "2026-09-13T12:30:00.000Z"
}
```

#### `PUT /api/machines/:id`

Updates a machine. Every field is optional, and a field left out keeps its stored value, so a client can save one part of a machine without resending the rest. That also means a client that predates test cases cannot wipe them. Returns the updated machine response.

A save that changes the source, positions, or test cases is also applied to the machine's collaboration room: to the open room if there is one, so everyone in it sees the save, or otherwise to the room state stored with the machine. See [WebSocket Events](websocket-events.md#the-room). A name-only update leaves the room alone.

#### `PATCH /api/machines/:id`

Rename only. Body: `{ "name": "New name" }` (1–200 chars after trimming, so a machine cannot be renamed to a blank string).

#### `DELETE /api/machines/:id`

Deletes the machine. Returns **204 No Content**.

---

## Sharing (`/api/machines/:id/shares`)

Only a machine's **owner** may view, add, change, or revoke its shares. Each person has a role: `viewer` (open and run the machine) or `editor` (also change it). Ownership is checked before any email lookup, so a caller cannot use someone else's machine id to probe which emails have accounts.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/machines/:id/shares` | List who the machine is shared with |
| `POST` | `/api/machines/:id/shares` | Share with a user by email |
| `PATCH` | `/api/machines/:id/shares/:sub` | Change one user's role |
| `DELETE` | `/api/machines/:id/shares/:sub` | Revoke one user's access |

A change of role, a revoke, or deleting the machine also applies straight away to anyone who has the machine open in a collaboration room. See [WebSocket Events](websocket-events.md#access-changes-while-connected).

#### `POST /api/machines/:id/shares`

```json
{ "email": "classmate@example.com", "role": "editor" }
```

- The email is trimmed and lowercased, then resolved against the **users collection** — a recipient must have signed in to the app at least once. When two Auth0 identities share an email, the most recently active one wins.
- `role` is optional and defaults to `viewer`.
- Storing the share is a set-union on the machine's `sharedWith` list, and `editors` for an editor. Sharing again with someone who already has access sets their role. Sharing never changes the machine's `updatedAt` (it changes who can see the machine, not the machine).
- Returns the updated share list as user summaries:

```json
[
  { "sub": "auth0|9876543210", "email": "classmate@example.com", "name": "Classmate", "role": "editor" }
]
```

#### `GET /api/machines/:id/shares`

Same response shape as above. A `sub` with no user record still appears, with `null` email and name.

#### `PATCH /api/machines/:id/shares/:sub`

Body: `{ "role": "viewer" }` or `{ "role": "editor" }`. Only for someone the machine is already shared with; sharing with someone new goes through `POST`, by email. Returns the updated share list, or **404** if the machine does not exist, the caller does not own it, or it is not shared with that user.

#### `DELETE /api/machines/:id/shares/:sub`

Revokes that user's access. Returns **204** whether or not the user had access; **404** if the machine does not exist or the caller does not own it.

#### `GET /api/machines/shared-with-me`

Lists machines other users have shared with the caller — summaries (no `source`, `positions`, or `testCases`) with the owner identified, sorted by `updatedAt` descending.

```json
[
  {
    "id": "64f1a2b3c4d5e6f7a8b9c0d1",
    "name": "Binary counter",
    "owner": { "sub": "auth0|1234567890", "email": "owner@example.com", "name": "Owner" },
    "isOwner": false,
    "role": "viewer",
    "createdAt": "2026-09-13T10:00:00.000Z",
    "updatedAt": "2026-09-13T12:30:00.000Z"
  }
]
```

`?include=preview` works here as it does on `GET /api/machines`.

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
Test-case persistence (M3b) was documented with the assistance of: Claude Code [Claude Opus 5].
The list preview, collaboration-room saves, and share roles were documented with the assistance of: Claude Code [Claude Opus 5.5].
