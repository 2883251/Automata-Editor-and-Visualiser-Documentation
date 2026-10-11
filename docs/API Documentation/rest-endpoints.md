# REST Endpoints

Reference for all HTTP endpoints exposed by the backend API (Express 5, `Automata-Editor-and-Visualiser-Backend`). Examples assume a local API at `http://localhost:4000`. The deployed API is at `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net`.

To call the API from a script, start with [Using the API](using-the-api.md).

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

All endpoints below require a valid Auth0 JWT sent as `Authorization: Bearer <token>`. The token's `sub` claim scopes every query; a machine **shared with** the caller can be read. An **editor** may also update it (`PUT`), but only its owner may delete it, manage its shares, or rename it with `PATCH`. `PUT` accepts a `name` as well, so an editor's update can change the name; the app itself offers renaming to the owner only.

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
| `PUT` | `/api/machines/:id` | Update the content (fields left out are kept) |
| `PATCH` | `/api/machines/:id` | Rename (owner only) |
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
- A request body may be at most 100 kB.
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

Updates a machine. Every field is optional, and a field left out keeps its stored value, so a client can save one part of a machine without resending the rest. That also means a client that predates test cases cannot wipe them. The owner and editors may call it; a viewer gets `404`. The fields are `name`, `source`, `positions` and `testCases`, so an editor's update may carry a new name. Returns the updated machine response.

A save that changes the source, positions, or test cases is also applied to the machine's collaboration room: to the open room if there is one, so everyone in it sees the save, or otherwise to the room state stored with the machine. See [WebSocket Events](websocket-events.md#the-room). A name-only update leaves the room alone.

#### `PATCH /api/machines/:id`

Rename only, and owner only. Body: `{ "name": "New name" }` (1–200 chars after trimming, so a machine cannot be renamed to a blank string).

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
- Returns **200** with the updated share list as user summaries:

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

## API Key Management (`/api/api-keys`)

API keys let external tools and scripts (grading systems, CI pipelines, LMS integrations) authenticate to the marking endpoint without a browser session. Keys are scoped to the user who creates them and stored as SHA-256 hashes — the plain-text value is returned exactly once at creation and cannot be retrieved later.

All three endpoints require a valid Auth0 JWT (`Authorization: Bearer <token>`).

| Method | Path | Description |
|---|---|---|
| `POST` | `/api/api-keys` | Create a new API key |
| `GET` | `/api/api-keys` | List the caller's API keys (metadata only) |
| `DELETE` | `/api/api-keys/:id` | Revoke an API key |

#### `POST /api/api-keys`

Creates a new API key. The plain-text key is returned **exactly once** — the server stores only a SHA-256 hash and cannot display it again.

```json
{ "label": "Sprint 4 marking" }
```

- `label` is required (1–200 characters after trimming).
- Returns **201 Created**:

```json
{
  "id": "66f8a1b2c3d4e5f6a7b8c9d0",
  "label": "Sprint 4 marking",
  "key": "e3b0c44298fc1c149afbf4c8996fb924...",
  "createdAt": "2026-09-22T10:30:00.000Z"
}
```

#### `GET /api/api-keys`

Lists the caller's API keys. Only metadata is returned — the key value is never included.

```json
[
  {
    "id": "66f8a1b2c3d4e5f6a7b8c9d0",
    "label": "Sprint 4 marking",
    "createdAt": "2026-09-22T10:30:00.000Z"
  }
]
```

#### `DELETE /api/api-keys/:id`

Revokes (deletes) an API key. Returns **204 No Content**. The lookup is scoped to the caller's `sub`, so a missing or another user's key returns **404** rather than **403** to avoid leaking existence.

---

## Marking (`/api/mark`)

The marking endpoint lets external callers submit a Turing machine together with test cases and receive per-case results. It is designed for grading systems, CI pipelines, and LMS integrations that operate without a browser session.

