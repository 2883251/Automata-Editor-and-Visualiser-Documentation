# Core Package Architecture

The shared core package `@brh/automata-core` (repository `Automata-Editor-and-Visualiser-Core`) contains everything that must behave identically in the browser and on the server: the machine data model, the instruction-language parser, validation and diagnostics, serialisation, the simulator (a single run, and the computation tree of a nondeterministic machine), and the test-case model. The current release is **4.0.0**. It has **zero runtime dependencies**, is published to the Gitea npm registry under the `@brh` scope, and follows Semantic Versioning with a Keep a Changelog format.

---

## Module Map

```
src/
  types/           Branded domain types: StateId, Symbol, Direction, Transition
  machine/         Machine model: createMachine, lookupTransition, machinesEqual,
                   immutable update helpers, setNondeterministic, transitionKey
  parser/          Instruction language: lexer, AST, recursive-descent parser,
                   error recovery, trivia, astToMachine, grammar.md
  diagnostics/     diagnose() (parse diagnostics), validateMachine() (semantic checks)
  serialisation/   serialise() / deserialise() with a versioned JSON schema
  simulator/       Configuration, step(), simulate(), deriveOutput(), and
                   exploreComputation() / simulateNondeterministic() for the computation tree
  test-cases/      TestCase model, runTestCase(), summariseResults(), cost measurement
  index.ts         Public API barrel — the single documented entry point
```

Consumers import from the package root (`@brh/automata-core`), never a deep path. A public-API surface test (`index.test.ts`) fails when an export is added or removed without updating the expected list, which keeps the barrel deliberate.

---

## Machine Model

- `createMachine(definition)` builds a **validated, deeply frozen** `Machine` from a `MachineDefinition`; invalid input raises `MachineValidationError`.
- Transition lookup is constant time: the inner transition map is keyed by `transitionKey(reads)` — a composite NUL-joined string of the per-tape read symbols — so a multi-tape lookup stays a single `Map` access at any tape count.
- Immutable update helpers (`addState`, `removeState`, `addTransition`, `removeTransition`, `setStartState`, `setNondeterministic`, `addAcceptingState`/`removeAcceptingState`, `addRejectingState`/`removeRejectingState`) each return a new frozen machine.
- The blank symbol is always `_`. It must be in the tape alphabet and must not be in the input alphabet.
- The tape is infinite in both directions: heads start at cell 0 and may move to negative positions.
- `machinesEqual(a, b)` provides deep structural equality.

### Multi-tape support (3.0.0)

A machine declares how many tapes it operates on, and every transition carries **one read, write, direction, and optional distance entry per tape**:

| Field | Type | Description |
|---|---|---|
| `Machine.tapeCount` | `number` (≥ 1) | Number of tapes the machine operates on |
| `Transition.reads` | `readonly [Symbol, ...Symbol[]]` | One read symbol per tape, in tape order |
| `Transition.writes` | `readonly [Symbol, ...Symbol[]]` | One write symbol per tape |
| `Transition.directions` | `readonly [Direction, ...Direction[]]` | One head direction per tape |
| `Transition.distances?` | `readonly [number, ...number[]]` | Optional per-tape multi-step distance |

`createMachine` validates the tape count and every transition's arity and symbols. Each tape's head moves independently during simulation.

### Nondeterministic machines (3.1.0)

A machine marked `nondeterministic` may have more than one transition for the same state and read symbols.

| Field | Type | Description |
|---|---|---|
| `Machine.nondeterministic?` | `boolean` | Set on a nondeterministic machine, absent otherwise |
| `Machine.alternatives?` | `ReadonlyMap<StateId, ReadonlyMap<string, readonly [Transition, ...Transition[]]>>` | The extra branches, in the order they were declared |

- The first transition declared for a state and read stays in `transitions`, so code written for deterministic machines keeps working. Later ones go in `alternatives`.
- `lookupTransition()` returns every branch for a read.
- `setNondeterministic(machine, false)` is refused while any read still has more than one branch.
- On a deterministic machine a second transition for the same read is an error, and the message says to mark the machine nondeterministic.

### Stationary moves (4.0.0)

`Direction` is `'L' | 'R' | 'S'`. A head that stays (`S`) writes its symbol and does not move. Its `distances` entry, if given, must be `1`, and `effectiveDistance()` returns `0` for it.

---

## Instruction Language Parser

