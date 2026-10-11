# Backend Architecture

The backend is an Express 5 application. It stores Turing machines in MongoDB, shares them between users, runs the live collaboration rooms over a WebSocket, and marks machines against test cases for outside callers. It is built with the `createApp()` factory pattern for testability.

---

## Overview

- **Framework**: Express 5 (`express@^5.2.1`)
- **Language**: TypeScript 6.0.3, ESM modules
- **Runtime**: Node.js 24.x
- **Default port**: `4000` (chosen to avoid conflict with VS Code Live Preview on 3000)
- **Entry point**: `src/index.ts`
- **One server, two protocols**: the REST API and the collaboration WebSocket share one HTTP server and one port

---

## Application Factory

The app uses a `createApp()` factory in `src/app.ts`, separated from `src/index.ts`:

- `createApp()` — returns a configured Express instance without binding a port or connecting to a database. This lets tests drive the app directly with Supertest.
- `index.ts` — handles environmental concerns: reading config, connecting to MongoDB (five attempts, waiting 1, 2, 4 and 8 seconds between them), starting the HTTP listener, and attaching the collaboration server to it.

### Layers

A request passes through **route → middleware → controller → Mongoose model**. There is no separate service layer: controllers call the models directly. The collaboration code in `src/collab/` sits beside these layers, and the controllers call into it when a save or a change of access has to reach an open room.

### Middleware

| Middleware | Configuration |
|---|---|
| `cors` | Origin restricted to `CORS_ORIGIN` env var, defaults to `*` |
| `express.json()` | Parses JSON request bodies, up to the default 100 kB |
| `createAuthMiddleware()` | Auth0 JWT validation via `express-oauth2-jwt-bearer`; applied to every route that needs a sign-in. Answers `503` when Auth0 is not configured |
| `createUserRecordMiddleware()` | Runs after JWT validation: upserts a `UserModel` record for the caller keyed on the token's `sub`, refreshing email/name from token claims. Skips when MongoDB is not connected; a failed write is logged, never fails the request |
| `createApiKeyAuth()` | Checks the `X-API-Key` header on the marking endpoint: hashes the key and looks the hash up in the `apikeys` collection |
| `validateBody(schema)` / `validateParams(schema)` / `validateQuery(schema)` | Zod validation of the request body, path parameters and query at the API boundary |

On a signed-in route the order is auth, user record, validation, controller, so a caller with no token gets `401` before a bad body gets `400`. The marking route checks the key first for the same reason.

There is no rate limiting, request logging, or custom error handler. An error a controller does not catch is answered by Express's own handler.

---

## Endpoints

