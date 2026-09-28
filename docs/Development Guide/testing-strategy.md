# Testing Strategy

Testing frameworks and configuration for each repository.

---

## Frameworks by Repo

| Repo | Unit/Component | Integration | E2E |
|---|---|---|---|
| **Core** | Vitest | — | — |
| **Backend** | Vitest | Supertest | — |
| **Frontend** | Vitest + React Testing Library | — | Playwright |

---

## Shipped Test Suites (Sprint 2)

- **Core**: unit tests beside the source for the machine model, parser, simulator, serialisation, and test-case modules. A public-API surface test (`index.test.ts`) fails whenever an export is added to or removed from the barrel without updating the expected list.
- **Frontend**: unit tests for the machine store, the home page and editor routes, and the simulation controller (`use-simulation`), plus component tests with React Testing Library. Playwright e2e specs cover the simulator (live playback, budget pause, cancel-on-edit), export, test cases, and diagram edge labels.
- **Backend**: controller, model, and schema tests run against Vitest mocks — no MongoDB instance is required (`mongodb-memory-server` was removed for AVX-less CI compatibility).

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

- Frontend uses `jsdom` environment and enables Vitest globals
- `src/test/setup.ts` gives jsdom a `matchMedia` in which width queries match, so components render their **desktop** layout, the one most tests are about. A test of the phone layout replaces `window.matchMedia` with one that matches nothing, and puts the original back afterwards (see `EditorRoute.layout.test.tsx`)
- jsdom lays nothing out, so the setup file also supplies a no-op `ResizeObserver` and `Element.scrollTo`
- `e2e/` directory is excluded from Vitest — Playwright handles that

### Test File Convention

- Tests live beside the code they cover as `*.test.ts` (or `*.test.tsx` for React components)
- Test files are excluded from production builds (`dist/`)

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

Playwright builds the app and serves it automatically — no dev server needs to be running.

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

## CI Integration

All tests run as part of the Gitea Actions CI pipeline on every pull request. See [CI/CD Pipeline](ci-cd-pipeline.md).

---

## Coverage Expectations

- **Sprint 1** (delivered): Basic unit tests for core logic
- **Sprint 2** (delivered): UI *and* API testing — frontend unit suites and Playwright e2e specs, backend controller/model/schema tests
- **Sprint 3**: Both UI and API testing — useful, extensive test suites

### Measuring Coverage

`npm run test:coverage` works the same in Core, Frontend and Backend. It runs the Vitest suite with `@vitest/coverage-v8`, prints a summary per file, and writes an HTML report to `coverage/index.html` that highlights the lines no test ran. `coverage/` is git-ignored. Only each package's own code is counted: tests, test setup and type declarations are left out, and so are the Frontend's `main.tsx` and the Backend's `index.ts`, which only start the app. The Playwright e2e specs are not measured.

Coverage on `dev/sprint-3` on 2026-09-26:

| Package | Statements | Branches | Functions | Lines |
|---|---|---|---|---|
| Core | 93.0% | 87.6% | 96.4% | 94.4% |
| Frontend | 86.1% | 77.9% | 84.7% | 90.4% |
| Backend | 90.1% | 81.6% | 93.4% | 91.1% |

---

**Related**: [Coding Standards](coding-standards.md) | [CI/CD Pipeline](ci-cd-pipeline.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
