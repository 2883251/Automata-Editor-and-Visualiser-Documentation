# Simulation & Playback

The **Simulation** panel, along the bottom of the editor, turns the machine you are editing into a running computation: type an input, press Run, and watch the tape, the current state, and the active transition update as the machine executes. Shipped in sprint 2 ([G2]-[G5]).

---

## Running a Computation

1. Type an input string into the Simulation panel's input box (an empty input is valid — the tape is simply all blanks).
2. Press **Run**. The machine executes at the chosen pace, one transition at a time.
3. Watch the outcome: **Accepted** (the machine reached an accepting state), **Rejected** (it reached an explicit rejecting state), or **Stuck** (no transition was defined for what the head was reading).

The distinction between *rejected* and *stuck* matters to learners: a machine that "rejects" by having no rule for a symbol is telling you something different from one that walks into a rejecting state, and the Simulation panel reports the two separately.

## The Panel

- The input, the playback controls, the speed, and the current state, steps and cells visited sit in one row. The tape runs under them across the full width.
- The panel's header collapses it to a single line, to give the editors more room. While collapsed, its **Run** button opens the panel and starts the run.
- Its top edge can be dragged, or moved with the arrow keys, to make it taller or shorter. The editor remembers the height.
- **Opening a test case in the simulator** opens the panel, loads the case's input, and puts the cursor in it. See [Test Cases](test-cases.md#inspecting-a-failure-i3).
- A **nondeterministic** machine shows its computation tree here instead of a single tape, in a taller panel, with the selected branch and its tape beside the tree.

## Playback Controls

| Control | What it does |
|---|---|
| **Run** | Starts the computation from the beginning of the input |
| **Pause** | Freezes the run where it is, for inspection |
| **Step** | Applies exactly one transition — useful for tracing a machine rule by rule |
| **Reset** | Tears the run down and returns to idle |
| **Continue / Stop** | Shown only after a budget pause (below) |

- The **speed** is adjustable; the default is 200 ms per step.
- **Stuck vs rejected**: halting in a rejecting state reports *Rejected*; having no applicable transition reports *Stuck*.
- Editing the machine (diagram or code) while a run is live **cancels the run** rather than continuing against a stale machine. Merely dragging a state around does not cancel anything.

## Step Budget Checkpoint

A long-running (or accidentally infinite) computation pauses by itself after **2,000 steps** with a *budget paused* status, so it can never silently run away:

- **Continue** resumes the run, resetting the checkpoint counter.
- **Stop** behaves like a manual pause — the run freezes for inspection or reset.

This checkpoint exists purely to keep the interface responsive; it is not a verdict on the machine. (A related but distinct budget applies to [test cases](test-cases.md), where hitting it produces an *inconclusive* result.)

## What the Visualiser Shows

- **Tape**: the tape contents render across the panel and scroll to keep the head in the middle, also when the panel is opened or resized; the cell written most recently gets a brief emphasis.
- **Current state**: highlighted on the state diagram.
- **Active transition**: the edge just taken is highlighted on the diagram.
- **Resource usage (G5)**: the step counter and a live count of **distinct tape cells the head has visited** — the space the computation has actually touched, shown as it grows.

## How It Stays Responsive

The simulation drives the Core package's single-step `step()` function on a timer loop rather than calling its run-to-completion `simulate()`, which cannot be paused, resumed, or watched step by step. Every step is one small unit of work on the interface's own schedule, which is what lets playback stay smooth and every control interject ([G2]).

---

**Related**: [Test Cases](test-cases.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
