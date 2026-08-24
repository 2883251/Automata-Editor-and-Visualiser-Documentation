# REST Endpoints

Reference for all HTTP endpoints exposed by the backend API.

---

## Currently Implemented

### `GET /health`

Liveness check used by deployment platforms and monitoring.

- **Response**: `200 OK`
- **Body**: `{"status":"ok"}`
- **Authentication**: Not required

```bash
curl http://localhost:4000/health
# {"status":"ok"}
```

---

## Planned Endpoints

!!! info "Status: Planned"
    The following endpoint groups have dependencies installed (Mongoose, Zod, express-oauth2-jwt-bearer) but are not yet implemented.

### Machine Management

| Method | Path | Description |
|---|---|---|
| `POST` | `/machines` | Create a new machine |
| `GET` | `/machines` | List machines for the authenticated user |
| `GET` | `/machines/:id` | Get a single machine by ID |
| `PUT` | `/machines/:id` | Update a machine |
| `DELETE` | `/machines/:id` | Delete a machine |

### Computation & Execution

| Method | Path | Description |
|---|---|---|
| `POST` | `/machines/:id/run` | Execute a computation with given input |
| `POST` | `/machines/:id/step` | Step through execution one transition at a time |

### User Management

User identity is managed by Auth0. The API may expose:

| Method | Path | Description |
|---|---|---|
| `GET` | `/profile` | Get the authenticated user's profile |
| `DELETE` | `/profile` | Delete the user's account and data |

---

## Error Format

All error responses follow a consistent shape:

```json
{
  "error": "Description of what went wrong"
}
```

| Status Code | Meaning |
|---|---|
| `400` | Bad request — invalid input |
| `401` | Unauthenticated — missing or invalid JWT |
| `404` | Resource not found |
| `500` | Internal server error |

---

**Related**: [Authentication & Security](authentication.md) | [Data Models](data-models.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
