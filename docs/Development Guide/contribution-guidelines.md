# Contribution Guidelines

Process for contributing to any of the four Automata Editor repositories. All rules come from the project's Git Methodology document.

---

## Branching Strategy

- **Branch from the active release branch** (e.g. `dev/sprint-1`), never from `main`
- **Branch naming** follows the commit type vocabulary:

  ```
  <type>/<short-kebab-description>
  ```

  With a board item number where one exists:

  ```
  feat/42-short-description
  fix/tape-head-offset
  docs/api-reference
  chore/update-dependencies
  ```

  Accepted prefixes: `feat/`, `fix/`, `docs/`, `refactor/`, `test/`, `build/`, `ci/`, `chore/`, `style/`, `perf/`, `revert/`

---

## Development Workflow

1. **Checkout the release branch** and pull latest:

   ```bash
   git checkout dev/sprint-1
   git pull
   ```

2. **Create your working branch**:

   ```bash
   git checkout -b feat/42-short-description
   ```

3. **Make changes** with Conventional Commits (see [Coding Standards](coding-standards.md#commit-messages))

4. **Before opening a PR**, run the full CI gate locally:

   ```bash
   npm run typecheck && npm run lint && npm run test && npm run build
   ```

5. **Open a pull request** into the release branch

---

## Pull Request Requirements

- **One approving review** from another team member
- **CI must pass** — lint, typecheck, and tests all run on every PR via Gitea Actions
- **PR title** follows Conventional Commits format — it becomes the squashed commit message
- **PR description** states what changed and why, and links the board item where applicable

### Review Process

- Reviews are turned around within one working day where possible
- A reviewer either approves or requests changes with specific comments
- The author does not merge until the review is resolved

---

## Merge Strategy

| Merge direction | Strategy | Rationale |
|---|---|---|
| Working branch → release branch | **Squash** | Each PR becomes a single commit on the release branch |
| Release branch → `main` | **Merge commit** | Preserves the release as a distinct event |
| Hotfix → `main` | **Squash** | Single focused change |

Working branches are **deleted after merge**.

---

## Branch Protection

Configured on `main` and the active release branch:

- No direct pushes — changes only via pull request
- At least one approving review required
- Status checks (lint, typecheck, test) must pass before merge
- Force-push disabled
- Branch deletion disabled

---

## Hotfixes

A defect in the deployed application is fixed immediately:

1. Cut a `hotfix/<description>` branch from `main`
2. PR it into `main`
3. Merge `main` back into the active release branch so the fix is not lost at the next release

---

## Core Package Releases

The core package (`@brh/automata-core`) versions independently of the sprint cadence:

- `feat` commits → minor bump
- `fix`/`perf` commits → patch bump
- `!` marker or `BREAKING CHANGE:` footer → major bump

Pushing a `v*` tag triggers the publish workflow. See the Core repository's `README.md` for the full release procedure.

---

**Related**: [Coding Standards](coding-standards.md) | [Testing Strategy](testing-strategy.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