Full request/response reference: [REST Endpoints](../API%20Documentation/rest-endpoints.md).

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/health` | None | Liveness check. Returns `{"status":"ok"}` |
| `GET` | `/api/me` | JWT | Returns the authenticated user's claims (`sub`, `email`, `name`) |
| `GET` | `/api/machines` | JWT | List the caller's machines (summaries, newest first; `?include=preview` adds what a thumbnail needs) |
| `GET` | `/api/machines/shared-with-me` | JWT | Machines shared with the caller |
| `GET` | `/api/machines/:id` | JWT | Get one machine (owner, editors and viewers) |
| `POST` | `/api/machines` | JWT | Create a machine (201) |
| `PUT` | `/api/machines/:id` | JWT | Update the content (owner and editors) |
| `PATCH` | `/api/machines/:id` | JWT | Rename (owner only). `PUT` also accepts a `name`, from the owner or an editor |
| `DELETE` | `/api/machines/:id` | JWT | Delete (owner only, 204) |
| `GET` / `POST` | `/api/machines/:id/shares` | JWT | List or add share recipients (owner only) |
| `PATCH` | `/api/machines/:id/shares/:sub` | JWT | Change one recipient's role (owner only) |
| `DELETE` | `/api/machines/:id/shares/:sub` | JWT | Revoke one recipient's access (owner only) |
| `POST` / `GET` | `/api/api-keys` | JWT | Create or list the caller's API keys |
| `DELETE` | `/api/api-keys/:id` | JWT | Revoke an API key |
| `POST` | `/api/mark` | API key | Run test cases against a machine |
| WebSocket | `/collab/:machineId` | JWT in `?token=` | The machine's collaboration room |

Every machine query is scoped by the caller's Auth0 `sub` claim. A machine the caller may not see answers `404`, never `403`, so the API does not reveal that it exists.

The backend stores machine **content without validating machine semantics** when saving. The one place it uses the Core package is the marking endpoint, which parses or deserialises the machine, validates it, and runs each test case with Core's `runTestCase()`.

---

## Real-Time Collaboration

`src/collab/` runs a WebSocket server (`ws`) on the same HTTP server as the REST API. The wire protocol is the one `y-websocket` clients speak, written by hand on `y-protocols` rather than taken from the `y-websocket` package. See [WebSocket Events](../API%20Documentation/websocket-events.md) for the messages and close codes.

| File | Role |
|---|---|
| `server.ts` | Accepts the upgrade, speaks the protocol, relays updates and presence, and closes connections with the right code |
| `authorise.ts` | Checks the `Origin`, verifies the access token with `jose`, and looks up the caller's role on the machine |
| `room.ts` | `RoomManager`: one Yjs document per machine, seeded from the stored machine, and written back to MongoDB |
| `rooms.ts` | The single shared `RoomManager` |
| `access.ts` | Carries a change of access from a REST controller to the open connections |

- A room is created when the first person joins and released when the last one leaves.
- A room writes its content back about 2 seconds after the last change, at least every 10 seconds while editing continues, and when the last person leaves. A failed write is tried again and the room is kept until it saves.
- A newly seeded room is stored before any client receives it, so the machine has one edit history from the start.
- A viewer's presence is relayed, but anything a viewer sends to change the document is dropped.
- A `PUT` over REST is applied to the open room, or to the stored room state, as ordinary edits.

---

## Database

- **Engine**: MongoDB Atlas (managed cloud, M0 free tier)
- **ODM**: Mongoose 9
- **Optional at startup**: If `MONGODB_URI` is unset, the server logs a warning and starts without a database connection. This allows the API to run before MongoDB is provisioned; machine persistence endpoints require the database.

### Collections

The field-by-field schema is in [Data Models](../API%20Documentation/data-models.md#database-schema-mongodb). In short:

| Collection | Model | Holds |
|---|---|---|
| `machines` | `src/models/machine.ts` | One document per machine: `owner`, `name`, `source`, `positions`, `testCases`, `sharedWith`, `editors`, and the room's `collabState` |
| `users` | `src/models/user.ts` | `sub`, `email`, `name` for everyone who has signed in. No credentials |
| `apikeys` | `src/models/api-key.ts` | `owner`, `keyHash` (SHA-256), `label`. The key itself is never stored |

All three use Mongoose timestamps. Documents refer to people by Auth0 `sub`.

The users collection only resolves a `sub` to the email and name the token last carried, so an owner can share a machine by email. A recipient must have used the app at least once before a machine can be shared with them.

---

## Environment Variables

| Variable | Purpose | Default |
|---|---|---|
| `PORT` | Port the API listens on | `4000` |
| `MONGODB_URI` | MongoDB connection string | *(unset — runs without DB)* |
| `CORS_ORIGIN` | Origin permitted to call this API, and to open a collaboration WebSocket | `*` |
| `AUTH0_ISSUER_BASE_URL` | Auth0 tenant URL for JWT validation | *(unset — signed-in routes answer 503)* |
| `AUTH0_AUDIENCE` | Auth0 API identifier this server accepts tokens for | *(unset — signed-in routes answer 503)* |
| `API_KEY_HEADER_NAME` | Header that carries the API key on the marking endpoint | `X-API-Key` |

`npm run dev` loads a `.env` file if there is one. `npm start` does not; in production the values are set on the Azure App Service.

---

## Project Structure

```
src/
  app.ts                 createApp() factory — builds Express app
  index.ts               entry point: config, MongoDB connect, HTTP listen, collab server
  collab/                the collaboration WebSocket: server, authorise, room, rooms, access
  config/                claims.ts — reads the caller's identity from the validated token
  routes/                machine, api-key and mark routes
  controllers/           machine, share, api-key and mark request handlers
  models/                machine.ts, user.ts, api-key.ts — Mongoose schemas and models
  middleware/            auth (JWT), api-key, user-record, validate (Zod)
  schemas/               machine, share, mark, params — Zod request validation schemas
```

Tests sit beside the code they cover as `*.test.ts`. `load-tests/` at the repository root holds the Artillery load test.

---

## Dependencies

| Package | Version | Role |
|---|---|---|
| `express` | ^5.2.1 | HTTP framework |
| `cors` | ^2.8.6 | Cross-origin resource sharing |
| `mongoose` | ^9.9.3 | MongoDB ODM |
| `zod` | ^4.4.3 | Request validation |
| `express-oauth2-jwt-bearer` | ^1.10.0 | JWT auth middleware for the REST API |
| `jose` | ^6.2.12 | Verifies the access token on a WebSocket connection |
| `ws` | ^8.21.3 | WebSocket server |
| `yjs` | ^13.6.32 | The shared document behind a collaboration room |
| `y-protocols` | ^1.0.7 | The sync and awareness messages a room exchanges |
| `lib0` | ^0.2.117 | Encoding and decoding of those messages |
| `@brh/automata-core` | ^4.0.0 | Parses, validates and runs machines for the marking endpoint |

---

**Related**: [System Overview](system-overview.md) | [Frontend Architecture](frontend-architecture.md) | [Core Package](core-package.md) | [WebSocket Events](../API%20Documentation/websocket-events.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
