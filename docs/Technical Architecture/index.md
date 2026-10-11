# Technical Architecture

This section provides an in-depth look at the Automata Editor's system design, component interactions, and architecture patterns.

## Overview

The Automata Editor follows a non-monolithic architecture with clear separation between:

- **Frontend Application** — React-based UI with visual and code editors
- **Shared Core Package** — `@brh/automata-core`: the machine model, parser, simulator and test cases, used by both applications
- **Backend API** — Express.js REST API, the collaboration WebSocket, and the marking endpoint
- **Database** — MongoDB for persistence
- **External Services** — Auth0 for sign-in

## Sections

- **[System Overview](system-overview.md)** — The four repositories, how they talk to each other, and where each is deployed
- **[Core Package Architecture](core-package.md)** — Machine model, instruction language, simulator, computation tree, test cases
- **[Backend Architecture](backend-architecture.md)** — API design, collaboration rooms, database, marking
- **[Frontend Architecture](frontend-architecture.md)** — Routes, editor layout, feature modules, persistence

---

*For detailed technical specifications, refer to the specific architecture pages.*

**Related**: [API Documentation](../API%20Documentation/index.md) | [Development Guide](../Development%20Guide/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
