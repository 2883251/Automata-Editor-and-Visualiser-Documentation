# Nondeterministic Machines

A nondeterministic machine may have **more than one transition** for the same state and the same symbols under its heads. Where that happens the computation forks, and the machine **accepts if any branch accepts**. The editor shows every branch as a **computation tree** that you can walk through. Shipped in sprint 3 ([O1]-[O3]), on Core 3.1.0.

---

## Making a Machine Nondeterministic

Open **Machine** in the side bar (the gear icon on the activity bar) and tick **Nondeterministic**. In the instructions this is one line:

```text
nondeterministic: true
```

After that, a state may have several rules for the same read. Add them the usual way, in the diagram or in the instructions:

```text
transitions:
  (q0, '0') -> (q0, '0', R)
  (q0, '1') -> (q0, '1', R)
  (q0, '1') -> (q_accept, '1', R)
```

Reading `1` in `q0`, this machine can either carry on or accept. Each branch is its own entry on the diagram's edge label and can be edited or deleted on its own.

- On a machine that is **not** marked nondeterministic, a second rule for the same read is an error, and the message says to mark the machine nondeterministic.
- To go back to a deterministic machine, remove the extra branches first. Until then the tick box stays on and says so.
- The **Contains a 1** example on the home page is a small nondeterministic machine to start from.

## The Computation Tree

For a nondeterministic machine, the Simulation panel shows a tree in place of the single tape. Type an input and press **Run**.

- The tree grows **one level at a time**, at the chosen speed. Every configuration the machine can reach after *n* steps is on level *n*.
- Each node shows its state, its step, and how that branch stands:

| Status | Meaning |
|---|---|
| **Accepted** | The branch reached an accepting state |
| **Rejected** | The branch reached a rejecting state |
| **Stuck** | No transition applies, so the branch stops |
| **Open** | The branch can still step |

- Each edge is labelled with the transition that was taken.
- **Pause** stops the tree growing, and **Reset** clears it.

### The Outcome

| Outcome | When |
|---|---|
| **Accepted** | One branch reaches an accepting state. The search stops there, so that branch is a shortest accepting one |
| **Rejected** | Every branch stops without accepting |
| **Inconclusive** | The tree reached 10,000 configurations with branches still open, so there is no answer yet |

The limit of 10,000 configurations keeps a machine that never halts from running forever. It counts every node in the tree, not the steps of one branch.

### Large Trees

At most 300 nodes are drawn at once. Deeper levels are folded away, and a node with folded branches below it shows how many are hidden. **Show more levels** and **Show fewer levels** change how deep the drawing goes.

## Walking a Branch

Click a node to select it. Beside the tree (under it on a phone), the **Selected branch** panel shows that configuration's tape, and:

- The path from the start to the selected node is emphasised in the tree.
- The selected node's state is highlighted on the **diagram**, and the rule that led to it is highlighted in the **instructions**.
- **Step** moves one step further down the branch. At a fork it lists the branches and asks which one to follow.
- **Step Back** moves one step back up.
- **Show accepting path** jumps to the node that accepted. It is unavailable while no branch has accepted.

## What Does Not Apply

- **Test cases are not checked** for a nondeterministic machine. The Test cases view says so. The cases stay saved with the machine, and the view comes back if the machine becomes deterministic again.
- The **instruction table** export lists the first transition for each read, not the extra branches.
- The [marking endpoint](../API%20Documentation/using-the-api.md) runs a machine as a deterministic one, so its verdicts are not meaningful for a nondeterministic machine.

---

**Related**: [Simulation & Playback](simulation.md) | [TM Variants](tm-variants.md) | [Core Package Architecture](../Technical%20Architecture/core-package.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Claude Code [Claude Opus 5.5].