A hand-written pipeline: **lexer → AST → recursive-descent parser → `astToMachine`** conversion, with a `SourceMap` mapping each transition back to its source range.

- 15 token kinds; 10 AST node types; error recovery with per-item synchronisation so one malformed rule does not poison the rest of the parse.
- `lexer()` and `parse()` never throw: `parse()` always returns an AST, possibly partial, together with its errors.
- Comments and whitespace are preserved as trivia associated with nodes.
- `parse` errors are converted into user-facing `Diagnostic[]` by `diagnose()` (B3), enriched with the offending token and a source range for inline rendering.

### Grammar highlights

Sections (order-free): `states`, `start`, `accept`, `reject`, `tape_alphabet`, `input_alphabet`, `tapes`, `nondeterministic`, `transitions`. A transition rule reads one symbol per tape on the left and applies semicolon-separated write-and-move actions on the right:

```text
tapes: 2

transitions:
  (q0, 'a', '_') -> (q1, 'b', R; '_', R)
  (q0, 'a')      -> (q1, 'a', R, 4)   # optional multi-step distance per tape
```

- `tapes: N` (default `1`) declares the tape count; programs written in the single-tape syntax parse unchanged.
- `nondeterministic: true` (3.1.0, default `false`) allows more than one rule for the same state and read symbols.
- Directions are the capital letters `L`, `R` and `S`. A comment runs from `#` to the end of the line.
- A per-tape action may carry an optional **distance** after its direction (`(q0, 'a') -> (q1, 'a', R, 4)` moves that tape's head 4 cells in one step). Omitted means one cell, exactly as before.
- The direction `S` (4.0.0) keeps that tape's head on the same cell for one step. It takes no distance: `S, 3` is a parse error, and `createMachine` rejects a stay whose `distances` entry is not `1`. `effectiveDistance()` returns `0` for it.
- Full EBNF: `src/parser/grammar.md` in the Core repository.

### Semantic validation (B4)

`validateMachine()` checks constructed machines for undeclared states in transitions, symbols outside the tape alphabet, input alphabet not a subset of the tape alphabet, accepting states unreachable (BFS from the start state, a warning rather than an error), and more than one transition for a read on a machine not marked nondeterministic — returning `Diagnostic[]`.

---

## Simulator

- `initConfiguration(machine, input)` seeds a `Configuration`: current state, one sparse tape (`Map<number, Symbol>`) and head position per tape (input written to tape 0, others blank), and step count.
- `step(machine, configuration)` applies exactly one transition: matches the full per-tape read tuple, then writes and moves each head independently, honouring per-tape distances (`effectiveDistance(transition, tapeIndex)` resolves the default of 1, and 0 for a stay). On a nondeterministic machine it follows the first branch declared.
- `simulate(machine, input, limit?)` runs to completion with a configurable step limit (default `10 000`, exported as `DEFAULT_STEP_LIMIT`) and returns `exceeded-limit` instead of looping forever.
- `HaltReason` covers `accepted`, `rejected`, and `exceeded-limit`; `StepResult` is a discriminated union (`accepted | rejected | stepped`) for consumers that drive the simulator one step at a time. A machine with no transition to apply reports `rejected`; the test-case model and the editor separate that case out as *stuck*.
- `deriveOutput(machine, configuration)` (3.0.0) reads a computational machine's **output** from a halted configuration's first tape — its non-blank extent with surrounding blanks trimmed — so a live run can show the output distinctly from the raw tape.

### Computation tree (3.1.0)

A nondeterministic machine is explored as a tree of configurations, breadth first:

- `exploreComputation(machine, input, maxConfigurations?)` is a generator that yields each `ComputationNode` as it is discovered, so an interface can draw the tree while it grows. `simulateNondeterministic()` runs the same search to the end and returns the result.
- A `ComputationNode` has an `id`, its `parent`, the `transition` that led to it, its `configuration`, and a `BranchStatus`: `accepted`, `rejected`, `stuck` (no transition applies), or `open` (it can still step).
- The search stops at the **first accepting node**, so the path from the root to it is a shortest accepting path. If every branch ends without accepting, the result is `rejected`.
- A budget of `DEFAULT_CONFIGURATION_LIMIT` (`10 000`) nodes keeps a machine that never halts from running forever. When the next node would go over it, the result is `inconclusive`.

---

## Serialisation (B5)

`serialise()` / `deserialise()` round-trip a machine and its diagram layout positions through a versioned JSON schema.

- **Schema version 2** (3.0.0): documents carry `tapeCount` and per-tape transition arrays. A nondeterministic machine also carries `nondeterministic: true` and every branch. The version number did not change for stationary moves, but a document that contains a stay cannot be read by Core 3.x.
- Version-1 documents are still read and migrated in memory to one-tape machines.
- Future schema versions are rejected with a clear upgrade message; `DeserialiseError` provides structured error handling for malformed JSON, missing schema, or invalid definitions.
- Public types: `SerialisedDocument`, `LayoutPoint`, `DeserialiseResult`.

---

## Test Cases and Cost Measurement (K1, 2.2.0)

- `TestCase` pairs an input string with an `Expectation`: `accepts`, `rejects` (passes when the machine halts in a rejecting state or gets stuck), or a specific `final-tape` value. It is a structural type — a stored `{ id, name, input, expectation }` passes straight into `runTestCase()`.
- `runTestCase()` runs one case with the single-run simulator, returning a `TestCaseResult` with the outcome, a pass/fail verdict, the halting configuration, and the run's cost. There is no test-case runner for the computation tree.
- `MeasuredCost` records `steps` (transitions applied), `cells` (distinct tape cells the head occupied — summed across all tapes since 3.0.0), and `inputLength`, so time and space can be plotted against input size.
- `ExcludedCost` — a run that hit its step budget carries **no** step or cell count at all (only `inputLength`, `reason`, `limit`), so a budget can never be mistaken for a measurement. `TestCaseMeasurement` is the union of the two.
- `TapeComparison` matches `final-tape` expectations across the tape's non-blank extent and reports the first differing cell as both a string index and a tape position.
- `summariseResults()` folds completed results into passed/failed/excluded counts plus measured costs. It runs nothing itself: a suite is driven one `runTestCase()` call at a time, so a long suite never blocks the thread it is on.
- `TestCaseOutcome` refines the simulator's `HaltReason` by separating `stuck` (no transition applied) from `rejected` (an explicit rejecting state).

---

## Version History

| Version | Date | Content |
|---|---|---|
| `1.0.0` | 2026-08-24 | First published release: the machine model, transition lookup, equality, update helpers, and the branded types. |
| `2.0.0` | 2026-08-24 | Instruction-language parser (lexer, AST, error recovery, `astToMachine`). Breaking: `lookupTransition()` returns a list of transitions. |
| `2.1.0` | 2026-09-09 | Sprint-1 wrap-up release: parse diagnostics (B3), machine validation (B4), serialisation (B5), deterministic simulator (G1). All additive. |
| `2.2.0` | 2026-09-11 | Test case model and cost measurement (K1) — all additive, no signature changes. |
| `3.0.0` | 2026-09-12 | Multi-tape machines (L3) with breaking type changes; multi-step distances in the instruction language (L5); `deriveOutput()` tape output reading; K1 space measurement fixed to aggregate across tapes. |
| `3.1.0` | 2026-09-23 | Nondeterministic machines (O1): the `nondeterministic` flag and section, `alternatives`, `setNondeterministic()`, and the computation tree (`exploreComputation()`, `simulateNondeterministic()`). All additive. |
| `4.0.0` | 2026-10-07 | Stationary moves (L7): the direction `S`. Breaking because `Direction` gained a member, so code that handles only `L` and `R` must handle `S` too. |

### Migrating 2.x → 3.0.0

The language and the serialised schema extend additively, but the public types were reshaped for per-tape data:

- `Transition`: wrap each scalar `read`/`write`/`direction`/`distance` in a one-element array (`reads`/`writes`/`directions`/`distances`).
- `Machine.transitions` inner map is keyed by `transitionKey(reads)` rather than a single read symbol — build keys with `transitionKey()` or go through `lookupTransition`.
- `Configuration`: `tape`/`head` become the `tapes`/`heads` tuples (index `0` for a single-tape machine).
- `lookupTransition()` / `removeTransition()` take a `readonly Symbol[]` read tuple; `effectiveDistance()` takes the tape index as a second parameter.
- Parser AST: `TransitionRuleNode` carries `readSymbols[]` and `tapeActions[]` instead of scalar read/write/direction.

---

**Related**: [System Overview](system-overview.md) | [Data Models](../API%20Documentation/data-models.md) | [TM Variants](../Features/tm-variants.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
Nondeterministic machines, the computation tree and the 3.1.0 and 4.0.0 releases were documented with the assistance of: Claude Code [Claude Opus 5.5].
