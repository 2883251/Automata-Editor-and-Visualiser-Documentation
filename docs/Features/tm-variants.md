# TM Variants: Multi-Tape, Multi-Step, and Output

Sprint 2 extended the Core package to 3.0.0 with three closely related capabilities: machines with **several tapes** ([L3]), transitions that move a head **several cells in one step** ([L5]), and reading a computational machine's **output from its tape** ([L2]).

> Status: these capabilities are **shipped in the Core package (3.0.0)**. The editor UI for authoring variant machines is still in review — the editor today authors single-tape machines.

---

## Multi-Tape Machines (L3)

- A machine declares how many tapes it operates on (`tapes: 2` in the instruction language).
- Every transition reads **one symbol per tape** and applies **one write-and-move action per tape**; each head moves independently.
- Input is written to tape 0; the other tapes start blank.

Instruction-language example (from the grammar):

```text
tapes: 2

transitions:
  (q0, 'a', '_') -> (q1, 'b', R; '_', R)
```

Single-tape programs are unchanged — `tapes: 1` is the default, and existing machines parse as before.

## Multi-Step Transitions (L5)

A per-tape action may carry an optional **distance** after its direction:

```text
(q0, 'a') -> (q1, 'a', R, 4)
```

This moves that tape's head 4 cells in a **single transition application** — one step of the computation, counted as one. Omitted distances mean one cell, exactly as before, and each tape's action states its distance independently.

## Computational Output (L2)

For machines that compute rather than merely accept, the output is read from the halted first tape: its non-blank extent with surrounding blanks trimmed. Test cases can assert a **final tape value** against this content, and the simulation reports the derived output distinctly from the raw tape (see [Test Cases](test-cases.md) and [Simulation & Playback](simulation.md)).

## Under the Hood

The machine model, parser, simulator, and serialisation changes behind these features — including the versioned serialisation schema and the 2.x → 3.0.0 migration notes for API consumers — are documented in [Core Package Architecture](../Technical%20Architecture/core-package.md).

---

**Related**: [Test Cases](test-cases.md) | [Simulation & Playback](simulation.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
