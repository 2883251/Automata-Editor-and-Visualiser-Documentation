# Backend Architecture

The backend is an Express 5 application that persists Turing machine definitions to MongoDB and serves them to the frontend. It is built with the `createApp()` factory pattern for testability.

---

## Overview

- **Framework**: Express 5 (`express@^5.2.1`)
- **Language**: TypeScript 6.0.3, ESM modules
- **Runtime**: Node.js 24.x
- **Default port**: `4000` (chosen to avoid conflict with VS Code Live Preview on 3000)
- **Entry point**: `src/index.ts`

---

## Application Factory

The app uses a `createApp()` factory in `src/app.ts`, separated from `src/index.ts`:

- `createApp()` — returns a configured Express instance without binding a port or connecting to a database. This lets tests drive the app directly with Supertest.
- `index.ts` — handles environmental concerns: reading config, connecting to MongoDB, and starting the HTTP listener.

### Middleware

| Middleware | Configuration |
|---|---|
| `cors` | Origin restricted to `CORS_ORIGIN` env var, defaults to `*` |
| `express.json()` | Parses JSON request bodies |
| `createAuthMiddleware()` | Auth0 JWT validation via `express-oauth2-jwt-bearer`; applied to every protected route |
| `createUserRecordMiddleware()` | Runs after JWT validation: upserts a `UserModel` record for the caller keyed on the token's `sub`, refreshing email/name from token claims. Skips when MongoDB is not connected; a failed write is logged, never fails the request |
| `validateBody(schema)` / `validateParams(schema)` | Zod request-body and path-parameter validation at the API boundary |

---

## Endpoints

All machine routes are mounted under `/api/machines` and require authentication. Full request/response reference: [REST Endpoints](../API%20Documentation/rest-endpoints.md).

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Liveness check. Returns `{"status":"ok"}` |
| `GET` | `/api/me` | Returns the authenticated user's claims (`sub`, `email`, `name`) |
| `GET` | `/api/machines` | List the caller's machines (summaries, newest first) |
| `GET` | `/api/machines/shared-with-me` | Machines shared with the caller |
| `GET` | `/api/machines/:id` | Get one machine (readable by owner and share recipients) |
| `POST` | `/api/machines` | Create a machine (201) |
| `PUT` | `/api/machines/:id` | Full-replacement update |
| `PATCH` | `/api/machines/:id` | Rename |
| `DELETE` | `/api/machines/:id` | Delete (204) |
| `GET` / `POST` | `/api/machines/:id/shares` | List or add share recipients (owner only) |
| `DELETE` | `/api/machines/:id/shares/:sub` | Revoke one recipient's access (owner only) |

Every query is scoped by the caller's Auth0 `sub` claim; a machine shared with the caller can be read, but only its owner may change or delete it. The backend stores machine **content without validating machine semantics** — that is the Core package's job.

---

## Database

- **Engine**: MongoDB Atlas (managed cloud, M0 free tier)
- **ODM**: Mongoose 9
- **Optional at startup**: If `MONGODB_URI` is unset, the server logs a warning and starts without a database connection. This allows the API to run before MongoDB is provisioned; machine persistence endpoints require the database.

### Collections

**machines** (`src/models/machine.ts`):

| Field | Type | Notes |
|---|---|---|
| `owner` | string (Auth0 `sub`) | required, indexed |
| `name` | string | default `'Untitled machine'`, trimmed, max 200 |
| `source` | string | required — the instruction-language source text |
| `positions` | `Map<string, {x, y}>` | diagram layout positions |
| `sharedWith` | string[] | Auth0 `sub`s of share recipients |
| `createdAt` / `updatedAt` | dates | Mongoose timestamps |

Compound indexes: `{ owner: 1, updatedAt: -1 }` and `{ sharedWith: 1, updatedAt: -1 }`.

**users** (`src/models/user.ts`):

| Field | Type | Notes |
|---|---|---|
| `sub` | string | required, unique — Auth0 subject |
| `email` | string (optional) | lowercased, trimmed, indexed |
| `name` | string (optional) | trimmed |

The users collection holds **no credentials** — Auth0 owns identity. It only resolves a `sub` to the email and name the token last carried, so an owner can share a machine by email. A recipient must have used the app at least once before a machine can be shared with them.

---

## Environment Variables

| Variable | Purpose | Default |
|---|---|---|
| `PORT` | Port the API listens on | `4000` |
| `MONGODB_URI` | MongoDB connection string | *(unset — runs without DB)* |
| `CORS_ORIGIN` | Origin permitted to call this API | `*` |
| `AUTH0_ISSUER_BASE_URL` | Auth0 tenant URL for JWT validation | *(unset)* |
| `AUTH0_AUDIENCE` | Auth0 API identifier this server accepts tokens for | *(unset)* |

---

## Project Structure

```
src/
  app.ts                 createApp() factory — builds Express app
  index.ts               entry point: config, MongoDB connect, HTTP listen
  config/                claims.ts — reads the caller's identity from the validated token
  routes/                machine.routes.ts — machine CRUD + sharing routes
  controllers/           machine.controller.ts, share.controller.ts — request handlers
  models/                machine.ts, user.ts — Mongoose schemas and models
  middleware/            auth (JWT), user-record, validate (Zod)
  schemas/               machine, share, params — Zod request validation schemas
  types/                 shared type declarations
```

---

## Dependencies

| Package | Version | Role |
|---|---|---|
| `express` | ^5.2.1 | HTTP framework |
| `cors` | ^2.8.6 | Cross-origin resource sharing |
| `mongoose` | ^9.9.3 | MongoDB ODM |
| `zod` | ^4.4.3 | Request validation |
| `express-oauth2-jwt-bearer` | ^1.10.0 | JWT auth middleware |
| `@brh/automata-core` | ^2.0.0 | Shared machine model |

---

**Related**: [System Overview](system-overview.md) | [Frontend Architecture](frontend-architecture.md) | [Core Package](core-package.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