Unlike the machine and sharing endpoints, marking uses **API key authentication** (not JWT). The caller sends a key in the `X-API-Key` header (configurable via `API_KEY_HEADER_NAME`). See [Authentication & Security](authentication.md#api-key-authentication) for details.

### `POST /api/mark`

Runs test cases against a machine and returns per-case results with a summary.

- **Authentication**: `X-API-Key` header (not JWT)
- **Content-Type**: `application/json`

#### Request body

```json
{
  "machine": {
    "kind": "source",
    "text": "states:\n  q0\n  yes\n  no\n\nstart: q0\naccept: yes\nreject: no\n\ntape_alphabet: '_', '0', '1'\ninput_alphabet: '0', '1'\n\ntransitions:\n  (q0, '0') -> (q0, '0', R)\n  (q0, '1') -> (no, '1', R)\n  (q0, '_') -> (yes, '_', R)\n"
  },
  "testCases": [
    { "input": "000", "expectation": { "kind": "accepts" } },
    { "input": "010", "expectation": { "kind": "rejects" } },
    { "input": "01", "expectation": { "kind": "final-tape", "tape": "11" } }
  ],
  "maxSteps": 1000
}
```

The `machine` field accepts two formats via a discriminated union:

| `kind` | Fields | Description |
|---|---|---|
| `source` | `text` (string, non-empty) | Raw instruction language text, parsed by the Core lexer and parser |
| `serialised` | `data` (object) | A serialised machine document: the JSON that Core's `serialise()` writes (`{ schema, machine, positions }`), passed to the Core `deserialise` function |

Each test case has an `input` string and an `expectation`:

| Expectation `kind` | Meaning |
|---|---|
| `accepts` | The machine should halt in an accepting state |
| `rejects` | The machine should halt in a rejecting state, or get stuck (no transition applies) |
| `final-tape` | The first tape should match `tape` after the run. Blanks at either end are ignored, and so is the position on the tape. The machine need not accept |

`testCases` needs at least one case. `maxSteps` is optional. When omitted, the Core default (`DEFAULT_STEP_LIMIT`, 10,000) applies. The ceiling is 10,000,000; values above it are rejected with `VALIDATION_ERROR`.

A machine is run as a deterministic one. A machine marked `nondeterministic` follows the first transition written for each choice.

#### Response

Returns **200 OK** with per-case results and a summary. This is the reply to the request above:

```json
{
  "results": [
    {
      "testCase": { "input": "000", "expectation": { "kind": "accepts" } },
      "outcome": "accepted",
      "passed": true,
      "measurement": { "kind": "measured", "inputLength": 3, "steps": 4, "cells": 5 }
    },
    {
      "testCase": { "input": "010", "expectation": { "kind": "rejects" } },
      "outcome": "rejected",
      "passed": true,
      "measurement": { "kind": "measured", "inputLength": 3, "steps": 2, "cells": 3 }
    },
    {
      "testCase": { "input": "01", "expectation": { "kind": "final-tape", "tape": "11" } },
      "outcome": "rejected",
      "passed": false,
      "measurement": { "kind": "measured", "inputLength": 2, "steps": 2, "cells": 3 },
      "comparison": {
        "expected": "11",
        "actual": "01",
        "origin": 0,
        "firstDifference": 0,
        "firstDifferencePosition": 0
      }
    }
  ],
  "summary": {
    "total": 3,
    "passed": 2,
    "failed": 1,
    "excluded": 0
  }
}
```

Each result entry includes:

- `testCase` — the input and expectation echoed back
- `outcome` — one of `accepted`, `rejected`, `stuck`, `exceeded-limit`
- `passed` — whether the outcome matched the expectation
- `measurement` — `kind: "measured"` with the resource usage (`inputLength`, `steps`, `cells`), or `kind: "excluded"` with `inputLength`, `reason` and `limit` when the step budget ran out
- `comparison` — present only for `final-tape` expectations; shows `expected`, `actual`, `origin`, `firstDifference`, and `firstDifferencePosition`. The last two are `null` when the tapes match

The `summary` counts `total`, `passed`, `failed`, and `excluded` (cases that exceeded the step budget, which count as neither passed nor failed).

#### Marking error responses

| Code | Status | Meaning |
|---|---|---|
| `UNAUTHENTICATED` | `401` | Missing `X-API-Key` header |
| `FORBIDDEN` | `403` | Unrecognised API key |
| `VALIDATION_ERROR` | `400` | Request body failed schema validation (empty test cases, `maxSteps` above ceiling, etc.) |
| `PARSE_ERROR` | `400` | Source text contains syntax errors; a `diagnostics` array lists each one as `{ severity, message, range }`, where `range` has `start`, `end`, `line` and `column` |
| `INVALID_MACHINE` | `400` | The machine definition is semantically invalid (undefined states, malformed serialised data). `detail` says why, and some cases also carry a `diagnostics` array |
| `INTERNAL_ERROR` | `500` | An unexpected fault while marking |

No data is persisted. The endpoint is a pure computation — nothing is written to the database.

---

## REST Architecture Conventions

The API follows REST resource-oriented design principles consistently across all endpoints.

### Resource-oriented URLs

Every endpoint addresses a resource or a collection of resources, never an action:

| Pattern | Example | Meaning |
|---|---|---|
| Collection | `/api/machines` | The set of machines belonging to the caller |
| Item | `/api/machines/:id` | One machine |
| Sub-collection | `/api/machines/:id/shares` | The shares belonging to one machine |
| Sub-item | `/api/machines/:id/shares/:sub` | One share on one machine |
| Separate collection | `/api/api-keys` | The caller's API keys |
| Computation | `/api/mark` | A stateless marking run (no resource created) |

### HTTP method semantics

| Method | Used for | Idempotent |
|---|---|---|
| `GET` | Reading a resource or collection | Yes |
| `POST` | Creating a resource, or a computation that cannot map to CRUD (`/api/mark`) | No |
| `PUT` | Updating a machine's content. Fields left out keep their stored value | Yes |
| `PATCH` | Partial update (rename a machine, change a share's role) | Yes |
| `DELETE` | Removing a resource (machine, share, API key) | Yes |

### Status codes

| Code | When |
|---|---|
| `200 OK` | Successful read, update, share, or computation |
| `201 Created` | Resource created (machine, API key) |
| `204 No Content` | Successful delete or revoke — no body |
| `400 Bad Request` | Validation failure, parse error, or semantic conflict |
| `401 Unauthorized` | Missing or invalid credentials |
| `403 Forbidden` | An API key was sent but is not recognised (marking endpoint) |
| `404 Not Found` | Resource does not exist or caller does not own it |
| `500 Internal Server Error` | Unexpected server fault (`INTERNAL_ERROR` on the marking endpoint) |
| `503 Service Unavailable` | Auth0 not configured (development bootstrap) |

### Ownership and security

- Every machine route requires a valid Auth0 JWT; the caller's `sub` claim scopes all queries to the machines they own or that are shared with them.
- A request for a machine the caller does not own returns `404`, not `403` — ownership failures are indistinguishable from missing resources, so the API leaks no information about other users' data.
- The marking endpoint authenticates with an `X-API-Key` header instead of a JWT, allowing external callers (grading scripts, CI) without a browser session.

### Validation

Every route that takes a body, path parameter or query passes it through Zod validation middleware (`validateBody`, `validateParams`, `validateQuery`) before reaching the controller. Malformed input is rejected at the route level with a `400` and a structured `issues` array, rather than surfacing as a database error.

### Consistent error shape

Error responses written by the API use one JSON structure (`{ "error": "CODE", "detail": "..." }`) whichever endpoint or middleware produced them. Two responses fall outside it: a missing or invalid JWT is answered by the Auth0 middleware with a bare `401`, and `GET /api/me` answers `{ "error": "No valid token provided" }` with no `detail`. See [Error Format](#error-format) below.

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
| `UNAUTHENTICATED` | `401` | A protected route was reached but the caller's claims could not be read from the token, or the `X-API-Key` header is missing on the marking endpoint |
| `FORBIDDEN` | `403` | The API key in the `X-API-Key` header is not recognised (marking endpoint only) |
| `NOT_FOUND` | `404` | Machine not found, or caller does not own it (ownership failures are indistinguishable from missing machines); also returned for API key revocation of a missing or another user's key |
| `USER_NOT_FOUND` | `404` | No user with that email has signed in yet (sharing) |
| `CANNOT_SHARE_WITH_SELF` | `400` | The owner cannot be their own share recipient |
| `VALIDATION_ERROR` | `400` | Request body or path parameters failed Zod validation; includes `issues: [{ path, message }]` |
| `PARSE_ERROR` | `400` | Source text contains syntax errors (marking endpoint); includes a `diagnostics` array |
| `INVALID_MACHINE` | `400` | Machine definition is semantically invalid (marking endpoint) |
| `INTERNAL_ERROR` | `500` | An unexpected fault (marking endpoint) |

Two authentication edge cases are handled at the middleware level, before any controller runs:

- A **missing or invalid JWT** is rejected by the `express-oauth2-jwt-bearer` middleware with its own `401` response. Its body is not JSON, so a client should go by the status code.
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
API key management and the marking endpoint were documented with the assistance of: Qoder IDE [auto].
REST architecture conventions were documented with the assistance of: Qoder-IDE [Qwen3.8-Max].
The marking example and error details were corrected with the assistance of: Claude Code [Claude Opus 5.5].
