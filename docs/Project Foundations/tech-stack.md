# Tech Stack

This page documents the technology choices, frameworks, libraries, and deployment platforms used in the Automata Editor project.

---

## 1. Overview

The application is split across four repositories, all written in **TypeScript** (except docs) and running on **Node.js** (except docs). This document lists every technology chosen, why it was chosen, and where it fits in the architecture.

| Repository | Contents |
|---|---|
| `Automata-Editor-and-Visualiser-Core` | Shared TypeScript core: machine model, parser, simulator, test cases |
| `Automata-Editor-and-Visualiser-Frontend` | Client application (React) |
| `Automata-Editor-and-Visualiser-Backend` | API server (Express) |
| `Automata-Editor-and-Visualiser-Documentation` | MkDocs documentation site source |

---

## 2. Language & Runtime

| Technology | Where | Motivation |
|---|---|---|
| **TypeScript 6.0.3** | Core, Frontend, Backend repos | Strong typing catches errors at compile time, improves IDE support, and makes the shared Core contract explicit across repo boundaries. The team has existing experience with it. Pinned to v6.0.3 for ESLint compatibility (ESLint's TypeScript parser does not yet support TS v7). |
| **Node.js** | Backend repo, build tooling | Mature ecosystem, first-class TypeScript support, and aligns with the rest of the stack. |

### Turing Machine Simulation Core

The TM simulation engine (machine model, parser, simulator) lives in the `Automata-Editor-and-Visualiser-Core` repository and is initially written in TypeScript. This keeps the full stack in one language and simplifies development and debugging during early sprints.

**Future optimisation path:** If performance profiling reveals bottlenecks — particularly for large or nondeterministic computations — the simulation core can be rewritten in a compiled systems language such as **C** or **Rust** and compiled to **WebAssembly (WASM)**. The Core repository's public API would remain unchanged; only the internal implementation would switch from a TypeScript module to a WASM module loaded by the same interface. This migration can happen at any point without requiring changes in the Frontend or Backend consumers.

---

## 3. Frontend (`Automata-Editor-and-Visualiser-Frontend`)

| Technology | Role | Motivation |
|---|---|---|
| **React** | UI framework | Component-based architecture maps well to the editor's distinct panels (diagram, code editor, tape visualiser). Large ecosystem for UI libraries. |
| **Vite** | Dev server & bundler | Fast HMR, native TypeScript support, and straightforward configuration. Significantly faster dev experience than legacy bundlers. |
| **Vitest** | Unit & component testing | Built on Vite's module graph, so tests share the same transform pipeline as the app. Fast execution and a Jest-compatible API. |
| **React Flow** | State diagram visual editor | Purpose-built library for interactive node-and-edge diagrams. Supports dragging, custom node rendering, and edge drawing — maps directly to the state diagram requirement. |
| **Monaco Editor** (`@monaco-editor/react`, `monaco-editor`) | Code/instruction editor | The same editor engine that powers VS Code. Provides syntax highlighting, autocompletion, and inline error markers for the TM instruction language. Bundled with the app, so nothing is fetched from a CDN at run time. |
| **Tailwind CSS** | Styling | Utility-first approach for rapid UI development, consistent design tokens, and responsive layouts without writing custom CSS files. |
| **React Router** | Client-side routing | Standard routing solution for React SPAs. Handles navigation between the home page, the editor, sign-in, and the privacy policy. |
| **Yjs** (`yjs`, `y-websocket`, `y-indexeddb`, `y-protocols`) | Simultaneous editing | Added in sprint 3. Yjs is a CRDT: every person's edits merge without conflicts and without a central lock, which is what lets several people type in the same machine at once and keep working through a dropped connection. `y-websocket` connects a machine's shared document to its room on the backend, `y-indexeddb` keeps a copy in the browser so a room reopens at once, and `y-protocols` carries presence (who is here, and their cursor). |
| **html-to-image** | Diagram export (PNG/SVG) | Client-side rendering of the state diagram to raster/vector images for the export feature. Added in sprint 2; no server-side rendering involved. |
| **VS Code codicons** (`@vscode/codicons`) | Interface icons | Added in sprint 3 when the text buttons became icon buttons with tooltips. The same icon set as VS Code, so the debugger-style controls (run, step, step back) look familiar. Each SVG is imported on its own, so only the icons in use are bundled, and no icon font is loaded. Licensed CC-BY-4.0; the attribution is in the Frontend README. |

### State management

React's built-in `useState` and custom hooks are used, with no state library and no context layer. The diagram and the code view share one document object, so the bidirectional sync never needed an external store. **Zustand** was kept in mind as a lightweight option if that changed, and was not needed.

---

## 4. Backend (`Automata-Editor-and-Visualiser-Backend`)

| Technology | Role | Motivation |
|---|---|---|
| **Express** | HTTP API server | Minimal, well-understood framework. The API is hand-written (no auto-generated endpoints) and Express gives full control over route definitions and middleware. |
| **Mongoose** | ODM (Object Document Mapper) | Schema-based modelling for MongoDB with built-in validation, middleware, and TypeScript support. Enforces field types, required constraints, defaults, and indexes at the ODM layer on top of MongoDB's flexible document model; TypeScript integration ensures type safety from the database through to the API controllers. |
| **MongoDB Atlas** | Managed cloud database | Fully managed MongoDB cluster with automatic backups, scaling, and monitoring. The free tier (M0) provides a shared 512 MB cluster suitable for development and early deployment. Chosen over relational databases because machine definitions are hierarchical (states contain transitions, transitions reference symbols, positions are keyed by state id) — the document model stores a complete machine in a single record without joins, and the JSON-native storage aligns with the REST API's wire format so no ORM mapping layer is needed between the database and HTTP responses. |
| **Auth0** | Authentication platform | Fully managed identity service handling sign-up, sign-in, password reset, account deletion, and social login. Provides both a React SDK (`@auth0/auth0-react`) and Express middleware (`express-oauth2-jwt-bearer`) for token validation. Satisfies the requirement to use established, audited auth systems rather than writing a custom one. |
| **Zod** | Request validation | Runtime type validation for API request bodies, query parameters, and path parameters. Shares type definitions with the TypeScript codebase. |
| **ws** | WebSocket server | Added in sprint 3 for the collaboration rooms. A small, widely used WebSocket library that attaches to the same HTTP server as Express, so the API and the rooms share one port and one deployment. |
| **Yjs** (`yjs`, `y-protocols`, `lib0`) | Collaboration rooms | The server side of simultaneous editing: one shared document per machine, the messages that sync it, and their encoding. The room protocol is written on these directly, in the form `y-websocket` clients speak. |
| **jose** | Token check on a WebSocket | Verifies the Auth0 access token when a collaboration connection opens. The Express JWT middleware only works on HTTP requests, so the WebSocket needs its own check against the same tenant keys. |

---

## 5. Shared Core (`Automata-Editor-and-Visualiser-Core`)

| Technology | Role | Motivation |
|---|---|---|
| **TypeScript** | TM model, parser, simulator, test cases | Core simulation logic with full type safety. Published to the Gitea npm registry as `@brh/automata-core` and consumed by both the Frontend and Backend repos. |
| **Vitest** | Unit testing | Same test runner as the frontend for consistency. The simulation logic is pure and highly testable. |

### Distribution

The Core package is published to **Gitea's npm package registry** under the `@brh` scope as `@brh/automata-core`; both consumers install it as a normal dependency from that registry.

The published version is **4.0.0** (multi-tape machines since 3.0.0, nondeterministic machines since 3.1.0, stationary moves since 4.0.0), and the Frontend and Backend both depend on `^4.0.0`. The package has zero runtime dependencies and follows Semantic Versioning — see [Core Package Architecture](../Technical%20Architecture/core-package.md).

---

## 6. Testing

| Tool | Scope | Where |
|---|---|---|
| **Vitest** | Unit tests, integration tests | All three repos |
| **React Testing Library** | Component tests | Frontend |
| **Playwright** | End-to-end / UI tests | Frontend (drives a production build of the app in Chromium) |
| **Supertest** | API integration tests | Backend |
| **Artillery** | Load tests | Backend (run on demand against the deployed API) |

Testing coverage expectations grow across milestones: sprint 2 requires at least UI *or* API testing; sprint 3 expects both with useful, extensive test suites. Sprint 2 delivered both — unit suites and Playwright e2e specs in the Frontend, and controller/model/schema tests in the Backend (see [Testing Strategy](../Development%20Guide/testing-strategy.md)).

---

## 7. DevOps & Deployment

| Technology | Role | Motivation |
|---|---|---|
| **Gitea** | Version control, issue tracking, project board | Project-mandated platform. All four repos and issue tracking live here. |
| **Gitea Actions** | CI pipelines | Native to Gitea. Runs lint, type check, and tests on every pull request. |
| **Azure App Service** | Hosting — frontend and backend | A persistent Node.js web app for each: the Express backend, and the built React frontend behind a small Express file server. One platform and one deploy action for both. |
| **GitHub Pages** | Documentation site hosting | The MkDocs site is built and deployed via a GitHub Actions workflow on a GitHub mirror of the documentation repository. Gitea push mirroring syncs commits from Gitea to GitHub, where GitHub Actions builds the MkDocs output and deploys it to GitHub Pages. Free hosting with no visitor account required. |

### How each target is hosted

| Target | Platform | Notes |
|---|---|---|
| **Frontend** (`Automata-Editor-and-Visualiser-Frontend`) | **Azure App Service** | The Vite build is deployed with a small Express server that serves the files and sends `index.html` for any address the app routes itself. Deployed by a Gitea Actions workflow on every push to `main`. |
| **Backend** (`Automata-Editor-and-Visualiser-Backend`) | **Azure App Service** | Runs as a persistent Node.js web app (not serverless), which the collaboration WebSocket needs: a room lives in the server's memory while people are connected. Deployed by a Gitea Actions workflow on every push to `main`. |
| **Docs site** (`Automata-Editor-and-Visualiser-Documentation`) | **GitHub Pages** (via Gitea push mirror + GitHub Actions) | Gitea push-mirrors the repo to GitHub. A GitHub Actions workflow (`.github/workflows/github-pages-deploy.yml`) builds the MkDocs site and deploys the output to GitHub Pages. |

The frontend and backend are deployed on **Azure**, while the docs site is deployed to **GitHub Pages** — all three are deployed and scaled independently, satisfying the non-monolithic architecture requirement.

---

## 8. Documentation Site

| Technology | Role | Motivation |
|---|---|---|
| **MkDocs** (with **Material for MkDocs** theme) | Static documentation generator | Markdown-driven, zero-JS by default, fast builds, and a rich ecosystem of plugins. The Material theme provides a polished, responsive UI with built-in search, dark mode, and code highlighting out of the box. Deployed to GitHub Pages via Gitea push mirroring and a GitHub Actions workflow. |

Alternatives considered: **Docusaurus** (React-based, heavier for a docs-only site), **mdBook** (minimal but Rust-ecosystem). MkDocs chosen for simplicity and fast iteration on documentation content.

---

## 9. External API Integration

The project's own API is available to outside callers: a script or marking tool can check a machine against test cases with an API key (see [Using the API](../API%20Documentation/using-the-api.md)).

The external API the project consumes is **Auth0** (see [Authentication & Security](#10-authentication-security)): a fully managed identity service whose hosted Universal Login, token issuance, and claims the application consumes. Sprint 2's sharing feature builds its user directory purely from the claims on each signed-in user's token (a post-login Action adds `https://automata-ev/`-namespaced claims) — no Auth0 Management API is called, and no identity data is stored beyond email and name.

Diagram export and test-case runs are handled entirely client-side by the Core package and `html-to-image`; no external rendering or analysis service is used.

---

## 10. Authentication & Security

Users must be able to **sign up, sign in, reset passwords, and delete accounts**. This is implemented using:

Authentication is delegated entirely to **Auth0**, which manages the full identity lifecycle:

- **Sign-up, sign-in, password reset, account deletion** — all handled by Auth0's hosted Universal Login page
- **Frontend** — `@auth0/auth0-react` SDK provides login/logout, token management, and user profile access
- **Backend** — `express-oauth2-jwt-bearer` middleware validates Auth0-issued JWTs on every protected API route, and `jose` validates the same token on a collaboration WebSocket
- **Sign-in methods** — Google sign-in, or an email address and password

No custom authentication logic is written. Auth0 is a mature, audited identity platform that satisfies the requirement to rely on established auth systems.

---

## 11. Summary Table

| Layer | Technology |
|---|---|
| Language | TypeScript 6.0.3 |
| Runtime | Node.js |
| Frontend framework | React |
| Frontend bundler | Vite |
| Diagram editor | React Flow |
| Code editor | Monaco Editor |
| Styling | Tailwind CSS |
| Routing | React Router |
| Diagram export | html-to-image |
| Icons | VS Code codicons |
| Simultaneous editing | Yjs (`y-websocket`, `y-indexeddb`; `ws` on the server) |
| Backend framework | Express |
| ORM / ODM | Mongoose |
| Database | MongoDB Atlas (managed cloud) |
| Auth | Auth0 (Google sign-in, or email and password) |
| Validation | Zod |
| Shared core | TypeScript 6.0.3 (WASM migration path via C/Rust) |
| Shared core package | `@brh/automata-core` (published to the Gitea npm registry) |
| Testing (unit) | Vitest |
| Testing (component) | React Testing Library |
| Testing (e2e) | Playwright |
| Testing (API) | Supertest |
| Testing (load) | Artillery |
| Version control | Gitea |
| CI/CD | Gitea Actions |
| Hosting (frontend, backend) | Azure App Service |
| Hosting (docs) | GitHub Pages (via Gitea push mirror + GitHub Actions) |

---

**Related**: [Git Methodology](git-methodology.md) | [Project Methodology](project-methodology.md)

---

**AI Declaration:** The preceding document was generated and reviewed with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
