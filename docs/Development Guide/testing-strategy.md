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
- `e2e/` directory is excluded from Vitest — Playwright handles that

### Test File Convention

- Tests live beside the code they cover as `*.test.ts` (or `*.test.tsx` for React components)
- Test files are excluded from production builds (`dist/`)

### Running Tests

```bash
npm run test          # run once
npm run test:watch    # watch mode
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

- **Sprint 1**: Basic unit tests for core logic
- **Sprint 2**: UI *or* API testing required
- **Sprint 3**: Both UI and API testing — useful, extensive test suites

---

**Related**: [Coding Standards](coding-standards.md) | [CI/CD Pipeline](ci-cd-pipeline.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
