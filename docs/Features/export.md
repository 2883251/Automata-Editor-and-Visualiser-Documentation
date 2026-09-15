# Export

Export your machine as an image or as an instruction table from the **Export** button in the navigation bar. Shipped in sprint 2 ([H1]-[H2]).

The export dialog is an accessible modal: focus is trapped inside while open, **Escape** closes it, and focus returns to where you left off.

---

## Diagram as an Image (H1)

- **PNG or SVG** of the state diagram.
- Rendered at 3× screen resolution so it stays crisp in documents and slides.
- Captures **every state** — including ones scrolled off screen while zoomed in — with generous padding that covers start-arrow labels and busy self-loop arcs.
- Independent of your current pan/zoom: the exported image always frames the whole machine.

## Instructions as a Table (H2)

One row per transition, sorted by state then read symbol so the output is stable:

| State | Read | Next State | Write | Move |
|---|---|---|---|---|
| q0 | a | q1 | b | R |

- A transition that moves the head several cells in one step shows the distance alongside the direction (e.g. `R4`).
- A caption line names the machine's start, accepting, and rejecting states.

## Delivery Formats

| Format | Best for |
|---|---|
| **CSV download** | Spreadsheets — RFC 4180 quoting, UTF-8 BOM, and a locale hint so Excel splits the columns correctly |
| **HTML file download** | A ready-to-open table for a browser |
| **Clipboard copy** | Pasting straight into docs or emails — an HTML table with a plain-text tab-separated fallback for targets that don't accept rich text |

---

**Related**: [Machine Management](machine-management.md) | [Test Cases](test-cases.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
