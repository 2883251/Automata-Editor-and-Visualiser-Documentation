# API Documentation

This section provides comprehensive reference documentation for the Automata Editor API, including REST endpoints, WebSocket events, data models, and authentication.

## Overview

The Automata Editor exposes a RESTful HTTP API for:

- Machine CRUD operations
- User authentication and account management
- Computation execution and results
- Test case and debugging features
- Sharing and collaboration
- Export and import functionality

Real-time features are supported via WebSocket connections for collaborative editing and live updates.

## Sections

- **[Authentication & Security](authentication.md)** — Auth0 integration, JWT validation, security practices
- **[REST Endpoints](rest-endpoints.md)** — Complete API endpoint reference and examples
- **[WebSocket Events](websocket-events.md)** — Real-time event types and message formats
- **[Data Models](data-models.md)** — Schema definitions for machines, users, and results

## Getting Started

### Base URL

```
https://api.automata-editor.example.com/api/v1
```

### Authentication

All requests require a valid JWT token:

```
Authorization: Bearer <token>
```

See [Authentication & Security](authentication.md) for details.

### Quick Example

```bash
# Get all machines for the current user
curl -H "Authorization: Bearer <token>" \
  https://api.automata-editor.example.com/api/v1/machines
```

---

**For detailed information, see the specific documentation pages.**

**Related**: [Development Guide](../Development%20Guide/index.md) | [Technical Architecture](../Technical%20Architecture/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
