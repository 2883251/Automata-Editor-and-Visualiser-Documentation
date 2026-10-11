# Testing Strategy

Testing frameworks, the suites that exist, and how to run them, for each repository.

---

## Frameworks by Repo

| Repo | Unit/Component | Integration | E2E |
|---|---|---|---|
| **Core** | Vitest | — | — |
| **Backend** | Vitest | Supertest (HTTP), real `ws` clients (WebSocket) | — |
| **Frontend** | Vitest + React Testing Library | — | Playwright |

The Backend also has an Artillery **load test**; see [Load Tests](#load-tests-backend).

---

## The Test Suites

As of the end of sprint 4 (counted on 11 October 2026):

| Repo | Test files | Tests | What they cover |
|---|---|---|---|
| **Core** | 23 | 501 | The machine model, parser, diagnostics, serialisation, simulator, computation tree, and test-case modules |
| **Backend** | 17 | 255 | Controllers, models, request schemas, middleware, and the collaboration server and rooms |
| **Frontend** (unit and component) | 77 | 893 | Stores, routes, hooks, and components |
| **Frontend** (end to end) | 18 specs | 110 | The app in a real browser |

- **Core**: unit tests beside the source. A public-API surface test (`index.test.ts`) fails whenever an export is added to or removed from the barrel without updating the expected list, and checks that the `VERSION` export matches `package.json`.
- **Frontend**: unit tests for the machine store, the routes, the simulation and computation-tree controllers, collaboration, and the layout, plus component tests with React Testing Library. Playwright specs cover the app shell and phone layout, the home page, saving and naming, the simulator, test cases, export, the diagram, code suggestions, colour mode, and each kind of machine: multi-tape, multi-step, stationary moves, computational, and nondeterministic with its computation tree.
- **Backend**: controller, model, and schema tests run against Vitest mocks — no MongoDB instance is required (`mongodb-memory-server` was removed for AVX-less CI compatibility). The collaboration tests start a real HTTP server on a free local port and connect real WebSocket clients to it, with the database and the token check replaced by fakes.

---

## Vitest (All Repos)

Vitest is the unit and integration test runner across all three TypeScript repositories. It shares Vite's module graph in the frontend and runs standalone in Core and Backend.

### Configuration

**Core** (`vitest.config.ts`):
```ts
export default defineConfig({
  test: {
    environment: 'node',
    include: ['src/**/*.test.ts'],
  },
})
```

**Backend** (`vitest.config.ts`):
```ts
export default defineConfig({
  test: {
    environment: 'node',
    include: ['src/**/*.test.ts'],
  },
})
```

**Frontend** (`vite.config.ts`):
```ts
test: {
  globals: true,
  environment: 'jsdom',
  setupFiles: ['./src/test/setup.ts'],
  exclude: ['**/node_modules/**', '**/dist/**', '**/e2e/**'],
}
```

The three configs also set up coverage (see [Measuring Coverage](#measuring-coverage)), and the Frontend sets `testTimeout: 15_000`.

- Frontend uses `jsdom` environment and enables Vitest globals
- `src/test/setup.ts` gives jsdom a `matchMedia` in which width queries match, so components render their **desktop** layout, the one most tests are about. A test of the phone layout replaces `window.matchMedia` with one that matches nothing, and puts the original back afterwards (see `EditorRoute.layout.test.tsx`)
- jsdom lays nothing out, so the setup file also supplies a no-op `ResizeObserver` and `Element.scrollTo`
- `e2e/` directory is excluded from Vitest — Playwright handles that

### Test File Convention

- Tests live beside the code they cover as `*.test.ts` (or `*.test.tsx` for React components)
- Test files are excluded from production builds (`dist/`)
- **No test depends on timing.** The CI runners are many times slower than a laptop, and their speed varies, so a test that waits a fixed time or expects something within a number of milliseconds fails at random. Tests wait for a condition, or use Vitest's fake timers

### Running Tests

```bash
npm run test          # run once
npm run test:watch    # watch mode
npm run test:coverage # run once and report coverage
```

---

## React Testing Library (Frontend)

- **Package**: `@testing-library/react@^16.3.2`
- **Additional**: `@testing-library/user-event@^14.6.5`, `@testing-library/jest-dom@^7.0.1`
- **Setup file**: `src/test/setup.ts`
- Used for component tests — rendering React components, simulating user interactions, asserting DOM output

---

## Playwright (Frontend E2E)

- **Package**: `@playwright/test@^1.62.1`
- **Config**: `playwright.config.ts`
- **Spec location**: `e2e/` directory
- **Browser**: Chromium (install with `npx playwright install chromium`)

Playwright builds the app and serves it automatically (`npm run build && npm run preview`, on port 4173) — no dev server needs to be running. In CI the build under test has no backend and no sign-in configured, so the specs exercise the signed-out app: everything except sharing, collaboration and API keys, which are covered by unit and component tests on both sides.

```bash
npm run test:e2e
```

---

## Supertest (Backend)

- **Package**: `supertest@^7.2.2`
- **Usage**: Drives the Express app through `createApp()` without binding a port or connecting to a database

```ts
import request from 'supertest'
import { createApp } from './app.js'

const response = await request(createApp()).get('/health')
```

This pattern means tests run without MongoDB or network dependencies.

---

## Load Tests (Backend)

The Backend repository has an [Artillery](https://www.artillery.io/) load test in `load-tests/`. It is run on demand against the deployed API by the **Load Test** workflow (see [CI/CD Pipeline](ci-cd-pipeline.md#load-test-backend)) and is not part of the pull-request gate. `load-tests/README.md` in the Backend repository has the setup, and the results of a run are on the [Performance](../performance.md#api-performance) page.

---

## CI Integration

The unit, component and end-to-end tests run as part of the Gitea Actions CI pipeline on every pull request. See [CI/CD Pipeline](ci-cd-pipeline.md).

---

## Coverage Expectations

- **Sprint 1** (delivered): Basic unit tests for core logic
- **Sprint 2** (delivered): UI *and* API testing — frontend unit suites and Playwright e2e specs, backend controller/model/schema tests
- **Sprint 3** (delivered): Both UI and API testing — the suites grew with the collaboration, nondeterminism, API key and marking work
- **Sprint 4** (delivered): tests for stationary moves, the in-app dialogs and help, and the backend load test

### Measuring Coverage

`npm run test:coverage` works the same in Core, Frontend and Backend. It runs the Vitest suite with `@vitest/coverage-v8`, prints a summary per file, and writes an HTML report to `coverage/index.html` that highlights the lines no test ran. `coverage/` is git-ignored. Only each package's own code is counted: tests, test setup and type declarations are left out, and so are the Frontend's `main.tsx` and the Backend's `index.ts`, which only start the app. The Playwright e2e specs are not measured.

No coverage threshold is enforced; the numbers are a guide. The last recorded measurement, on `dev/sprint-3` on 2026-09-26, before the sprint 4 work:

| Package | Statements | Branches | Functions | Lines |
|---|---|---|---|---|
| Core | 93.0% | 87.6% | 96.4% | 94.4% |
| Frontend | 86.1% | 77.9% | 84.7% | 90.4% |
| Backend | 90.1% | 81.6% | 93.4% | 91.1% |

---

**Related**: [Coding Standards](coding-standards.md) | [CI/CD Pipeline](ci-cd-pipeline.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
