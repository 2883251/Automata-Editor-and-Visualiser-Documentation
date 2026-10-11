# Test Cases & Complexity Plots

Test cases let you state what a machine *should* do for specific inputs, run the whole suite at once, and see exactly where reality disagrees. Sprint 2 shipped authoring, running, and inspection ([I1]-[I3]) plus time/space complexity plots ([K2]).

---

The test cases live in the **Test cases** view of the side bar. The beaker icon on the activity bar, down the editor's right edge, opens it; it is the view that is open to begin with.

## Authoring Cases (I1)

A test case pairs an **input string** with an **expectation**:

- **Accepts** — the machine must halt in an accepting state,
- **Rejects** — it must halt without accepting: in a rejecting state, or stuck with no transition to apply, or
- **Output** — after halting, what the machine left on the tape must equal the given string. Blanks at either end are ignored, and so is where on the tape it sits.

- The expected output keeps what you typed even when you switch the expectation radio away and back, so typing is never lost.
- Cases are **saved with the machine**, so they survive a reload and follow the machine to anyone it is shared with.
- A case that is already in the list (the same input and the same expectation) is not added twice.
- **Output** checks the first tape only, so it is offered on single-tape machines.
- Test cases are **not checked for a nondeterministic machine**. The view says so, and the cases stay saved. See [Nondeterministic Machines](nondeterminism.md).

## Running a Suite (I2)

Run the suite with **Check all test cases**: cases execute **one at a time**, yielding between cases, so the page never freezes while a long suite runs.

| Verdict | Meaning |
|---|---|
| **Passed** | The outcome matched the expectation |
| **Failed** | The outcome differed from the expectation |
| **Inconclusive** | The case hit the 2,000-step budget without halting — no verdict is issued, and no step or cell counts are reported |
| **Not checked** | The input contained symbols outside the machine's input alphabet, so the case was flagged and skipped |

- The per-case step budget is **terminal** here: an *inconclusive* verdict, deliberately distinct from the simulator's budget *pause* (see [Simulation & Playback](simulation.md)), so raising the interactive checkpoint can never silently redefine a test verdict.
- A suite run can be cancelled mid-way. The results so far are kept.
- Changing the machine clears the results, since they no longer describe it.
- The **status bar** along the bottom of the editor shows progress while a check runs, then totals the passed, failed and inconclusive cases (and any not checked). Choosing it opens the Test cases view.
- When some cases are inconclusive, a note next to the list says why and suggests opening one in the simulator to keep stepping.

## Inspecting a Failure (I3)

**Compare**, on a case that did not pass, shows what was expected beside what the machine did. For an Output expectation it shows the two tapes and the **first cell where the tape diverged** from the expected value. Any case that has been checked can be opened in the simulator preloaded with that case's input, so you can step through and see why the machine disagrees. This opens the Simulation panel along the bottom, if it was collapsed, and puts the cursor in its input.

## Complexity Plots (K2)

From a suite's measured results, **Plot complexity** opens the plot:

- Two series against **input length**, one at a time: **Steps** (time) and **Tape cells used** (space).
- A table under the plot lists the measurements behind it, shortest input first.
- A plot needs at least two measured cases.
- Axes always start at zero — an axis starting near the lowest value would exaggerate growth.
- Only *measured* cases are plotted. A budget-hit case carries no counts by design; plotting it would draw a misleading flat line.
- Where a plot cannot be drawn yet, the dialog says why, and it counts the cases left out: those that did not halt within the step budget and those that were not checked.

---

**Related**: [Simulation & Playback](simulation.md) | [TM Variants](tm-variants.md) | [Using the API](../API%20Documentation/using-the-api.md) (the same checks from a script) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
