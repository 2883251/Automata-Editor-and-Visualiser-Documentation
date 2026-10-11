# CI/CD Pipeline

Continuous integration and deployment pipelines are built with Gitea Actions (all four repos) and GitHub Actions (to publish the documentation site).

---

## CI Pipeline (Gitea Actions)

All three TypeScript repositories run the same CI gate on every pull request. Workflows live in `.gitea/workflows/ci.yml`.

### Trigger

- Pull requests (push trigger is commented out — CI runs on PRs only)

### Runners

Jobs run on the team's own self-hosted Gitea runners. The Frontend, Backend and Documentation workflows ask for them by the label `brh-local`; the Core workflows use `ubuntu-latest`, which the same runners also answer to.

### Steps (in order)

| Step | Command |
|---|---|
| Checkout | `actions/checkout@v4` |
| Setup Node.js | `actions/setup-node@v4` — Node 24 with npm cache |
| Authenticate to registry | Write `.npmrc` with `NPM_TOKEN` secret (Backend and Frontend only — Core has no private deps) |
| Install dependencies | `npm ci` |
| Lint | `npm run lint` |
| Type-check | `npm run typecheck` |
| Run tests | `npm test -- --run` |
| Build | `npm run build` |

The Frontend CI adds two extra steps after unit tests, before the build:

- `npx playwright install --with-deps` — download browser binaries
- `npm run test:e2e` — run Playwright E2E tests

### Registry Authentication

Backend and Frontend CI require a `NPM_TOKEN` repository secret with `read:package` scope to install `@brh/automata-core` from the Gitea npm registry. The workflow validates the token is present before proceeding.

---

## Deployment Pipelines

### Backend → Azure App Service

**File**: `.gitea/workflows/deploy-azure.yml`

| Property | Value |
|---|---|
| Trigger | Push to `main` |
| Concurrency | `deploy-backend` group, no cancel-in-progress |
| Steps | CI gate (lint → typecheck → test → build) then `azure/webapps-deploy@v3` |
| App name | `Automata-EV-server` |
| Deployed artifact | A `deploy/` folder: `dist/` (compiled TypeScript), `package.json`, the lockfile, and the production dependencies installed into it |
| Secrets required | `AZURE_WEBAPP_PUBLISH_PROFILE`, `NPM_TOKEN` |
| Runtime settings | `MONGODB_URI`, `CORS_ORIGIN` and the Auth0 values are set on the App Service, not in the workflow |
| Address | `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net` |

### Frontend → Azure App Service

**File**: `.gitea/workflows/deploy-azure.yml`

| Property | Value |
|---|---|
| Trigger | Push to `main` |
| Concurrency | `deploy-frontend` group, no cancel-in-progress |
| Steps | Lint → typecheck → test → build, then `azure/webapps-deploy@v3`. The end-to-end tests run on the pull request, not again here |
| App name | `Automata-EV` |
| Deployed artifact | A `deploy/` folder: `dist/` (Vite production build) and a small Express server (`azure/server.js`) that serves it, sending `index.html` for any address the app routes itself |
| Secrets required | `AZURE_WEBAPP_PUBLISH_PROFILE`, `NPM_TOKEN` |
| Build settings | `VITE_API_URL` and the `VITE_AUTH0_*` values come from repository variables, since Vite builds them into the app |
| Address | `https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net` |

### Load Test (Backend)

**File**: `.gitea/workflows/load-test.yml`

| Property | Value |
|---|---|
| Trigger | Run by hand (`workflow_dispatch`), choosing all scenarios or one |
| Steps | Check the secrets are set → run the Artillery test in `load-tests/` against the deployed API → upload `report.json` as an artifact |
| Secrets required | `LOAD_TEST_BASE_URL`, `LOAD_TEST_API_KEY`, `LOAD_TEST_JWT_TOKEN` |

See [Testing Strategy](testing-strategy.md#load-tests-backend).

### Documentation CI

**File**: `.gitea/workflows/ci.yml` (Documentation repository)

| Property | Value |
|---|---|
| Trigger | Every push and every pull request |
| Steps | Checkout → setup Python 3.12 → `pip install -r requirements.txt` → `mkdocs build --strict` |

The strict build treats warnings as errors, so a broken internal link fails the check before it can reach the site.

### Documentation → GitHub Pages

**File**: `.github/workflows/github-pages-deploy.yml`

| Property | Value |
|---|---|
| Trigger | Push to `main` on the GitHub mirror (via Gitea push mirror) |
| Steps | Checkout → setup Python 3.12 → `pip install -r requirements.txt` → `mkdocs build --strict` → upload Pages artifact → deploy to GitHub Pages |
| Permissions | `contents: read`, `pages: write`, `id-token: write` |
| Concurrency | `pages` group, no cancel-in-progress |

The workflow lives in the Gitea repo and syncs to GitHub via push mirroring. GitHub Actions picks it up from the mirrored copy.

---

## Core Package Publish

**File**: `.gitea/workflows/publish.yml`

| Property | Value |
|---|---|
| Trigger | Push of a `v*` tag (e.g. `v2.0.0`) |
| Steps | Check tag matches `package.json` version → CI gate (lint → typecheck → test → build) → authenticate registry → `npm publish` |
| Registry | `https://sdp.ms.wits.ac.za/api/packages/brh/npm/` |
| Secret required | `NPM_TOKEN` with `write:package` scope |

The tag check runs before `npm install` so a mistyped tag fails in seconds rather than after the whole gate. The same CI gate as `ci.yml` runs — a tag must not publish something that would have failed a pull request.

---

## Summary

| Repo | CI | Deploy |
|---|---|---|
| Core | PR → lint, typecheck, test, build | Tag push → verify + `npm publish` |
| Backend | PR → lint, typecheck, test, build | Push `main` → Azure App Service. Load test by hand |
| Frontend | PR → lint, typecheck, test, e2e, build | Push `main` → Azure App Service |
| Documentation | Push and PR → `mkdocs build --strict` | Push `main` → GitHub Pages (via push mirror) |

---

**Related**: [Testing Strategy](testing-strategy.md) | [Coding Standards](coding-standards.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
