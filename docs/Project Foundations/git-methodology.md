# Git Methodology

## 1. Purpose

This document defines how the team uses version control: how branches are created and merged, how commits and branches are named, how changes are reviewed, and how releases are deployed. It applies to all three repositories (`package`, `frontend`, `backend`).

## 2. Repositories

| Repository | Contents |
| --- | --- |
| `package` | Shared TypeScript (or later a WASM compilable language) core: machine model, parser, simulator |
| `frontend` | Client application |
| `backend` | API server |

The three are kept separate so that the core package can evolve independently of the applications that consume it, and so that the front-end and back-end are deployed as separate applications.

## 3. Branching Model

We use a three-level model: `main`, release branches (`dev/`), and short-lived working branches.

### `main`

`main` is the deployed state of the project. It is only ever updated by merging a release branch into it, or by a hotfix. Every commit on `main` corresponds to something that has been deployed.

### Release branches (`dev/`)

A release branch is created from `main` at the start of each release, and merged into `main` at the end of each release. For the duration of that release it acts as the team's integration branch: all working branches are cut from it, and all completed work is merged back into it.

Naming: `dev/<release-name>` for the current release.

### Working branches

Every unit of work gets its own branch off the current release branch. Working branches are short-lived and are deleted after merge.

Naming follows the commit type vocabulary in Section 4:

```
<type>/<short-kebab-description>
```

Examples:

```
feat/import-automata
fix/tape-head-offset
docs/api-reference
refactor/transition-lookup
```

Where a board item exists, prefix the description with its number:

```
feat/42-import-automata
```

### Hotfix branches

A defect in the deployed application is fixed immediately rather than waiting for the next release. Cut a `hotfix/<description>` branch from `main`, PR it into `main`, and then merge `main` back into the active release branch so the fix is not lost at the next release.

## 4. Commit Messages

We follow [Conventional Commits](https://www.conventionalcommits.org/).

```
<type>(<scope>): <description>

[optional body - why changes were made]

[optional footer]
```

### Types

| Type | Use for |
| --- | --- |
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace, NO behaviour change |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | A change that improves performance |
| `test` | Adding or correcting tests |
| `build` | Build system, bundler, or dependency changes |
| `ci` | CI/CD configuration and workflows |
| `chore` | Maintenance that does not affect source or tests |
| `revert` | Reverts a previous commit |

### Scopes

Optional, but preferred. Use the area of the codebase affected, for example
`editor`, `parser`, `simulator`, `api`, `auth`, `docs`.

### Breaking changes

Append `!` after the type or scope, and explain in the footer:

```
feat(package)!: change transition representation to a map

BREAKING CHANGE: Machine.transitions is now a Map rather than an array.
```

### Examples

```
feat(editor): add drag handling for state nodes
fix(simulator): correct head position after left move at tape start
docs(methodology): document hotfix branch procedure
test(parser): cover malformed instruction input
```

## 5. Pull Requests

All changes reach a release branch or `main` through a pull request. Direct pushes are blocked by branch protection.

### Requirements

- **One approving review** from another team member
- **CI must pass** (lint, type check, and tests)
- The PR title follows the Conventional Commits format, since it becomes the
  squashed commit message
- The description states what changed and why, and links the board item where applicable

### Review expectations

Reviews are turned around within one working day where possible. A reviewer either approves, or requests changes with specific comments. The author does not merge until the review is resolved.

### Merge strategy

| Merge | Strategy | Rationale |
| --- | --- | --- |
| working branch → release branch | **Squash** | Each PR becomes a single commit, so the release branch reads as one entry per unit of work |
| release branch → `main` | **Merge commit** | Preserves the release as a distinct event and keeps the branches from diverging |
| hotfix → `main` | **Squash** | Single focused change |

Working branches are deleted after merge.

## 6. Branch Protection

The following rules are configured on `main` and on the active release branch:

- No direct pushes, with changes instead only via pull request
- At least one approving review required
- Status checks (lint, type check, test) must pass before merge
- Force-push disabled
- Branch deletion disabled

## 7. Releases and Deployment

Merging the release branch into `main` is what deploys the application.

Responsibility for the release is assigned at the sprint meeting preceding it. The release procedure is:

1. Confirm CI is green on the release branch
2. Open a PR from the release branch into `main`
3. Obtain one approval
4. Merge with a merge commit
5. Tag `main` with the release version
6. Confirm the deployed applications are up and healthy
7. Cut the next release branch from `main`

## 8. Enforcement

Where possible the rules above are enforced mechanically:

- Branch protection blocks unreviewed and failing changes at the server
- CI runs lint, type check, and tests on every pull request
- Commit message format is checked automatically

## 9. Open Points

- Commit message linting tool TBD and configured
- Distribution mechanism for the shared `package` across the two application
  repositories (registry vs. git reference) TBD
- Release version scheme TBD.
- Potential TM-core language TBD (C or C++ good options).

<br>

**AI Declaration:** The preceding document was reviewed and edited with the assisstance of: Claude Web [Claude Opus 5].