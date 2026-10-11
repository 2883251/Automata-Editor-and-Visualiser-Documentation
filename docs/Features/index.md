# Features

A user-facing overview of what the Automata Editor can do, organised by the project's feature tiers. Every feature below is shipped and live at <https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net>. Sprint 1 delivered the two synchronised editors, sprint 2 the intermediate set (simulation, machine management, export, test cases and plots, sharing, machine variants), sprint 3 the advanced set (nondeterministic machines, simultaneous editing, the debugger, API keys and marking), and sprint 4 stationary moves, in-app help and finishing work.

---

## Basic Features

| Feature | Status | Where documented |
|---|---|---|
| Dual editor — visual diagram + instruction code, kept in sync | Shipped (sprint 1) | [UI Design](../ui-design.md) |
| Interactive state diagram — add/delete states, mark start/accepting/rejecting, draw transitions, reposition, auto-arrange | Shipped (sprint 1) | [UI Design](../ui-design.md) |
| Instruction code editor with syntax highlighting, inline diagnostics, suggestions inside a rule, and a quick fix that fills in missing transitions | Shipped (sprint 1; suggestions in sprint 3) | [UI Design](../ui-design.md) |
| Machine management — create, save, open, rename, delete; account storage, home page, explicit save and revert | Shipped (sprints 2 and 3) | [Machine Management](machine-management.md) |
| TM simulation — run, pause, step, tape visualisation, resource usage | Shipped (sprint 2) | [Simulation & Playback](simulation.md) |
| Export — diagram as image, instructions as a table | Shipped (sprint 2) | [Export](export.md) |
| Example machines — seven on the home page, one for each kind of machine | Shipped (sprints 3 and 4) | [Machine Management](machine-management.md#the-home-page) |
| In-app help — a Help dialog with seven topics and worked examples | Shipped (sprint 4, [P3]) | [Getting Started](../getting-started.md#help-inside-the-app) |
| Light and dark mode, and a layout for phones | Shipped (sprint 3) | [UI Design](../ui-design.md) |

## Intermediate Features

| Feature | Status | Where documented |
|---|---|---|
| Test cases — define inputs with expected outcomes, run them, inspect failures | Shipped (sprint 2) | [Test Cases](test-cases.md) |
| Complexity plotting — time (steps) and space (cells) against input length | Shipped (sprint 2) | [Test Cases](test-cases.md) |
| TM variants — multi-tape and multi-step machines; computational output; stationary moves | Shipped (sprint 2); stationary moves in sprint 4 | [TM Variants](tm-variants.md) |
| Sharing — share a machine by email as a viewer or an editor; copy a shared machine | Shipped (sprints 2 and 3) | [Sharing](sharing.md) |
| Debugger — step through a computation, step back, and highlight the executing instruction | Shipped (sprint 3, [J1], [J3]) | [Simulation & Playback](simulation.md) |

## Advanced Features

| Feature | Status |
|---|---|
| Nondeterministic machines with computation tree visualisation and branch traversal | Shipped (sprint 3, [O1]-[O3]); see [Nondeterministic Machines](nondeterminism.md) |
| Real-time collaborative editing, with presence and connection recovery | Shipped (sprint 3, [N1]-[N5]); see [Sharing](sharing.md) and [WebSocket Events](../API%20Documentation/websocket-events.md) |
| API key management — create, list, and revoke keys for external callers | Shipped (sprint 3, [A8]); see [API Key Management](../API%20Documentation/rest-endpoints.md#api-key-management-apiapi-keys) |
| Marking endpoint — run test cases against a machine via API key for grading and CI | Shipped (sprint 3, [A7]); see [Using the API](../API%20Documentation/using-the-api.md) and [Marking](../API%20Documentation/rest-endpoints.md#marking-apimark) |

---

**Related**: [Getting Started](../getting-started.md) | [System Overview](../Technical%20Architecture/system-overview.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
API key management and the marking endpoint were documented with the assistance of: Qoder IDE [auto].
