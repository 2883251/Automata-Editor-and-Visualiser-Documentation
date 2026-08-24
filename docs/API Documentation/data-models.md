# Data Models

The data models for the Automata Editor are defined in `@brh/automata-core` and exported from its barrel entry point `src/index.ts`. Both the frontend and backend consume these types identically.

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

A single transition in a Turing machine's transition function.

| Field | Type | Description |
|---|---|---|
| `read` | `Symbol` | Symbol that must be under the head for this transition to apply |
| `write` | `Symbol` | Symbol to write in place of the read symbol |
| `direction` | `Direction` | Head movement direction (`'L'` or `'R'`) |
| `distance` | `number` *(optional)* | Cells to move; defaults to `1`. For multi-step machines |
| `to` | `StateId` | State to enter after the transition |

Helper: `effectiveDistance(transition)` — resolves the `distance` default, returning `1` when omitted.

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
| `transitions` | `ReadonlyMap<StateId, ReadonlyMap<Symbol, Transition>>` | Nested map for O(1) transition lookup by state then symbol |

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
| `createMachine(definition)` | Build a validated, deeply frozen `Machine` from a `MachineDefinition`. Throws `MachineValidationError` on invalid input. |
| `lookupTransition(machine, state, symbol)` | Constant-time transition lookup by state and symbol. Returns `TransitionResult`. |
| `machinesEqual(a, b)` | Deep structural equality comparison for machines. |
| `addState`, `removeState` | Immutable update helpers — each returns a new frozen `Machine` |
| `addTransition`, `removeTransition` | Immutable transition update helpers |
| `setStartState` | Immutable start state update |
| `addAcceptingState`, `removeAcceptingState` | Immutable accepting state update helpers |
| `addRejectingState`, `removeRejectingState` | Immutable rejecting state update helpers |

---

## API Payload Shape

When machine CRUD endpoints are implemented, the JSON payload will serialise `MachineDefinition` (arrays, not Sets/Maps) since it maps naturally to JSON and MongoDB documents. The backend will use `createMachine()` to validate before storage.

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Authentication & Security](authentication.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
