# WebSocket Events

!!! info "Status: Planned"
    WebSocket support is not yet implemented. This page describes the intended direction.

---

## Intended Use

WebSocket connections are planned for real-time collaborative editing of Turing machines, allowing multiple users to work on the same machine simultaneously.

### Planned Event Types

- **Machine synchronisation** — state diagram changes, transition updates, conflict resolution
- **Presence** — user joined/left session, cursor position updates
- **Live computation** — execution progress streamed to connected clients
- **Notifications** — share invitations, collaboration requests

---

## Current State

The backend exposes a REST API only (`/health`, `/api/me`, and the machine persistence and sharing endpoints documented in [REST Endpoints](rest-endpoints.md)). No WebSocket server is configured and no socket library is installed; sharing is currently request/response only, so a recipient sees machines shared with them on their next fetch of `/api/machines/shared-with-me`.

---

**Related**: [REST Endpoints](rest-endpoints.md) | [Authentication & Security](authentication.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
