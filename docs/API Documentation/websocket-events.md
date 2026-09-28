# WebSocket Events

!!! success "Status: Shipped (sprint 3)"
    Real-time collaborative editing runs over a WebSocket on the same server as the REST API.

---

## Connecting

```
wss://<api-host>/collab/<machineId>?token=<Auth0 access token>
```

- `<machineId>` is the machine's 24-character id. A malformed id is refused with HTTP `400` before the upgrade.
- A browser cannot set an `Authorization` header on a WebSocket upgrade, so the access token travels as a query parameter. The server verifies it against Auth0 itself.
- The `Origin` header is checked against `CORS_ORIGIN`, as the REST API does.
- The frontend connects with `y-websocket`'s standard `WebsocketProvider`. Its URL is the server URL, then `/` and the room name, which is why the machine id is its own path segment.

Who may connect: the machine's **owner**, its **editors**, and its **viewers**. A viewer receives everything but anything a viewer sends to change the document is dropped. The frontend joins a room for a machine only while it is shared for editing (the owner has at least one editor), and always for an editor or a viewer. See [Sharing](../Features/sharing.md).

## Messages

The wire protocol is `y-websocket`'s: each message starts with a type number.

| Type | Name | Direction | Meaning |
|---|---|---|---|
| `0` | Sync | Both | `y-protocols/sync`: the document's state and updates |
| `1` | Awareness | Both | Presence: who is here, and their cursor or selected state |
| `4` | Saved | Server to client | The room's latest changes were written to the database |
| `5` | Access changed | Server to client | This user's role changed while they stayed connected. It carries nothing: the client fetches the machine over REST to learn its new role |

Types `2` and `3` are not used: `y-websocket`'s client reserves them.

## Close Codes

| Code | Meaning | Client reconnects? |
|---|---|---|
| `4001` | The token is missing or does not verify | Yes |
| `4002` | A malformed message | Yes |
| `4003` | The `Origin` is not allowed | Yes |
| `4004` | No such machine, or this user has no access to it | Yes |
| `4403` | This user lost access while connected: the owner removed them or deleted the machine | No |

`y-websocket` does not reconnect after a close code from `4400` to `4499`, which is why `4403` is in that range.

## The Room

- There is one Yjs document per machine. It holds `source` (a `Y.Text`), `positions` (a `Y.Map` from state id to `{ x, y }`), and the test cases (a `Y.Map` from id to test case, with a `Y.Array` of ids for their order). These are the same fields `PUT /api/machines/:id` saves.
- While people are connected, the room writes its content back to the machine about 2 seconds after the last change, and at least every 10 seconds during continuous editing. It also writes when the last person leaves, then closes. Each write sends message `4` to everyone in the room.
- The room's Yjs state is stored with the machine (`collabState`), and a room reopens from it rather than from `source`. That keeps one edit history, so a browser holding a cached copy of the room does not end up with duplicated text.
- A content save over REST (`PUT /api/machines/:id`) is applied to the open room, or to the stored `collabState`, as ordinary edits. Without this, the next room would bring back the older content.

## Access Changes While Connected

When the owner changes someone's access, the change applies to their open connection straight away:

| Change | What the server does |
|---|---|
| Viewer made an editor | Accepts their edits from now on, and sends message `5` |
| Editor made a viewer | Drops their edits from now on, and sends message `5` |
| Access removed | Closes their connection with `4403` |
| Machine deleted | Closes every connection to it with `4403` |

A role change that leaves the role as it was sends nothing.

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Authentication & Security](authentication.md) | [Sharing](../Features/sharing.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
