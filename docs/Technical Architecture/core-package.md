# Core Package Architecture

The shared core package `@brh/automata-core` (repository `Automata-Editor-and-Visualiser-Core`) contains everything that must behave identically in the browser and on the server: the machine data model, the instruction-language parser, validation and diagnostics, serialisation, the deterministic simulator, and the test-case model. It has **zero runtime dependencies**, is published to the Gitea npm registry under the `@brh` scope, and follows Semantic Versioning with a Keep a Changelog format.

---

## Module Map

```
src/
  types/           Branded domain types: StateId, Symbol, Direction, Transition
  machine/         Machine model: createMachine, lookupTransition, machinesEqual,
                   immutable update helpers, transitionKey
  parser/          Instruction language: lexer, AST, recursive-descent parser,
                   error recovery, trivia, astToMachine, grammar.md
  diagnostics/     diagnose() (parse diagnostics), validateMachine() (semantic checks)
  serialisation/   serialise() / deserialise() with a versioned JSON schema
  simulator/       Configuration, step(), simulate(), deriveOutput()
  test-cases/      TestCase model, runTestCase(), summariseResults(), cost measurement
  index.ts         Public API barrel — the single documented entry point
```

Consumers import from the package root (`@brh/automata-core`), never a deep path. A public-API surface test (`index.test.ts`) fails when an export is added or removed without updating the expected list, which keeps the barrel deliberate.

---

## Machine Model

- `createMachine(definition)` builds a **validated, deeply frozen** `Machine` from a `MachineDefinition`; invalid input raises `MachineValidationError`.
- Transition lookup is constant time: the inner transition map is keyed by `transitionKey(reads)` — a composite NUL-joined string of the per-tape read symbols — so a multi-tape lookup stays a single `Map` access at any tape count.
- Immutable update helpers (`addState`, `removeState`, `addTransition`, `removeTransition`, `setStartState`, `addAcceptingState`/`removeAcceptingState`, `addRejectingState`/`removeRejectingState`) each return a new frozen machine.
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

---

## Instruction Language Parser

A hand-written pipeline: **lexer → AST → recursive-descent parser → `astToMachine`** conversion, with a `SourceMap` mapping each transition back to its source range.

- 13 token kinds; 8+ AST node types; error recovery with per-item synchronisation so one malformed rule does not poison the rest of the parse.
- Comments and whitespace are preserved as trivia associated with nodes.
- `parse` errors are converted into user-facing `Diagnostic[]` by `diagnose()` (B3), enriched with the offending token and a source range for inline rendering.

### Grammar highlights

Sections (order-free, each keyword at most once): `states`, `start`, `accept`, `reject`, `tape_alphabet`, `input_alphabet`, `tapes`, `transitions`. A transition rule reads one symbol per tape on the left and applies semicolon-separated write-and-move actions on the right:

```text
tapes: 2

transitions:
  (q0, 'a', '_') -> (q1, 'b', R; '_', R)
  (q0, 'a')      -> (q1, 'a', R, 4)   # optional multi-step distance per tape
```

- `tapes: N` (default `1`) declares the tape count; programs written in the single-tape syntax parse unchanged.
- A per-tape action may carry an optional **distance** after its direction (`(q0, 'a') -> (q1, 'a', R, 4)` moves that tape's head 4 cells in one step). Omitted means one cell, exactly as before.
- Full EBNF: `src/parser/grammar.md` in the Core repository.

### Semantic validation (B4)

`validateMachine()` checks constructed machines for undeclared states in transitions, symbols outside the tape alphabet, input alphabet not a subset of the tape alphabet, accepting states unreachable (BFS from the start state), and nondeterminism on a deterministic machine — returning `Diagnostic[]`.

---

## Simulator

- `initConfiguration(machine, input)` seeds a `Configuration`: current state, one sparse tape (`Map<number, Symbol>`) and head position per tape (input written to tape 0, others blank), and step count.
- `step(machine, configuration)` applies exactly one transition: matches the full per-tape read tuple, then writes and moves each head independently, honouring per-tape distances (`effectiveDistance(transition, tapeIndex)` resolves the default of 1).
- `simulate(machine, input, limit?)` runs to completion with a configurable step limit (default `10 000`, exported as `DEFAULT_STEP_LIMIT`) and returns `exceeded-limit` instead of looping forever.
- `HaltReason` covers `accepted`, `rejected`, and `exceeded-limit`; `StepResult` is a discriminated union (`accepted | rejected | stepped`) for consumers that drive the simulator one step at a time.
- `deriveOutput(configuration)` (3.0.0) reads a computational machine's **output** from a halted configuration's first tape — its non-blank extent with surrounding blanks trimmed — so a live run can show the output distinctly from the raw tape.

---

## Serialisation (B5)

`serialise()` / `deserialise()` round-trip a machine and its diagram layout positions through a versioned JSON schema.

- **Schema version 2** (3.0.0): documents carry `tapeCount` and per-tape transition arrays.
- Version-1 documents are still read and migrated in memory to one-tape machines.
- Future schema versions are rejected with a clear upgrade message; `DeserialiseError` provides structured error handling for malformed JSON, missing schema, or invalid definitions.
- Public types: `SerialisedDocument`, `LayoutPoint`, `DeserialiseResult`.

---

## Test Cases and Cost Measurement (K1, 2.2.0)

- `TestCase` pairs an input string with an `Expectation`: `accepts`, `rejects`, or a specific `final-tape` value. It is a structural type — a stored `{ id, name, input, expectation }` passes straight into `runTestCase()`.
- `runTestCase()` runs one case, returning a `TestCaseResult` with the outcome, a pass/fail verdict, the halting configuration, and the run's cost.
- `MeasuredCost` records `steps` (transitions applied), `cells` (distinct tape cells the head occupied — summed across all tapes since 3.0.0), and `inputLength`, so time and space can be plotted against input size.
- `ExcludedCost` — a run that hit its step budget carries **no** step or cell count at all (only `inputLength`, `reason`, `limit`), so a budget can never be mistaken for a measurement. `TestCaseMeasurement` is the union of the two.
- `TapeComparison` matches `final-tape` expectations across the tape's non-blank extent and reports the first differing cell as both a string index and a tape position.
- `summariseResults()` folds completed results into passed/failed/excluded counts plus measured costs. It runs nothing itself: a suite is driven one `runTestCase()` call at a time, so a long suite never blocks the thread it is on.
- `TestCaseOutcome` refines the simulator's `HaltReason` by separating `stuck` (no transition applied) from `rejected` (an explicit rejecting state).

---

## Version History (sprint 2 releases)

| Version | Date | Content |
|---|---|---|
| `2.1.0` | 2026-09-09 | Sprint-1 wrap-up release: parse diagnostics (B3), machine validation (B4), serialisation (B5), deterministic simulator (G1). All additive. |
| `2.2.0` | 2026-09-11 | Test case model and cost measurement (K1) — all additive, no signature changes. |
| `3.0.0` | 2026-09-12 | Multi-tape machines (L3) with breaking type changes; multi-step distances in the instruction language (L5); `deriveOutput()` tape output reading; K1 space measurement fixed to aggregate across tapes. |

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
