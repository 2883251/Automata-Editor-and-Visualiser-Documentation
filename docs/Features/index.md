# Features

A user-facing overview of what the Automata Editor can do, organised by the project's feature tiers. Sprint 2 (August 26 – September 15, 2026) delivered the intermediate feature set: simulation with playback, machine management, export, test cases with complexity plots, and the API for machine sharing, plus multi-tape machine support in the Core package.

---

## Basic Features

| Feature | Status | Where documented |
|---|---|---|
| Dual editor — visual diagram + instruction code, kept in sync | Shipped (sprint 1) | [UI Design](../ui-design.md) |
| Interactive state diagram — add/delete states, mark start/accepting/rejecting, draw transitions, reposition, auto-arrange | Shipped (sprint 1) | [UI Design](../ui-design.md) |
| Instruction code editor with syntax highlighting and inline diagnostics | Shipped (sprint 1) | [UI Design](../ui-design.md) |
| Machine management — create, save, open, rename, delete; account storage, home page, explicit save and revert | Shipped (sprints 2 and 3) | [Machine Management](machine-management.md) |
| TM simulation — run, pause, step, tape visualisation, resource usage | Shipped (sprint 2) | [Simulation & Playback](simulation.md) |
| Export — diagram as image, instructions as a table | Shipped (sprint 2) | [Export](export.md) |

## Intermediate Features

| Feature | Status | Where documented |
|---|---|---|
| Test cases — define inputs with expected outcomes, run them, inspect failures | Shipped (sprint 2) | [Test Cases](test-cases.md) |
| Complexity plotting — time (steps) and space (cells) against input length | Shipped (sprint 2) | [Test Cases](test-cases.md) |
| TM variants — multi-tape and multi-step machines; computational output | Shipped (sprint 2) | [TM Variants](tm-variants.md) |
| Sharing — share a machine by email as a viewer or an editor; copy a shared machine | Shipped (sprints 2 and 3) | [Sharing](sharing.md) |
| Debugger — step through a computation, step back, and highlight the executing instruction | Shipped (sprint 3, [J1], [J3]) | [Simulation & Playback](simulation.md) |

## Advanced Features

| Feature | Status |
|---|---|
| Nondeterministic machines with computation tree visualisation and branch traversal | Shipped (sprint 3, [O1]-[O3]) |
| Real-time collaborative editing, with presence and connection recovery | Shipped (sprint 3, [N1]-[N5]); see [Sharing](sharing.md) and [WebSocket Events](../API%20Documentation/websocket-events.md) |

---

**Related**: [Getting Started](../getting-started.md) | [System Overview](../Technical%20Architecture/system-overview.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
