# API Documentation

This section documents the Automata Editor and Visualiser backend API.

---

## Current Status

The API is live and serves the editor's machine persistence, sharing, collaborative editing, and marking endpoints. To call it from your own script or marking tool, start with **[Using the API](using-the-api.md)**.

| Endpoint group | Description | Reference |
|---|---|---|
| `GET /health` | Liveness check | [REST Endpoints](rest-endpoints.md#public-endpoints) |
| `/api/machines` | Machine CRUD, list, shared-with-me | [REST Endpoints](rest-endpoints.md#machine-management-apimachines) |
| `/api/machines/:id/shares` | Sharing by email, role management | [REST Endpoints](rest-endpoints.md#sharing-apimachinesidshares) |
| `GET /api/me` | The signed-in caller's identity | [REST Endpoints](rest-endpoints.md#get-apime) |
| `/api/api-keys` | API key creation, listing, revocation | [REST Endpoints](rest-endpoints.md#api-key-management-apiapi-keys) |
| `/api/mark` | Marking — run test cases against a machine via API key | [REST Endpoints](rest-endpoints.md#marking-apimark) |
| WebSocket `/collab/:machineId` | Real-time collaborative editing | [WebSocket Events](websocket-events.md) |

---

## Sections

- **[Using the API](using-the-api.md)** — Step by step: create a key, send a marking request, read the reply
- **[Authentication & Security](authentication.md)** — Auth0 integration, JWT validation, API key authentication, CORS
- **[REST Endpoints](rest-endpoints.md)** — Full endpoint reference (machines, sharing, API keys, marking)
- **[WebSocket Events](websocket-events.md)** — The collaboration room: connecting, messages, close codes, saving
- **[Data Models](data-models.md)** — Core type definitions from `@brh/automata-core`, the database schema, and production data

---

## Base URL

- **Local development**: `http://localhost:4000`
- **Production**: `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net`

The app that uses it is at `https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net`.

---

**Related**: [Development Guide](../Development%20Guide/index.md) | [Technical Architecture](../Technical%20Architecture/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
API key management and the marking endpoint were documented with the assistance of: Qoder IDE [auto].
The base URLs and section list were updated with the assistance of: Claude Code [Claude Opus 5.5].
