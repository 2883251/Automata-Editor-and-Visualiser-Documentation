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
| `/machines` | `MachineListRoute` | Saved machines list (placeholder) |
| `/sign-in` | `SignInRoute` | Auth0 sign-in page (placeholder) |

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
| `OutputStrip` | Bottom strip for simulation output display |
| `UnavailableButton` | Placeholder button for features not yet implemented |

---

## Feature Modules (`src/features/`)

| Directory | Library | Purpose |
|---|---|---|
| `diagram/` | React Flow (`@xyflow/react@^12.11.3`) | Interactive state diagram — drag nodes, draw edges, custom node rendering |
| `code-editor/` | Monaco Editor (`@monaco-editor/react@^4.7.0`) | Instruction language editing with syntax highlighting and error markers |
| `tape/` | — | Tape visualiser and simulation controls |

---

## Cross-Cutting Modules

| Directory | Purpose |
|---|---|
| `lib/` | API client, `useMediaQuery` hook, `useResizablePanels` hook |
| `hooks/` | Shared React hooks |
| `types/` | Shared TypeScript type declarations |
| `test/` | Test setup and helpers (Vitest + RTL) |

---

## Auth0 Integration

- **Package**: `@auth0/auth0-react@^2.24.1`
- **Purpose**: Login/logout, token management, user profile access via Auth0 Universal Login

!!! info "Status: Planned"
    The `@auth0/auth0-react` package is installed but the `Auth0Provider` wrapper and login flows are not yet wired up. The sign-in route currently renders a placeholder.

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
| `@auth0/auth0-react` | ^2.24.1 | Authentication (planned) |
| `@brh/automata-core` | ^2.0.0 | Shared machine model |

---

**Related**: [System Overview](system-overview.md) | [Backend Architecture](backend-architecture.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
