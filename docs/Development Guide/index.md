# Development Guide

This section is for developers working on the Automata Editor: how to get the project running on your own machine, and the standards, workflow, tests and pipelines the team follows.

## Sections

- **[Coding Standards](coding-standards.md)** — Code style, naming conventions, best practices
- **[Contribution Guidelines](contribution-guidelines.md)** — How to contribute, PR process, workflow
- **[Testing Strategy](testing-strategy.md)** — Unit tests, integration tests, E2E tests, test coverage
- **[CI/CD Pipeline](ci-cd-pipeline.md)** — Automated checks, deployment process, release management

---

## Getting Set Up

### Prerequisites

- **Node.js 24** and npm 11
- **Git**, and an account on the team's Gitea server, `https://sdp.ms.wits.ac.za`
- **MongoDB** for the backend: a local MongoDB 7 or an Atlas connection string. The backend starts without one, but saving and sharing need it
- **Python 3.12**, only to build this documentation site

### The Repositories

All four are in the `brh` organisation on Gitea.

| Repository | What it is | Runs locally at |
|---|---|---|
| `Automata-Editor-and-Visualiser-Frontend` | The React app | `http://localhost:5173` |
| `Automata-Editor-and-Visualiser-Backend` | The Express API and collaboration server | `http://localhost:4000` |
| `Automata-Editor-and-Visualiser-Core` | The shared `@brh/automata-core` package | A library, with nothing to run |
| `Automata-Editor-and-Visualiser-Documentation` | This site | `http://localhost:8000` |

To work on the app you need the Frontend and the Backend. The Core package is installed from the registry like any other dependency, so clone it only to change the machine model, parser or simulator.

### 1. A Registry Token

The Frontend and Backend install `@brh/automata-core` from the Gitea npm registry, which needs a token.

1. On Gitea, open **Settings → Applications** and generate a token with the **`read:package`** scope. A token with only repository scopes is refused by the registry.
2. In each of the Frontend and Backend repositories, copy `.npmrc.example` to `.npmrc`.
3. Make the token available as `NPM_TOKEN`, which `.npmrc` reads:

    ```bash
    export NPM_TOKEN=<your token>
    ```

`.npmrc` is git-ignored. Never commit a token.

### 2. The Backend

```bash
git clone https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Backend
cd Automata-Editor-and-Visualiser-Backend
cp .npmrc.example .npmrc
cp .env.example .env
npm install
npm run dev
```

Set the values in `.env` before `npm run dev`:

| Variable | Set it to |
|---|---|
| `MONGODB_URI` | Your MongoDB, for example `mongodb://localhost:27017/automata` |
| `CORS_ORIGIN` | `http://localhost:5173`, the frontend's address |
| `AUTH0_ISSUER_BASE_URL`, `AUTH0_AUDIENCE` | The team's Auth0 tenant and API identifier. Ask a team member |
| `API_KEY_HEADER_NAME` | Delete this line to use the default, `X-API-Key` |

Check it is up with `curl http://localhost:4000/health`. Without the Auth0 values the API still starts, and the routes that need a sign-in answer `503`. See [Backend Architecture](../Technical%20Architecture/backend-architecture.md#environment-variables).

### 3. The Frontend

```bash
git clone https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend
cd Automata-Editor-and-Visualiser-Frontend
cp .npmrc.example .npmrc
cp .env.example .env
npm install
npm run dev
```

Set the values in `.env` before `npm run dev`:

| Variable | Set it to |
|---|---|
| `VITE_API_URL` | `http://localhost:4000`, the backend's address |
| `VITE_AUTH0_DOMAIN`, `VITE_AUTH0_CLIENT_ID` | The team's Auth0 tenant and application. Ask a team member |
| `VITE_AUTH0_AUDIENCE` | The same API identifier as the backend's `AUTH0_AUDIENCE` |

The app opens at `http://localhost:5173`. With the Auth0 values left empty it still runs, permanently signed out, with machines kept in the browser. That is enough to work on the editors, the simulation and the test cases.

### 4. The Core Package (only to change it)

```bash
git clone https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core
cd Automata-Editor-and-Visualiser-Core
npm install
npm run test
```

It has no dependencies to authenticate for. A change reaches the applications when a new version is published; see [Contribution Guidelines](contribution-guidelines.md#core-package-releases).

### 5. This Site

```bash
git clone https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Documentation
cd Automata-Editor-and-Visualiser-Documentation
pip install -r requirements.txt
mkdocs serve
```

---

## Everyday Commands

The three TypeScript repositories share the same scripts:

| Command | Does |
|---|---|
| `npm run dev` | Start the dev server (Frontend and Backend) |
| `npm run lint` | ESLint |
| `npm run typecheck` | TypeScript, without emitting |
| `npm run test` | The Vitest suite, once |
| `npm run test:watch` | Vitest in watch mode |
| `npm run test:coverage` | The suite with a coverage report |
| `npm run build` | The production build |
| `npm run test:e2e` | Playwright end-to-end tests (Frontend only; run `npx playwright install chromium` once first) |

Before opening a pull request, run the same gate CI runs:

```bash
npm run typecheck && npm run lint && npm run test && npm run build
```

## Development Workflow

1. Create a working branch from the current release branch
2. Make changes following [Coding Standards](coding-standards.md)
3. Write tests for new features (see [Testing Strategy](testing-strategy.md))
4. Ensure the gate passes locally
5. Submit a pull request with a clear description
6. Address review feedback
7. Merge when approved

The full rules are in [Contribution Guidelines](contribution-guidelines.md).

## Useful Links

- **Repositories, issues and project board**: `https://sdp.ms.wits.ac.za/brh`
- **CI/CD**: Gitea Actions, on each repository's Actions tab
- **The deployed app**: `https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net`
- **The deployed API**: `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net`

---

**Related**: [Technical Architecture](../Technical%20Architecture/index.md) | [API Documentation](../API%20Documentation/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
