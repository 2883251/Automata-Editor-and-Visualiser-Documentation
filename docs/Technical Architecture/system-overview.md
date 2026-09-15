# System Overview

The Automata Editor and Visualiser is a dual-view editor for Turing Machines that keeps a state-diagram view and a text-based instruction view bidirectionally synchronised. It is built across four independent repositories that deploy and scale separately.

---

## Four-Repository Architecture

| Repository | Purpose | Deployed to |
|---|---|---|
| `Automata-Editor-and-Visualiser-Core` | Shared TypeScript core — machine model, parser, simulator, test cases | Gitea npm registry (`@brh/automata-core`) |
| `Automata-Editor-and-Visualiser-Frontend` | React SPA — diagram editor, instruction editor, TM simulator, test cases, export, machine management | Azure App Service |
| `Automata-Editor-and-Visualiser-Backend` | Express API server — machine persistence and sharing | Azure App Service |
| `Automata-Editor-and-Visualiser-Documentation` | MkDocs + Material theme documentation site | GitHub Pages |

Frontend and backend are separate applications satisfying the non-monolithic architecture requirement.

---

## Communication Patterns

- **Frontend ↔ Backend**: REST over HTTP. The frontend sends requests to the backend API (base URL configured via `VITE_API_URL`).
- **Shared Core**: Both frontend and backend consume `@brh/automata-core` from the Gitea npm package registry. This ensures a machine behaves identically whether simulated in the browser or on the server. Core follows Semantic Versioning independently of the sprint cadence — as of sprint 2 the published release is **3.0.0** (multi-tape machines; breaking for 2.x consumers), while the frontend currently consumes `^2.2.0` and the backend `^2.0.0`.
- **Core package**: Published to `https://sdp.ms.wits.ac.za/api/packages/brh/npm/` under the `@brh` scope. Zero runtime dependencies — simulation logic is pure TypeScript.

---

## Deployment Topology

| Target | Platform | Trigger |
|---|---|---|
| **Frontend** | Azure App Service (Node.js B1) | Push to `main` → Gitea Actions workflow (`deploy-azure.yml`) builds Vite production output and deploys `dist/` |
| **Backend** | Azure App Service (Node.js B1) | Push to `main` → Gitea Actions workflow (`deploy-azure.yml`) builds TypeScript and deploys `dist/` |
| **Docs site** | GitHub Pages | Push to `main` → Gitea push-mirrors repo to GitHub → GitHub Actions builds MkDocs (`mkdocs build --strict`) and deploys to `gh-pages` |
| **Core package** | Gitea npm registry | Push a `v*` tag → Gitea Actions workflow (`publish.yml`) runs CI gate then `npm publish` |

All deployment workflows run the full CI gate (lint → typecheck → test → build) before deploying.

---

## Authentication Flow

- **Provider**: Auth0 (Google) with Universal Login
- **Frontend**: `@auth0/auth0-react` SDK handles login/logout, token management, and user profile
- **Backend**: `express-oauth2-jwt-bearer` middleware validates Auth0-issued JWTs on protected routes
- **Flow**: User signs in via Auth0 hosted page → frontend receives JWT access token → token sent as `Authorization: Bearer` header on API requests → backend validates token signature and audience
- **User records**: after JWT validation, the backend upserts a user record from the token's `sub`/email/name claims (an Auth0 post-login Action adds these under a `https://automata-ev/` claim namespace), so machines can be shared with a user by email

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
| Backend | Express 5, Mongoose 9, MongoDB Atlas |
| Auth | Auth0 (`@auth0/auth0-react` + `express-oauth2-jwt-bearer`) |
| Validation | Zod 4 |
| Shared core | `@brh/automata-core` (published 3.0.0; frontend on ^2.2.0, backend on ^2.0.0) |
| Testing | Vitest, React Testing Library, Playwright, Supertest |
| CI/CD | Gitea Actions |
| Version control | Gitea |

---

**Related**: [Backend Architecture](backend-architecture.md) | [Frontend Architecture](frontend-architecture.md) | [Core Package](core-package.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
