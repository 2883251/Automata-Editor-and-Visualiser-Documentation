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

### Middleware (currently registered)

| Middleware | Configuration |
|---|---|
| `cors` | Origin restricted to `CORS_ORIGIN` env var, defaults to `*` |
| `express.json()` | Parses JSON request bodies |

---

## Current Endpoints

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Liveness check. Returns `{"status":"ok"}` |

!!! info "Status: Planned"
    The following features have dependencies installed but are not yet implemented:

    - **Mongoose models** (`mongoose@^9.9.3`) — machine CRUD persistence to MongoDB Atlas
    - **Zod schemas** (`zod@^4.4.3`) — request body and parameter validation
    - **JWT auth** (`express-oauth2-jwt-bearer@^1.10.0`) — route protection via Auth0 token validation
    - **Route modules** in `src/routes/`, `src/controllers/`, `src/middleware/`, `src/schemas/` — currently contain `.gitkeep` placeholders

---

## Database

- **Engine**: MongoDB Atlas (managed cloud, M0 free tier)
- **ODM**: Mongoose 9
- **Optional at startup**: If `MONGODB_URI` is unset, the server logs a warning and starts without a database connection. This allows the API to run before MongoDB is provisioned.

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
  routes/                route definitions (placeholder)
  controllers/           request handlers (placeholder)
  models/                Mongoose schemas and models (placeholder)
  middleware/            auth, validation, error handling (placeholder)
  schemas/               Zod schemas for request validation (placeholder)
  config/                configuration helpers (placeholder)
  types/                 shared type declarations (placeholder)
```

---

## Dependencies

| Package | Version | Role |
|---|---|---|
| `express` | ^5.2.1 | HTTP framework |
| `cors` | ^2.8.6 | Cross-origin resource sharing |
| `mongoose` | ^9.9.3 | MongoDB ODM (planned) |
| `zod` | ^4.4.3 | Request validation (planned) |
| `express-oauth2-jwt-bearer` | ^1.10.0 | JWT auth middleware (planned) |
| `@brh/automata-core` | ^2.0.0 | Shared machine model |

---

**Related**: [System Overview](system-overview.md) | [Frontend Architecture](frontend-architecture.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
