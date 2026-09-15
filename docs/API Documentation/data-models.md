# Data Models

The core domain models for the Automata Editor are defined in `@brh/automata-core` and exported from its barrel entry point `src/index.ts`. Both the frontend and backend consume these types identically. This page reflects **Core 3.0.0** (multi-tape machines); see the [Core Package Architecture](../Technical%20Architecture/core-package.md) for the full module map and 2.x → 3.0 migration notes.

---

## Domain Types

### `StateId`

Branded string type for state identifiers. Nominal typing prevents accidental assignment of plain strings.

```ts
export type StateId = string & { readonly [stateIdBrand]: 'StateId' }
```

- Runtime guard: `isStateId(value)` — returns `true` for non-empty strings

### `Symbol`

Branded string type for tape and input symbols (single character).

```ts
export type Symbol = string & { readonly [symbolBrand]: 'Symbol' }
```

- Runtime guard: `isSymbol(value)` — returns `true` for strings of exactly length 1

### `Direction`

Head movement direction after a transition.

```ts
export type Direction = 'L' | 'R'
```

- Runtime guard: `isDirection(value)` — returns `true` for `'L'` or `'R'`

### `Transition`

A single transition in a Turing machine's transition function. Since Core 3.0.0 every transition carries **one entry per tape** for reads, writes, and head movement:

| Field | Type | Description |
|---|---|---|
| `reads` | `readonly [Symbol, ...Symbol[]]` | Symbol that must be under each tape's head for this transition to apply, in tape order |
| `writes` | `readonly [Symbol, ...Symbol[]]` | Symbol to write on each tape in place of the read symbol |
| `directions` | `readonly [Direction, ...Direction[]]` | Head movement direction (`'L'` or `'R'`) per tape |
| `distances` | `readonly [number, ...number[]]` *(optional)* | Cells to move per tape; defaults to `1`. For multi-step machines |
| `to` | `StateId` | State to enter after the transition |

Helpers:

- `effectiveDistance(transition, tapeIndex)` — resolves the per-tape `distances` default, returning `1` when omitted.
- `transitionKey(reads)` — the composite key (NUL-joined read tuple) used by the inner transition map, keeping multi-tape lookup a single O(1) `Map` access.

Machines written for Core 2.x with scalar `read`/`write`/`direction` fields need each value wrapped in a one-element array.

---

## Machine Model

### `Machine`

A fully constructed, deeply frozen Turing machine. All collections are `Readonly*` variants.

| Field | Type | Description |
|---|---|---|
| `states` | `ReadonlySet<StateId>` | Every state declared in the machine |
| `startState` | `StateId` | The state the machine starts in |
| `acceptingStates` | `ReadonlySet<StateId>` | States that cause the machine to accept |
| `rejectingStates` | `ReadonlySet<StateId>` | States that cause the machine to reject |
| `tapeAlphabet` | `ReadonlySet<Symbol>` | All symbols that may appear on the tape (includes blank) |
| `inputAlphabet` | `ReadonlySet<Symbol>` | Symbols the machine accepts as input (subset of tape alphabet) |
| `transitions` | `ReadonlyMap<StateId, ReadonlyMap<string, Transition>>` | Nested map for O(1) lookup by state then composite read key |
| `tapeCount` | `number` | How many tapes the machine operates on (≥ 1; added in 3.0.0) |

The inner map of `transitions` is keyed by `transitionKey(reads)` — the composite per-tape read string — not by a single symbol.

### `MachineDefinition`

Mutable input type used to describe a machine before construction. Uses plain arrays instead of Sets/Maps.

| Field | Type | Description |
|---|---|---|
| `states` | `ReadonlyArray<StateId>` | Every state in the machine |
| `startState` | `StateId` | The state the machine starts in |
| `acceptingStates` | `ReadonlyArray<StateId>` | States that cause acceptance |
| `rejectingStates` | `ReadonlyArray<StateId>` | States that cause rejection |
| `tapeAlphabet` | `ReadonlyArray<Symbol>` | All tape symbols (must include blank) |
| `inputAlphabet` | `ReadonlyArray<Symbol>` | Input symbols |
| `transitions` | `ReadonlyArray<TransitionEntry>` | Flat list of transitions, each carrying its source state |

