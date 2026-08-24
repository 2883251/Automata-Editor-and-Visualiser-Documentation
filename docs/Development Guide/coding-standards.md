# Coding Standards

Standards and conventions enforced across all three application repositories.

---

## Language & Type Safety

- **TypeScript strict mode** — all repos use `"strict": true` in `tsconfig.json`
- **TypeScript version**: pinned to `~6.0.3` across all repos. Do not upgrade to `7.x` — `typescript-eslint` supports only `>=4.8.4 <6.1.0`
- **ESM modules** — `"type": "module"` in every `package.json`. Relative imports must carry a `.js` extension even in `.ts` source files:

  ```ts
  import { createApp } from './app.js'   // correct, even though file is app.ts
  ```

---

## Linting

All three repos use the same ESLint flat config pattern (`eslint.config.js`):

- **`@eslint/js`** — recommended JavaScript rules
- **`typescript-eslint`** — TypeScript-specific rules via `tseslint.configs.recommended`
- **ECMAScript version**: 2023
- **Globals**: `globals.node` (Core, Backend), browser globals added by Vite (Frontend)
- **Ignores**: `dist`, `coverage`

Frontend adds:
- `eslint-plugin-react-hooks` — React hooks rules
- `eslint-plugin-react-refresh` — Fast Refresh compatibility

Run with:

```bash
npm run lint
```

Silence means success — no output indicates no issues.

---

## Commit Messages

All commits follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types

| Type | Use for |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace — no behaviour change |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or correcting tests |
| `build` | Build system, bundler, or dependency changes |
| `ci` | CI/CD configuration and workflows |
| `chore` | Maintenance that does not affect source or tests |

### Breaking Changes

Append `!` after the type or scope, and add a `BREAKING CHANGE:` footer:

```
feat(core)!: change transition representation to a map

BREAKING CHANGE: Machine.transitions is now a Map rather than an array.
```

---

## PR Title Format

PR titles must follow Conventional Commits format — the title becomes the squashed commit message when the branch is merged.

---

## Module Structure

- **Core**: Consumers import from the package root (`@brh/automata-core`), never a deep path. Internal layout can change without breaking consumers.
- **Backend**: `createApp()` factory separated from `index.ts` entry point for testability.
- **Frontend**: `@` path alias maps to `./src` for cleaner imports.

---

## Naming Conventions

- **Files**: kebab-case (`create-machine.ts`, `app-shell.spec.ts`)
- **Components**: PascalCase (`NavBar.tsx`, `DiagramPane.tsx`)
- **Types/Interfaces**: PascalCase (`Machine`, `StateId`, `TransitionEntry`)
- **Functions**: camelCase (`createMachine`, `lookupTransition`, `effectiveDistance`)
- **Constants**: UPPER_SNAKE_CASE (`VERSION`)

---

**Related**: [Contribution Guidelines](contribution-guidelines.md) | [Testing Strategy](testing-strategy.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
