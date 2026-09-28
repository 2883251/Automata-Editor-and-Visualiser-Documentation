# Frontend Architecture

The frontend is a React 19 single-page application built with Vite 8. Its editor is laid out by task: a state diagram and an instruction editor in the centre, the simulation along the bottom, the test cases and machine settings in a side bar, and a status bar.

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
| `/` | `HomeRoute` | Home page: your machines, machines saved in this browser, machines shared with you, and example machines, as cards with diagram thumbnails |
| `/editor` | `EditorRoute` | Editor for a machine that has not been saved yet (a scratch page) |
| `/m/:machineId` | `EditorRoute` | Editor for a saved machine. A stable address, so a reload or a shared link opens the same machine |
| `/sign-in` | `SignInRoute` | Auth0 sign-in page |

Old addresses still work: `/home` and `/machines` redirect to `/`, and `/?machineId=` or `/?example=` redirect to `/editor` with the same query.

---

## Editor Layout

`EditorRoute` loads the machine and builds the panels; `EditorShell` (`src/features/layout/`) arranges them. See [UI Design](../ui-design.md) for how it looks and behaves.

| Area | Component | Place on a desktop |
|---|---|---|
| Diagram | `DiagramPane`, React Flow (`@xyflow/react`) | Centre |
| Instruction Editor | `InstructionEditorPane`, Monaco Editor | Centre, beside or under the diagram |
| Simulation | `SimulationPane`, or `ComputationTreePane` for a nondeterministic machine | Along the bottom, collapsible |
| Test cases | `TestCasePanel` | Side bar view |
| Machine | `MachineSettings` (`TapeCountPanel`, `NondeterminismPanel`, `AlphabetPanel`) | Side bar view |
| Status | `StatusBar` | Along the very bottom |

Key implementation details:

- Plain resizable areas, with no docking library. `Splitter` is the line between two areas: a focusable `role="separator"` that is dragged with the pointer or moved with the arrow keys.
- The layout is one object (`EditorLayout` in `editor-layout.ts`): the editor arrangement (`side-by-side`, `stacked`, `diagram`, `code`), the split between the editors, the open side bar view, the side bar width, whether the simulation is open, and its height, kept separately for the tape and for the computation tree. `useEditorLayout` keeps it in `localStorage` under `automata-editor:editor-layout:v1`. A stored value is read field by field, so a field that is missing or out of range takes its default without discarding the rest.
- `LayoutSwitch` sits at the top right of the editors in every arrangement: in the instruction editor's header when it is on the right or alone, in the diagram's header when stacked or alone. **Reset layout** sits beside it.
- Hidden areas stay mounted (the `hidden` attribute), so switching views never loses state such as a half-written test case or the Monaco undo history.
- Status bar and "open in the simulator" actions reveal their target first (`flushSync`), then focus it: the first error in the instruction editor (`InstructionEditorHandle.showFirstError`), or the simulator's input (`SimulationPaneHandle.loadTestCase`).
- Below 768px (`useMediaQuery`), `EditorShell` shows one view at a time with a tab bar instead, and `DesktopNotice` says once that the editor works best on a bigger screen.
- A `NavBar` sits above with the Home link, document actions, the machine's name, colour mode, and auth controls.

### Layout Components (`src/components/layout/`)

| Component | Purpose |
|---|---|
| `NavBar` | Top navigation bar in three columns: title, Home link and document actions (New, Save or its status, Revert, Share, Export) on the left; the machine's name in the middle; presence, colour mode and auth controls on the right. On a phone, a Menu button opens the same actions as a labelled list over the page |
| `MachineName` | The open machine's name. The owner clicks it to rename the machine in place |
| `UnavailableButton` | Placeholder button for features not yet implemented |

### Machine Components (`src/components/machine/`)

| Component | Purpose |
|---|---|
| `MachineCard` | A home page card: diagram thumbnail, name, and actions. Also `NewMachineCard` and the `CardGrid` layout |
| `NameMachineDialog` | Modal that asks for a machine's name before New, opening an example, a copy, or the first save |

### UI Components (`src/components/ui/` and `src/components/icons/`)

