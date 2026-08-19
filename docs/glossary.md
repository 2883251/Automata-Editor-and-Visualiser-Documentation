# Glossary

This glossary defines key terminology used throughout the Automata Editor documentation and application.

## Alphabet

The set of symbols that can appear on the tape or be accepted as input. Typically divided into:
- **Input Alphabet** (Σ): Symbols that can appear in the input string
- **Tape Alphabet** (Γ): All symbols that can appear on the tape, including the blank symbol

## Computation Tree

A visualization of all possible execution paths in a nondeterministic Turing Machine, showing branching at decision points where multiple transitions are possible.

## Deterministic Turing Machine (DTM)

A Turing Machine variant where each configuration has at most one possible next configuration. Given a state and input symbol, there is exactly one transition.

## Finite State

A state in the Turing Machine that has no outgoing transitions. Computation halts when reaching a finite (accepting or rejecting) state.

## Halting

The condition when a Turing Machine reaches a state with no defined transitions for the current input symbol, or completes execution intentionally.

## Machine Configuration

A snapshot of a Turing Machine's current state, including:
- Current state
- Tape contents
- Head position
- Execution step number

## Multi-Tape Turing Machine

A Turing Machine variant with multiple independent tapes, each with its own read/write head. Increases computational power for practical applications.

## Multi-Step Turing Machine

A Turing Machine variant where transitions can perform multiple actions (read multiple symbols, write multiple symbols, move multiple positions) in a single step.

## Nondeterministic Turing Machine (NTM)

A Turing Machine variant where a configuration can have multiple possible next configurations. The machine accepts if at least one computation path leads to acceptance.

## Reject State

A state designated as a rejecting state. When the computation halts in this state, the input is considered rejected.

## Start State

The initial state where the Turing Machine begins execution. Also called the initial state.

## State

A node in the state diagram representing a distinct configuration or control flow position in the machine.

## Tape

The infinite (or arbitrarily large) data structure where the machine reads and writes symbols. The head maintains a position on the tape.

## Tape Head

The read/write mechanism that points to a specific position on the tape. The machine can move the head left or right and read/write symbols.

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

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
