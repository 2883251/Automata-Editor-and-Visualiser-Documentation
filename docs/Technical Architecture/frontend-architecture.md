# Frontend Architecture

The frontend is a React 19 single-page application built with Vite 8. It provides a three-pane resizable layout for editing Turing machines: a state diagram, an instruction editor, and a simulation view.

---

## Overview

- **Framework**: React 19 (`react@^19.2.8`)
- **Build tool**: Vite 8 (`vite@^8.2.1`)
- **Language**: TypeScript 6.0.3, ESM modules
- **Styling**: Tailwind CSS 4 (`tailwindcss@^4.3.3` via `@tailwindcss/vite` plugin)
- **Routing**: React Router 8 (`react-router@^8.3.0`)
- **Dev server**: Vite dev server with HMR on `http://localhost:5173`

---

## Routes

Defined in `src/App.tsx` using React Router's `<Routes>`:

| Path | Component | Description |
|---|---|---|
| `/` | `EditorRoute` | Main editor — three-pane layout with diagram, instruction editor, simulation |
| `/machines` | `MachineListRoute` | Saved machines list — create, open, rename, delete (machine CRUD, F2-F5) |
| `/sign-in` | `SignInRoute` | Auth0 sign-in page |

---

## Three-Pane Resizable Layout

The `EditorRoute` renders three horizontal panes separated by draggable dividers:

1. **Diagram Pane** (`DiagramPane`) — state diagram editor using React Flow (`@xyflow/react`)
2. **Instruction Editor Pane** (`InstructionEditorPane`) — code editor using Monaco Editor
3. **Simulation Pane** (`SimulationPane`) — tape visualiser and simulation controls

Key implementation details:

- Panes are resizable via `useResizablePanels` hook with pointer drag and keyboard support
- Default sizes: `[34%, 33%, 33%]`, persisted to `localStorage` under key `automata-editor:main-pane-sizes`
- Responsive: panes stack vertically on mobile (`<768px`), horizontal on desktop
- An `OutputStrip` component sits below the panes
- A `NavBar` sits above with navigation links and auth controls

### Layout Components (`src/components/layout/`)

| Component | Purpose |
|---|---|
| `NavBar` | Top navigation bar with logo, document actions (New/Open/Save/Export), Machines link, auth control, mobile hamburger menu |
| `Pane` | Labelled section with title header and scrollable content area |
| `PaneDivider` | Draggable divider between panes with pointer and keyboard resize handlers |
| `OutputStrip` | Bottom strip for simulation status and test-case suite results; collapsible |
| `UnavailableButton` | Placeholder button for features not yet implemented |

### Machine Components (`src/components/machine/`)

| Component | Purpose |
|---|---|
| `MachineList` | Saved-machine list UI: open, rename, and delete saved machines (backed by `machine-store`) |

---

## Feature Modules (`src/features/`)

| Directory | Library | Purpose |
|---|---|---|
| `diagram/` | React Flow (`@xyflow/react@^12.11.3`) | Interactive state diagram — drag nodes, draw edges, custom node rendering; `diagram-export.ts` renders the diagram to PNG/SVG for export (H1) |
| `code-editor/` | Monaco Editor (`@monaco-editor/react@^4.7.0`) | Instruction language editing with syntax highlighting and error markers |
| `tape/` | — | TM simulation: `SimulationPane`, `TapeView` (tape rendering that follows the head), and the `useSimulation` hook driving Core's `step()` on a timer loop (G2-G5) |
| `export/` | `html-to-image` | Export dialog (H1-H2): diagram as PNG/SVG image, instruction table as CSV/HTML download or clipboard copy |
| `test-cases/` | — | Test-case authoring and suite runs (I1-I3): `TestCasePanel`, `useSuiteRun`, and complexity plots (K2): `ComplexityChart`, `ComplexityDialog`, `complexity-model` |

---

## Cross-Cutting Modules

| Directory | Purpose |
|---|---|
| `lib/` | `machine-store` (localStorage-backed saved machines), `editor-draft`, `alphabet` helpers, `auth-config`, `useMediaQuery` hook, `useResizablePanels` hook |
| `hooks/` | Shared React hooks (`useAuthUser`) |
| `types/` | Shared TypeScript type declarations |
| `test/` | Test setup and helpers (Vitest + RTL) |

---

## Auth0 Integration

- **Package**: `@auth0/auth0-react@^2.24.1`
- **Purpose**: Login/logout, token management, user profile access via Auth0 Universal Login
- The `Auth0Provider` wrapper, sign-in/sign-out routes, and session persistence are wired up; the backend validates the resulting JWTs on protected routes (see [Authentication & Security](../API%20Documentation/authentication.md)).

---

## Machine Persistence (current state)

Machine CRUD (create, save, open, rename, delete) persists machines **in the browser's `localStorage`** under the key `automata-editor:machines` (`src/lib/machine-store.ts`). A saved machine stores its instruction source text, diagram layout positions, and timestamps.

!!! note "In flight"
    Keeping machines in the signed-in user's account via the backend API ([F7]) is under review as PR #70 in the frontend repository; the backend endpoints it will call are documented in [REST Endpoints](../API%20Documentation/rest-endpoints.md).

---

## Path Aliases

The Vite config defines a `@` alias mapping to `./src`, enabling imports like:

```ts
import NavBar from '@/components/layout/NavBar'
```

---

## Dependencies

| Package | Version | Role |
|---|---|---|
| `react` | ^19.2.8 | UI framework |
| `react-dom` | ^19.2.8 | DOM renderer |
| `react-router` | ^8.3.0 | Client-side routing |
| `@xyflow/react` | ^12.11.3 | State diagram editor |
| `@monaco-editor/react` | ^4.7.0 | Code editor |
| `tailwindcss` | ^4.3.3 | Utility-first CSS |
| `@auth0/auth0-react` | ^2.24.1 | Authentication |
| `@brh/automata-core` | ^2.2.0 | Shared machine model, parser, simulator, test cases |
| `html-to-image` | ^1.11.13 | Diagram PNG/SVG rendering for export |

!!! note "Core version"
    The frontend consumes Core `^2.2.0`. Adopting Core 3.0.0 (multi-tape and multi-step machine variants) is under review as PR #72 in the frontend repository; see [TM Variants](../Features/tm-variants.md).

---

**Related**: [System Overview](system-overview.md) | [Backend Architecture](backend-architecture.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
