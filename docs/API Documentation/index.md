# API Documentation

This section documents the Automata Editor and Visualiser backend API.

---

## Current Status

The API is live and serves the editor's machine persistence, sharing, collaborative editing, and marking endpoints:

| Endpoint group | Description | Reference |
|---|---|---|
| `GET /health` | Liveness check | [REST Endpoints](rest-endpoints.md#public-endpoints) |
| `/api/machines` | Machine CRUD, list, shared-with-me | [REST Endpoints](rest-endpoints.md#machine-management-apimachines) |
| `/api/machines/:id/shares` | Sharing by email, role management | [REST Endpoints](rest-endpoints.md#sharing-apimachinesidshares) |
| `/api/api-keys` | API key creation, listing, revocation | [REST Endpoints](rest-endpoints.md#api-key-management-apiapi-keys) |
| `/api/mark` | Marking — run test cases against a machine via API key | [REST Endpoints](rest-endpoints.md#marking-apimark) |
| WebSocket | Real-time collaborative editing | [WebSocket Events](websocket-events.md) |

---

## Sections

- **[Authentication & Security](authentication.md)** — Auth0 integration, JWT validation, API key authentication, CORS
- **[REST Endpoints](rest-endpoints.md)** — Full endpoint reference (machines, sharing, API keys, marking)
- **[WebSocket Events](websocket-events.md)** — Planned real-time event types
- **[Data Models](data-models.md)** — Core type definitions from `@brh/automata-core`

---

## Base URL

- **Local development**: `http://localhost:4000`
- **Production**: Azure App Service URL (configured per deployment)

---

**Related**: [Development Guide](../Development%20Guide/index.md) | [Technical Architecture](../Technical%20Architecture/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
API key management and the marking endpoint were documented with the assistance of: Qoder IDE [auto].
