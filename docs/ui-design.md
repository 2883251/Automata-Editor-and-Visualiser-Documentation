# UI Design

The editor is laid out around what you do with a machine: **build** it in the diagram and the instructions, **run** it on the tape, **test** it against expected outcomes, and check **how it went**. Each of those has one place on the screen, and it stays there. This layout shipped in sprint 3 (Frontend #85, #86).

![UI Design Mockup](assets/images/ui-mockup.png){ loading=lazy }

The mockup above is the original three-pane design from sprint 1. The sections below describe the editor as it is now.

---

## On a Desktop

| Area | Where | What it holds |
|---|---|---|
| **Diagram** and **Instruction Editor** | The centre | The two views of the machine, kept in sync. They get most of the screen. |
| **Simulation** | Along the bottom, under the editors | The input, the playback controls, the speed, and the tape across the full width. For a nondeterministic machine, the computation tree instead. |
| **Side bar** | The right edge | One of two views at a time: **Test cases** or **Machine** (the tape count, the Nondeterministic setting, and both alphabets). |
| **Status bar** | Along the very bottom | Whether the instructions parse, and how the last check of the test cases went. |
| **Navigation bar** | Along the top | Home, the document actions (New, Save, Revert, Share, Export), the machine's name, colour mode, and signing in. |

### Arranging the Editors

A switch at the **top right of the editors** shows them **side by side**, **stacked**, the **diagram only**, or the **code only**. It is in the same place whichever you choose. **Reset layout** sits next to it, set apart by a thin line, and puts every area back to its default.

### The Side Bar

The **activity bar** down the right edge has an icon for each side bar view: a gear for **Machine** and a beaker for **Test cases**. Choosing an icon opens its view. Choosing the open view's icon again closes the side bar, and the editors and the simulation take the room. Both views keep their state while hidden, so a half-written test case is still there after a look at the machine settings.

### The Simulation Panel

- The panel's header collapses it to a single line. While collapsed, the header has a **Run** button that opens the panel and starts the run.
- **Opening a test case in the simulator** opens the panel, loads that case's input, and puts the cursor in it.
- A nondeterministic machine gets a taller panel, with the computation tree on the left and the selected branch beside it. The two heights are remembered separately.

### The Status Bar

- **Errors in the instructions**: "No errors", or a count such as "2 errors". Choosing the count jumps to the first error in the instruction editor, and brings the editor back into view if it was hidden.
- **The last check of the test cases**: progress while a check runs, then the totals, for example "3 passed · 1 failed · 0 inconclusive". Choosing them opens the Test cases view.

### Resizing and Keyboard Use

The line between the editors, the top edge of the Simulation panel, and the left edge of the side bar can each be dragged. Each is also keyboard focusable and moves with the arrow keys. Every control has a name a screen reader can read out, and icon-only buttons show their name in a tooltip on hover or keyboard focus.

### What Is Remembered

The arrangement, the sizes, which side bar view is open, and whether the Simulation panel is collapsed are kept in this browser, so the editor opens the way you left it. Reset layout returns to the default: side by side, the Test cases view open, and the Simulation panel open.

## On a Phone

A phone has no room to show several areas at once, so the editor shows **one view at a time** (below 768px wide):

- A **tab bar** along the bottom switches between **Diagram**, **Code**, **Run**, **Tests** and **Machine**. The status bar sits just above it.
- The **menu** button in the navigation bar opens a list over the page: the machine's name, then Home, New, Save, Share and Export, each with its name in words, and colour mode and signing in at the bottom. An action that is unavailable says why under its name. Choosing an action, pressing Escape, or tapping outside closes the menu.
- The first time the editor opens on a phone, a notice says it **works best on a bigger screen** and can still be used there. It blocks nothing, and once dismissed it stays away in that browser.

---

## Accessibility

The editor is built to be usable without a mouse and by assistive technology.

### Semantic markup

Every interactive element is a real HTML control — `<button>`, `<input>`, `<select>`, `<dialog>` — not a styled `<div>`. Landmarks use their proper roles: the navigation bar is `<nav aria-label="Main">`, page sections use `<section aria-labelledby>`, and dialogs use the native `<dialog>` element.

### Accessible names

Every control has a name a screen reader can announce. Icon-only buttons (the activity bar, the layout switch, the toolbar actions) carry an `aria-label` and show a tooltip on hover or keyboard focus. Toggle buttons expose their state through `aria-pressed`. Form fields that fail validation are marked `aria-invalid`.

### Keyboard access

- All actions reachable by mouse are reachable by keyboard.
- Panel resizers are focusable and move with the arrow keys.
- Dialogs trap focus while open and return it to the triggering element on close.
- The help dialog's topic list uses roving `tabIndex` with arrow-key navigation.
- A shared `focusRing` style (`focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600`) is applied to every interactive component, so focus is always visible without affecting mouse users.

### Live regions

- Errors and destructive-action confirmations use `role="alert"` so they are announced immediately.
- Loading indicators use `role="status"` with `aria-live="polite"` so they are announced when convenient.
- The status bar's parse-error count and test-result totals update in place without stealing focus.

### Contrast

The colour tokens are chosen so that all text a user needs to read meets WCAG AA contrast in both light and dark mode. The one token below AA (`--fg-faint`) is reserved for disabled controls and decoration, never for readable text.

### Testing

Component and route tests query the DOM by accessible role (`getByRole`, `findByRole`) rather than by CSS selector or test ID. This means a missing or incorrect accessible name fails the test suite.

---

**Related**: [Features Overview](Features/index.md) | [Frontend Architecture](Technical%20Architecture/frontend-architecture.md) | [Simulation & Playback](Features/simulation.md) | [Test Cases](Features/test-cases.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5], Qoder-IDE [Qwen3.8-Max].