| Component | Purpose |
|---|---|
| `IconButton` | Icon-only button whose accessible name is its label, plus any reason it is unavailable |
| `Tooltip` | Hover and keyboard-focus tooltip, rendered in a portal so a scrolling panel cannot clip it |
| `Icon` | A VS Code codicon, imported one SVG at a time |

---

## Feature Modules (`src/features/`)

| Directory | Library | Purpose |
|---|---|---|
| `diagram/` | React Flow (`@xyflow/react@^12.11.3`) | Interactive state diagram — drag nodes, draw edges, custom node rendering; `diagram-export.ts` renders the diagram to PNG/SVG for export (H1); `MachineSettings` gathers the tape count, nondeterminism and alphabet controls for the side bar |
| `code-editor/` | Monaco Editor (`@monaco-editor/react@^4.7.0`) | Instruction language editing with syntax highlighting and error markers |
| `tape/` | — | TM simulation: `SimulationPane` (laid out for the wide panel along the bottom), `TapeView` (tape rendering that stays centred on the head, also when its panel is resized), and the `useSimulation` hook driving Core's `step()` on a timer loop (G2-G5) |
| `export/` | `html-to-image` | Export dialog (H1-H2): diagram as PNG/SVG image, instruction table as CSV/HTML download or clipboard copy |
| `test-cases/` | — | Test-case authoring and suite runs (I1-I3): `TestCasePanel`, `useSuiteRun`, and complexity plots (K2): `ComplexityChart`, `ComplexityDialog`, `complexity-model` |
| `layout/` | — | The editor's layout: `EditorShell`, `ActivityBar`, `LayoutSwitch`, `Splitter`, `StatusBar`, `DesktopNotice`, and the saved layout (`editor-layout`, `useEditorLayout`) |
| `examples/` | — | The three built-in example machines shown on the home page, with fixed layouts |
| `collab/` | Yjs, `y-websocket`, `y-indexeddb` | `useCollabDocument` backs the editor with a live collaboration room while a machine is shared for editing, and read-only for a viewer. Presence and the access-change handling live here too. See [WebSocket Events](../API%20Documentation/websocket-events.md) |

---

## Cross-Cutting Modules

| Directory | Purpose |
|---|---|
| `lib/` | `machine-store` (browser and account stores behind one interface), `editor-draft` (the unsaved draft), `new-machine`, `format-date` (South African dates, year first), `api-client`, `alphabet` helpers, `auth-config`, `useMediaQuery` hook |
| `hooks/` | Shared React hooks (`useAuthUser`) |
| `types/` | Shared TypeScript type declarations |
| `test/` | Test setup and helpers (Vitest + RTL) |

---

## Auth0 Integration

- **Package**: `@auth0/auth0-react@^2.24.1`
- **Purpose**: Login/logout, token management, user profile access via Auth0 Universal Login
- The `Auth0Provider` wrapper, sign-in/sign-out routes, and session persistence are wired up; the backend validates the resulting JWTs on protected routes (see [Authentication & Security](../API%20Documentation/authentication.md)).

---

## Machine Persistence

`src/lib/machine-store.ts` has two stores behind one `MachineStore` interface. `useMachineStore` picks the account store when the user is signed in, and the browser store otherwise.

- **Account store:** the backend API ([REST Endpoints](../API%20Documentation/rest-endpoints.md)).
- **Browser store:** `localStorage` under `automata-editor:machines`. The home page can move these machines into the account after sign-in.

A machine only its owner works on saves when Save is pressed. The editor compares the document with the last save to tell whether there are unsaved changes, and keeps an unsaved draft in `localStorage` (`src/lib/editor-draft.ts`) so a reload keeps it. A machine shared for editing saves through its collaboration room instead. See [Machine Management](../Features/machine-management.md#saving-and-reverting).

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
| `@brh/automata-core` | ^3.1.0 | Shared machine model, parser, simulator, test cases |
| `html-to-image` | ^1.11.13 | Diagram PNG/SVG rendering for export |
| `@vscode/codicons` | ^0.0.46-24 | Interface icons (CC-BY-4.0) |
| `yjs` | ^13.6.32 | Shared document for collaborative editing |
| `y-websocket` | ^3.1.0 | Collaboration room connection |
| `y-indexeddb` | ^9.0.12 | Caches a room's content in the browser |

---

**Related**: [System Overview](system-overview.md) | [Backend Architecture](backend-architecture.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
