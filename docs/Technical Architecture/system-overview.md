# System Overview

The Automata Editor and Visualiser is a dual-view editor for Turing Machines that keeps a state-diagram view and a text-based instruction view bidirectionally synchronised. It is built across four independent repositories that deploy and scale separately.

---

## Four-Repository Architecture

| Repository | Purpose | Deployed to |
|---|---|---|
| `Automata-Editor-and-Visualiser-Core` | Shared TypeScript core — machine model, parser, simulator, computation tree, test cases | Gitea npm registry (`@brh/automata-core`) |
| `Automata-Editor-and-Visualiser-Frontend` | React SPA — diagram editor, instruction editor, TM simulator, computation tree, test cases, export, machine management, sharing, in-app help | Azure App Service |
| `Automata-Editor-and-Visualiser-Backend` | Express API server — machine persistence, sharing, collaboration rooms, API keys and marking | Azure App Service |
| `Automata-Editor-and-Visualiser-Documentation` | MkDocs + Material theme documentation site | GitHub Pages |

Frontend and backend are separate applications satisfying the non-monolithic architecture requirement.

| Deployed | Address |
|---|---|
| App (frontend) | `https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net` |
| API (backend) | `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net` |

---

## Communication Patterns

- **Frontend ↔ Backend, REST**: JSON over HTTP for saving, opening, sharing and API keys. The frontend sends requests to the backend API (base URL configured via `VITE_API_URL`).
- **Frontend ↔ Backend, WebSocket**: a machine that is shared for editing opens a live collaboration room on the same server, at `/collab/<machineId>`. Edits travel as Yjs updates. See [WebSocket Events](../API%20Documentation/websocket-events.md).
- **Outside callers → Backend**: a script or marking tool calls `POST /api/mark` with an API key. See [Using the API](../API%20Documentation/using-the-api.md).
- **Shared Core**: Both frontend and backend consume `@brh/automata-core` from the Gitea npm package registry. This ensures a machine behaves identically whether simulated in the browser or marked on the server. Core follows Semantic Versioning independently of the sprint cadence. The published release is **4.0.0**, and both applications depend on `^4.0.0`.
- **Core package**: Published to `https://sdp.ms.wits.ac.za/api/packages/brh/npm/` under the `@brh` scope. Zero runtime dependencies — simulation logic is pure TypeScript.

---

## Deployment Topology

| Target | Platform | Trigger |
|---|---|---|
| **Frontend** | Azure App Service (Node.js) | Push to `main` → Gitea Actions workflow (`deploy-azure.yml`) builds the Vite production output and deploys it with a small Express server that serves the files |
| **Backend** | Azure App Service (Node.js) | Push to `main` → Gitea Actions workflow (`deploy-azure.yml`) builds TypeScript and deploys `dist/` with its production dependencies |
| **Docs site** | GitHub Pages | Push to `main` → Gitea push-mirrors repo to GitHub → GitHub Actions builds MkDocs (`mkdocs build --strict`) and deploys to GitHub Pages |
| **Core package** | Gitea npm registry | Push a `v*` tag → Gitea Actions workflow (`publish.yml`) runs CI gate then `npm publish` |

All deployment workflows run the gate (lint → typecheck → test → build) before deploying. See [CI/CD Pipeline](../Development%20Guide/ci-cd-pipeline.md).

---

## Authentication Flow

- **Provider**: Auth0 through Universal Login, with Google sign-in or an email address and password
- **Frontend**: `@auth0/auth0-react` SDK handles login/logout, token management, and user profile
- **Backend**: `express-oauth2-jwt-bearer` middleware validates Auth0-issued JWTs on protected routes
- **Flow**: User signs in via Auth0 hosted page → frontend receives JWT access token → token sent as `Authorization: Bearer` header on API requests → backend validates token signature and audience
- **User records**: after JWT validation, the backend upserts a user record from the token's `sub`/email/name claims (an Auth0 post-login Action adds these under a `https://automata-ev/` claim namespace), so machines can be shared with a user by email

- **Collaboration**: the same access token is sent when the WebSocket connects, and the backend verifies it there too
- **Outside callers**: the marking endpoint takes an API key in place of a token

Auth0 sign-in/sign-out (frontend) and JWT route protection (backend) are wired up; see [Authentication & Security](../API%20Documentation/authentication.md).

---

## Technology Stack Summary

| Layer | Technology |
|---|---|
| Language | TypeScript 6.0.3 |
| Runtime | Node.js 24.x |
| Frontend | React 19, Vite 8, Tailwind CSS 4, React Router 8 |
| Diagram editor | React Flow (`@xyflow/react` 12) |
| Code editor | Monaco Editor |
| Backend | Express 5, Mongoose 9, MongoDB Atlas, `ws` |
| Collaborative editing | Yjs (`yjs`, `y-websocket`, `y-indexeddb` in the frontend; `yjs`, `y-protocols` in the backend) |
| Auth | Auth0 (`@auth0/auth0-react` + `express-oauth2-jwt-bearer`) |
| Validation | Zod 4 |
| Shared core | `@brh/automata-core` 4.0.0 (frontend and backend on ^4.0.0) |
| Testing | Vitest, React Testing Library, Playwright, Supertest, Artillery (load) |
| CI/CD | Gitea Actions |
| Version control | Gitea |

---

**Related**: [Backend Architecture](backend-architecture.md) | [Frontend Architecture](frontend-architecture.md) | [Core Package](core-package.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
