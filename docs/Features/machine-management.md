# Machine Management

Create, open, save, rename, and delete machines. Machines are listed on the **home page**, and the machine you have open is managed from the navigation bar. Machine CRUD shipped in sprint 2 ([F2]-[F5]); account storage ([F7]), the home page, and the save and rename model below came in sprint 3 (Frontend #84, #87, #88, #92).

---

## What Gets Saved

A saved machine keeps everything needed to reconstruct it exactly:

- the **instruction code**, the machine's source text;
- the **diagram layout**, the position of every state on the canvas; and
- its **test cases**.

## The Home Page

The home page is at `/`. The **Home** link in the navigation bar returns to it from the editor. It has four sections, each a grid of cards with a small drawing of the machine's diagram:

| Section | What it shows |
|---|---|
| **Your machines** | A **New machine** card, then your machines, most recently saved first. Each card you own can be renamed, shared, or deleted. |
| **Saved in this browser** | Only when you are signed in and machines saved before signing in are still in this browser. Each can be moved to your account, or all at once. A move saves the machine to your account before removing it from the browser, so a failed move loses nothing. |
| **Shared with you** | Machines other people shared with you, with the owner's name. See [Sharing](sharing.md). |
| **Example machines** | Three built-in examples: **Equal a and b** (a decider), **Binary increment** (computes an output), and **Copy to a second tape** (multi-tape). |

Signed out, **Your machines** shows the machines saved in this browser, and both of the first sections offer a link to sign in. Dates are shown year first (`2026/09/24`).

## Creating a Machine

**New** (the card on the home page, or the button in the navigation bar) asks for a name, saves a blank machine straight away, and opens it at its own address, `/m/<id>`. Opening an example does the same with the example's content, named after it, so an example never changes. **Make my own copy**, on a machine shared with you as a viewer, uses the same dialog.

If the machine you are working on has unsaved changes, you are asked first.

The editor at `/editor` with no machine is a scratch page for a machine that has not been saved. Its **Save** asks for a name the first time.

## Saving and Reverting

A machine only you work on saves **when you press Save** (or Ctrl+S, Cmd+S on a Mac). The Save button in the navigation bar shows the state:

| State | What you see |
|---|---|
| Unsaved changes | **Save**, enabled, and **Revert to last save** next to it |
| No changes | A check mark, **Saved**, disabled |

- **Revert to last save** asks first, then puts the machine back as it was last saved.
- Unsaved changes are kept in the browser, so a reload keeps them, and they still show as unsaved.
- Closing the tab, or opening a different machine from the home page, asks first while there are unsaved changes.

A machine that is **shared for editing** (you own it and have given someone the editor role, or you are an editor on it) is different: it opens in a live collaboration room and saves on its own as you type. The Save slot then shows an icon for the room's state: connecting, saving, all changes saved, or offline. When you give the first person the editor role, your unsaved changes are saved first. See [Sharing](sharing.md).

## Renaming

The open machine's name sits in the middle of the navigation bar. If you own the machine, click the name to rename it: Enter or clicking away saves the new name, Escape cancels, and a blank name is refused. Renaming does not save the machine's content. Editors and viewers see the name as plain text. Cards on the home page can also be renamed.

## Where Machines Live

- **Signed in:** machines are saved to your account on the backend (see [REST Endpoints](../API%20Documentation/rest-endpoints.md)), so they follow you to any browser.
- **Signed out:** machines are saved in this browser only. Clearing the browser's storage clears them. Sign in to move them to your account from the home page.

---

**Related**: [Sharing](sharing.md) | [Simulation & Playback](simulation.md) | [Export](export.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
