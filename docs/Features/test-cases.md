# Test Cases & Complexity Plots

Test cases let you state what a machine *should* do for specific inputs, run the whole suite at once, and see exactly where reality disagrees. Sprint 2 shipped authoring, running, and inspection ([I1]-[I3]) plus time/space complexity plots ([K2]).

---

## Authoring Cases (I1)

A test case pairs an **input string** with an **expectation**:

- **accepts** — the machine must halt in an accepting state,
- **rejects** — it must halt in an explicit rejecting state, or
- **a final tape value** — after halting, the tape's non-blank content must equal the given string.

- The final-tape field keeps what you typed even when you switch the expectation radio away and back, so typing is never lost.
- Cases survive a page reload.

## Running a Suite (I2)

Run the suite from the test-case panel: cases execute **one at a time**, yielding between cases, so the page never freezes while a long suite runs.

| Verdict | Meaning |
|---|---|
| **Passed** | The outcome matched the expectation |
| **Failed** | The outcome differed from the expectation |
| **Inconclusive** | The case hit the 2,000-step budget without halting — no verdict is issued, and no step or cell counts are reported |
| **Excluded** | The input contained symbols outside the machine's input alphabet — flagged and skipped |

- The per-case step budget is **terminal** here: an *inconclusive* verdict, deliberately distinct from the simulator's budget *pause* (see [Simulation & Playback](simulation.md)), so raising the interactive checkpoint can never silently redefine a test verdict.
- A suite run can be cancelled mid-way.
- A summary line totals the passed / failed / excluded cases.

## Inspecting a Failure (I3)

A failing final-tape expectation shows the **first cell where the tape diverged** from the expected value. Any failure can be opened in the simulator preloaded with that case's input, so you can step through and see why the machine disagrees.

## Complexity Plots (K2)

From a suite's measured results, open the complexity plot:

- Two series against **input length**: **steps** (time) and **tape cells the head visited** (space).
- Axes always start at zero — an axis starting near the lowest value would exaggerate growth.
- Only *measured* cases are plotted. A budget-hit case carries no counts by design; plotting it would draw a misleading flat line.
- Where a plot cannot be drawn yet, the dialog says why and counts the excluded / not-yet-run cases.

---

**Related**: [Simulation & Playback](simulation.md) | [TM Variants](tm-variants.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
