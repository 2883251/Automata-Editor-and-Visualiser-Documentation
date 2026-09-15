# Machine Management

Create, save, open, rename, and delete machines from the **Machines** list in the navigation bar. Shipped in sprint 2 ([F2]-[F5]).

---

## What Gets Saved

A saved machine keeps everything needed to reconstruct it exactly:

- the **instruction code** — the machine's source text, and
- the **diagram layout** — the position of every state on the canvas.

## Working With the Machine List

| Action | How |
|---|---|
| **Create** | Start a new empty machine from the machines list |
| **Save** | Save the machine you are editing; the first save creates it, later saves update it |
| **Open** | Load a machine from the list back into the editor |
| **Rename** | Change a saved machine's name |
| **Delete** | Remove a saved machine |

- The list is ordered by most recently updated, so the machine you last touched is at the top.
- Saving after a refresh updates the machine you already have open instead of creating a near-duplicate (covered by e2e tests).

## Where Machines Live (and Don't Yet)

Machines are stored **in your browser only** — they belong to the browser profile you saved them from, not to your signed-in account. Account-backed storage ([F7]) is in review; the backend API for stored and shared machines already exists (see [REST Endpoints](../API%20Documentation/rest-endpoints.md)) and the editor will adopt it when that change lands. Until then, clearing your browser storage clears your saved machines.

---

**Related**: [Simulation & Playback](simulation.md) | [Export](export.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
