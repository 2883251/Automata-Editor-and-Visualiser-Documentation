# Sharing Machines

Share a machine with another user by **email**, as a **viewer** or an **editor**. Only the owner can rename, delete, or re-share it. Sprint 2 shipped the sharing API ([M1]) together with the user records it relies on ([F1]) and the editor UI ([M1b]). Sprint 3 added roles, copies, and simultaneous editing.

> Status: **shipped** (sprints 2 and 3).

---

## How Sharing Works

1. The owner shares a machine with a user's **email address**.
2. The backend resolves the email against its **user records** — the directory of people who have signed in — so a recipient must have used the app at least once. Sharing with an email that has never signed in fails with a clear "no user with that email" error.
3. The machine appears in the recipient's **Shared with you** section on the home page.
4. The owner can list who a machine is shared with, change each person's role, and **revoke** a user's access.

## Roles

| Role | Can do |
|---|---|
| **Owner** | Everything: edit, rename, delete, and manage who has access |
| **Editor** | Edit the machine and its test cases, at the same time as others |
| **Viewer** | Open and run the machine and check its test cases, but not change it |

- A **viewer** sees a read-only banner naming the owner, with **Make my own copy**. The copy asks for a name and becomes a new machine the viewer owns, test cases included.
- While a machine has at least one **editor**, it opens in a live collaboration room for its owner and editors. Changes save on their own, and everyone sees each other's edits and cursors. Viewers watch the same room read-only, so they see changes as they happen.
- A machine with no editors saves only when its owner presses Save. See [Machine Management](machine-management.md#saving-and-reverting).

## Changing Access While a Machine Is Open

A change the owner makes takes effect straight away for anyone who has the machine open, with no reload:

- **Viewer made an editor:** they can edit at once, and a notice says so.
- **Editor made a viewer:** the machine turns read-only at once. They stay in the room and keep seeing changes.
- **Access removed, or the machine deleted:** the machine turns read-only, with a notice that they no longer have access. Their edits stop being accepted immediately.

When the owner gives the first person the editor role, the owner's unsaved changes are saved first, so the live room starts from them.

Guarantees worth knowing:

- **Owner-only management** — and ownership is checked *before* the email lookup, so someone else's machine id cannot be used to probe which emails have accounts.
- **No self-sharing** — sharing with your own account is refused.
- **Sign-in creates your record** — your email and name are taken from your sign-in token (see [Authentication](../API%20Documentation/authentication.md)); no credentials are ever stored by this app.

## Endpoints

Full request/response shapes live in [REST Endpoints](../API%20Documentation/rest-endpoints.md). In short: list a machine's shares, add a share by email, change a role, revoke a share, and list the machines shared with you. The live room is described in [WebSocket Events](../API%20Documentation/websocket-events.md).

## In the Editor

The sharing UI appears in two places:

- **Home page**: a Share button on each machine card you own opens the share dialog.
- **Editor**: a Share button in the navigation bar (enabled only for saved machines you own; disabled with a reason when you're signed out or the machine isn't saved yet).

The share dialog lets you:

- **Share by email** — type the recipient's email address and choose **Can view** or **Can edit**; the backend resolves the email against signed-in users.
- **See who has access** — a list of everyone the machine is shared with, where each person's role can be changed.
- **Revoke access** — remove a person immediately (restored if the request fails).

Error cases are handled inline: sharing with an unknown email or your own email shows a reason and shares nothing.

---

**Related**: [Machine Management](machine-management.md) | [REST Endpoints](../API%20Documentation/rest-endpoints.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
