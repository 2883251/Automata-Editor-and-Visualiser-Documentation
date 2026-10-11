# Glossary

This glossary defines key terminology used throughout the Automata Editor documentation and application.

## Accept State

A state designated as an accepting state. When the computation reaches it, the machine halts and the input is accepted.

## Alphabet

The set of symbols that can appear on the tape or be accepted as input. Typically divided into:
- **Input Alphabet** (Σ): Symbols that can appear in the input string
- **Tape Alphabet** (Γ): All symbols that can appear on the tape, including the blank symbol

## API Key

A secret a signed-in user creates in the app so that a script or marking tool can call the marking endpoint without signing in. It is shown once, and the server keeps only a hash of it. See [Using the API](API%20Documentation/using-the-api.md).

## Blank Symbol

The symbol in every tape cell that has not been written. In the Automata Editor it is always `_`. It is part of the tape alphabet and never part of the input alphabet.

## Collaboration Room

The live session a machine opens in while it is shared for editing. Everyone in the room sees each other's changes and cursors, and the room saves on its own. See [Sharing](Features/sharing.md).

## Computation Tree

A visualization of all possible execution paths in a nondeterministic Turing Machine, showing branching at decision points where multiple transitions are possible. Each node is one configuration, and each branch ends accepted, rejected, or stuck. See [Nondeterministic Machines](Features/nondeterminism.md).

## Derived Output

The computational output of a machine that computes rather than merely accepts: read from the first tape of a halted configuration as its non-blank extent with surrounding blanks trimmed (Core 3.0.0's `deriveOutput()`).

## Deterministic Turing Machine (DTM)

A Turing Machine variant where each configuration has at most one possible next configuration. Given a state and the symbols under the heads, there is at most one transition.

## Halting

The condition when a Turing Machine stops: it reaches an accepting or rejecting state, or no transition is defined for the symbols under its heads (see *Stuck*).

## Machine Configuration

A snapshot of a Turing Machine's current state, including:
- Current state
- Tape contents
- Head position
- Execution step number

## Marking

Checking a machine against a list of test cases through the API, for use by a script or marking tool. See [Using the API](API%20Documentation/using-the-api.md).

## Multi-Step Turing Machine

A Turing Machine variant where a single transition can move a head several cells (an optional per-tape distance) in one transition application. Supported by the Core package's instruction language since 3.0.0.

## Multi-Tape Turing Machine

A Turing Machine variant with multiple independent tapes, each with its own read/write head. Equivalent in computational power to a single-tape machine, but often more convenient and fewer steps for practical computations. Supported by the Core package since 3.0.0.

## Nondeterministic Turing Machine (NTM)

A Turing Machine variant where a configuration can have multiple possible next configurations. The machine accepts if at least one computation path leads to acceptance. Supported by the Core package since 3.1.0.

## Reject State

A state designated as a rejecting state. When the computation halts in this state, the input is considered rejected.

## Shared Machine

A machine that its owner has shared with another signed-in user, by email address, as a **viewer** or an **editor**. A viewer can open and run it and make their own copy. An editor can also change it, at the same time as others. Only the owner can rename, delete, or re-share it.

## Start State

The initial state where the Turing Machine begins execution. Also called the initial state.

## State

A node in the state diagram representing a distinct configuration or control flow position in the machine.

## Stationary Move

A transition action that writes a symbol and leaves the head on the same cell, written with the direction `S`. It counts as one step and takes no distance. Supported by the Core package since 4.0.0.

## Step Budget

A cap on how many transitions may be applied before a run is stopped. In test cases the budget is terminal — a case that hits it is reported *inconclusive*; in the interactive simulator it acts as a checkpoint the user can continue past.

## Stuck

Halting because no transition is defined for the symbol(s) currently under the head(s). Distinct from *rejected*, which means the machine walked into an explicit rejecting state.

## Tape

The infinite (or arbitrarily large) data structure where the machine reads and writes symbols. The head maintains a position on the tape.

## Tape Head

The read/write mechanism that points to a specific position on the tape. The machine can move the head left or right, or leave it where it is, and read/write symbols.

## Test Case

An input string paired with an expectation — that the machine accepts, rejects, or leaves a specific output on the tape. Suites run one case at a time and report each case as passed, failed, inconclusive, or not checked.

## Transition

A directed edge in the state diagram representing a state change. Typically labeled with input symbol, output symbol, and head movement direction.

## Transition Rule

A formal specification of a machine transition, usually written as a tuple: (current_state, read_symbol) → (next_state, write_symbol, head_direction)

## Turing Machine (TM)

A mathematical model of computation consisting of states, transitions, and an infinite tape. Used to formalize the concept of an algorithm and computability.

## Visualization Layer

The frontend rendering system that displays the state diagram, tape, and execution visualization in the browser.

---

*For additional context on Turing Machines and formal language theory, consult academic references on Automata Theory and Computability.*

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
