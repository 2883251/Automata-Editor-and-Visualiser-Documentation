# CI/CD Pipeline

Continuous integration and deployment pipelines are built with Gitea Actions (for application repos) and GitHub Actions (for documentation).

---

## CI Pipeline (Gitea Actions)

All three TypeScript repositories run the same CI gate on every pull request. Workflows live in `.gitea/workflows/ci.yml`.

### Trigger

- Pull requests (push trigger is commented out — CI runs on PRs only)

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

The Frontend CI adds two extra steps after unit tests:

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
| Deployed artifact | `dist/` (compiled TypeScript) |
| Secret required | `AZURE_WEBAPP_PUBLISH_PROFILE` |

### Frontend → Azure App Service

**File**: `.gitea/workflows/deploy-azure.yml`

| Property | Value |
|---|---|
| Trigger | Push to `main` |
| Concurrency | `deploy-frontend` group, no cancel-in-progress |
| Steps | CI gate (lint → typecheck → test → build) then `azure/webapps-deploy@v3` |
| App name | `Automata-EV` |
| Deployed artifact | `dist/` (Vite production build) |
| Secret required | `AZURE_WEBAPP_PUBLISH_PROFILE` |

### Documentation → GitHub Pages

**File**: `.github/workflows/github-pages-deploy.yml`

| Property | Value |
|---|---|
| Trigger | Push to `main` on the GitHub mirror (via Gitea push mirror) |
| Steps | Checkout → setup Python 3.12 → `pip install -r requirements.txt` → `mkdocs build --strict` → upload Pages artifact → deploy to `gh-pages` |
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
| Backend | PR → lint, typecheck, test, build | Push `main` → Azure App Service |
| Frontend | PR → lint, typecheck, test, e2e, build | Push `main` → Azure App Service |
| Documentation | — | Push `main` → GitHub Pages (via push mirror) |

---

**Related**: [Testing Strategy](testing-strategy.md) | [Coding Standards](coding-standards.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
