# Sharing Machines

Share a machine with another user by **email**: recipients can open the machine, and only the owner can change or delete it. Sprint 2 shipped the sharing API ([M1]) together with the user records it relies on ([F1]) and the editor UI ([M1b]).

> Status: **shipped** (sprint 2).

---

## How Sharing Works

1. The owner shares a machine with a user's **email address**.
2. The backend resolves the email against its **user records** — the directory of people who have signed in — so a recipient must have used the app at least once. Sharing with an email that has never signed in fails with a clear "no user with that email" error.
3. The machine appears in the recipient's *shared with me* list; they can read it, but only the owner may edit, delete, or re-share it.
4. The owner can list who a machine is shared with and **revoke** a user's access.

Guarantees worth knowing:

- **Owner-only management** — and ownership is checked *before* the email lookup, so someone else's machine id cannot be used to probe which emails have accounts.
- **No self-sharing** — sharing with your own account is refused.
- **Sign-in creates your record** — your email and name are taken from your sign-in token (see [Authentication](../API%20Documentation/authentication.md)); no credentials are ever stored by this app.

## Endpoints

Full request/response shapes live in [REST Endpoints](../API%20Documentation/rest-endpoints.md). In short: list a machine's shares, add a share by email, revoke a share, and list the machines shared with you.

## In the Editor

The sharing UI appears in two places:

- **Machine list**: a Share button on each machine you own opens the share dialog.
- **Editor**: a Share button in the navigation bar (enabled only for saved machines you own; disabled with a reason when you're signed out or the machine isn't saved yet).

The share dialog lets you:

- **Share by email** — type the recipient's email address; the backend resolves it against signed-in users.
- **See who has access** — a list of everyone the machine is shared with.
- **Revoke access** — remove a person immediately (restored if the request fails).

Error cases are handled inline: sharing with an unknown email or your own email shows a reason and shares nothing.

---

**Related**: [Machine Management](machine-management.md) | [REST Endpoints](../API%20Documentation/rest-endpoints.md) | [Features Overview](index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