### `TransitionEntry`

A transition paired with its source state, used in `MachineDefinition.transitions`.

| Field | Type | Description |
|---|---|---|
| `from` | `StateId` | The state this transition originates from |
| `transition` | `Transition` | The transition itself (read, write, direction, target) |

---

## Machine Operations

| Function | Description |
|---|---|
| `createMachine(definition)` | Build a validated, deeply frozen `Machine` from a `MachineDefinition`. Throws `MachineValidationError` on invalid input. Validates the tape count and every transition's per-tape arity. |
| `lookupTransition(machine, state, reads)` | Constant-time transition lookup by state and per-tape read tuple. Returns `TransitionResult`. |
| `machinesEqual(a, b)` | Deep structural equality comparison for machines. |
| `addState`, `removeState` | Immutable update helpers — each returns a new frozen `Machine` |
| `addTransition`, `removeTransition` | Immutable transition update helpers |
| `setStartState` | Immutable start state update |
| `addAcceptingState`, `removeAcceptingState` | Immutable accepting state update helpers |
| `addRejectingState`, `removeRejectingState` | Immutable rejecting state update helpers |

---

## Simulator and Test-Case Types

Key runtime types (all exported from the package root):

| Type | Description |
|---|---|
| `Configuration` | The machine's complete situation at a point in time: current state, `tapes` (one sparse tape per tape), `heads` (one position per tape), and `steps`. `initConfiguration()` writes the input to tape 0 and starts every head at 0. |
| `StepResult` | Discriminated union (`accepted \| rejected \| stepped`) for driving the simulator one `step()` at a time. |
| `SimulateResult` | Outcome of `simulate()` with its `HaltReason` (`accepted \| rejected \| exceeded-limit`). |
| `DerivedOutput` | Output read back from a halted configuration's first tape by `deriveOutput()`: the trimmed string and the position it starts at. |
| `TestCase` | Input string plus an `Expectation` (`accepts`, `rejects`, or a `final-tape` value). Structural — carries no id. |
| `TestCaseResult` | One case's outcome, pass/fail verdict, halting configuration, and `TestCaseMeasurement`. |
| `MeasuredCost` | `steps`, `cells` (distinct head positions across all tapes), and `inputLength`. |
| `ExcludedCost` | A run that hit its step budget: no step/cell count, only `inputLength`, `reason`, and `limit`. |
| `TapeComparison` | First differing cell (string index + tape position) for a failed `final-tape` expectation. |

---

## API Payload Shape

The machine persistence API (sprint 2) does **not** transfer `MachineDefinition` objects. It stores the machine as the frontend holds it — the **instruction-language `source` text plus diagram layout `positions`** — and leaves semantic validation to the Core package in the client:

```json
{
  "name": "Binary counter",
  "source": "states: q0, q1\nstart: q0\n...",
  "positions": { "q0": { "x": 100, "y": 200 } }
}
```

See [REST Endpoints](rest-endpoints.md) for the full request and response shapes.

---

## Database Schema (MongoDB)

The backend persists machines and user records in **MongoDB** via Mongoose. The full collection schemas live in `src/models/machine.ts` and `src/models/user.ts` in the Backend repository.

### `machines` collection

| Field | Type | Notes |
|---|---|---|
| `owner` | string (Auth0 `sub`) | required, indexed |
| `name` | string | default `'Untitled machine'`, trimmed, max 200 chars |
| `source` | string | required — the instruction-language source text |
| `positions` | `Map<string, {x, y}>` | diagram layout positions for each state |
| `sharedWith` | string[] | Auth0 `sub`s of share recipients |
| `createdAt` / `updatedAt` | dates | Mongoose-managed timestamps |

