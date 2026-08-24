# API Documentation

This section documents the Automata Editor and Visualiser backend API.

---

## Current Status

The API is in early development. The only endpoint currently implemented is:

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Liveness check. Returns `{"status":"ok"}` |

!!! info "Status: Planned"
    The following endpoint groups are planned but not yet implemented:

    - Machine CRUD operations (create, read, update, delete, list)
    - Computation execution and results
    - User account management (delegated to Auth0)
    - Sharing and collaboration
    - Test case management
    - WebSocket events for real-time collaborative editing

---

## Sections

- **[Authentication & Security](authentication.md)** — Auth0 integration, JWT validation, CORS
- **[REST Endpoints](rest-endpoints.md)** — Current and planned endpoint reference
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