Compound indexes: `{ owner: 1, updatedAt: -1 }` (owner's machines sorted newest-first) and `{ sharedWith: 1, updatedAt: -1 }` (shared-with-me queries).

### `users` collection

| Field | Type | Notes |
|---|---|---|
| `sub` | string | required, unique — Auth0 subject identifier |
| `email` | string (optional) | lowercased, trimmed, indexed |
| `name` | string (optional) | trimmed |
| `createdAt` / `updatedAt` | dates | Mongoose-managed timestamps |

The users collection holds **no credentials** — Auth0 owns identity. It only resolves a `sub` to the email and name the token last carried, so an owner can share a machine by email. A recipient must have used the app at least once before a machine can be shared with them.

### Schema validation

Mongoose schemas enforce field types, required constraints, and defaults at the ODM layer. Additional request-level validation is performed by **Zod** schemas (`src/schemas/`) at the API boundary before data reaches the models — malformed requests are rejected with a `VALIDATION_ERROR` response before any database write occurs.

---

## Database Deployment

| Aspect | Detail |
|---|---|
| **Engine** | MongoDB 7+ |
| **ODM** | Mongoose 9 (`mongoose@^9.9.3`) |
| **Production** | MongoDB Atlas (managed cloud) — M0 free tier, 512 MB shared cluster, automatic backups, scaling, and monitoring |
| **Local development** | Any MongoDB 7+ instance: `mongodb://localhost:27017/automata` |
| **Connection string** | Set via the `MONGODB_URI` environment variable |
| **Startup behaviour** | If `MONGODB_URI` is unset, the server logs a warning and starts without a database connection — the API boots, but machine persistence and sharing endpoints require the database. If `MONGODB_URI` is set but MongoDB is unreachable, Mongoose retries with exponential backoff before failing. |

The backend separates the Express application factory (`src/app.ts`) from the entry point (`src/index.ts`) so that tests can drive the app via Supertest without binding a port or connecting to a database.

---

## Database Technology Choices

### Why MongoDB

The document model maps naturally to the project's data:

- **Machine definitions are hierarchical** — a machine contains states, states reference transitions, transitions reference symbols. Storing a complete machine as a single document avoids the joins a relational schema would require.
- **The instruction-language source text is stored verbatim** — the backend does not parse or validate machine semantics; that is the Core package's job in the client. A flexible document store accommodates this opaque-text pattern without schema migrations when the instruction language evolves.
- **JSON-native storage** aligns with the REST API's wire format, so no ORM mapping layer is needed between the database and the HTTP responses.
- **Atlas managed cloud** provides automatic backups, scaling, and monitoring on the free tier, removing operational burden during development and early deployment.

### Why Mongoose

MongoDB is schemaless by default, but the application still needs structure:

- Mongoose schemas enforce field types, required constraints, defaults, and indexes at the ODM layer.
- Built-in middleware supports the `timestamps: true` pattern (automatic `createdAt`/`updatedAt`) and custom pre/post hooks.
- TypeScript integration ensures type safety from the database layer through to the API controllers.

### Alternatives considered

| Alternative | Why not chosen |
|---|---|
| **Relational databases** (PostgreSQL, MySQL) | Machine definitions are inherently hierarchical — states contain transitions, transitions reference symbols, positions are keyed by state id. Mapping this to relational tables would require either a large number of join tables or opaque JSON columns, negating the benefits of a relational model. |
| **File-based storage** | Multi-user sharing requires concurrent access, conflict resolution, and query-by-owner/recipient — concerns a file system cannot address without building a database from scratch. |
| **BaaS (Firebase, Supabase)** | Would lock the project to a specific vendor's data model and auth system. The backend's hand-written Express API gives full control over validation, error shapes, and the sharing semantics. |

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Authentication & Security](authentication.md) | [Backend Architecture](../Technical%20Architecture/backend-architecture.md) | [Core Package Architecture](../Technical%20Architecture/core-package.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
