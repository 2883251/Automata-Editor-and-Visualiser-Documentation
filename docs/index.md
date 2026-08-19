# Automata Editor Documentation

Welcome to the comprehensive documentation for the **Automata Editor** — an interactive, web-based tool for learning and visualizing Turing Machines through synchronized visual and code editors.

## What is the Automata Editor?

The Automata Editor is an educational platform designed for computer science students and educators working in Formal Languages and Automata (FLA). It provides a hands-on environment for:

- **Visualizing Turing Machines** through interactive state diagrams
- **Editing machine definitions** using a synchronized code editor
- **Executing computations** with animated tape visualization and resource tracking
- **Exploring variants** including multi-tape and multi-step Turing Machines
- **Collaborating in real-time** on machine designs with other users

## Key Features by Tier

### Basic Features
- Create, read, update, and delete Turing Machines (CRUD)
- Visual state diagram editor with drag-and-drop interface
- Synchronized text-based code editor
- Alphabet configuration
- Computation visualization with tape animation
- Export capabilities (images, tables)

### Intermediate Features
- Test case management with expected outputs
- Step-through debugger for computation tracing
- Complexity plotting (time and space analysis)
- Support for multiple TM variants (standard, multi-tape, multi-step)
- Machine sharing functionality

### Advanced Features
- Nondeterministic Turing Machine (NTM) support
- Computation tree visualization and branch traversal
- Real-time collaborative editing with multiple users
- Advanced performance optimizations

## Quick Navigation

<div class="grid cards" markdown>

-   :book: **[User Guide](User%20Guide/index.md)**
    
    Learn how to use the Automata Editor effectively, from basic operations to advanced features.

-   :gear: **[Technical Architecture](Technical%20Architecture/index.md)**
    
    Understand the system design, backend and frontend components, and how they interact.

-   :api: **[API Documentation](API%20Documentation/index.md)**
    
    Reference for REST endpoints, authentication, WebSocket events, and data models.

-   :wrench: **[Development Guide](Development%20Guide/index.md)**
    
    Contribute to the project with coding standards, testing strategies, and CI/CD information.

-   :bulb: **[Examples & Tutorials](Examples%20%26%20Tutorials/index.md)**
    
    Hands-on tutorials and demonstrations from basic to advanced use cases.

-   :memo: **[Project Foundations](Project%20Foundations/git-methodology.md)**
    
    Git methodology, project structure, and technology stack documentation.

</div>

## Getting Started

New to the Automata Editor? Start with the **[Getting Started](getting-started.md)** guide for setup instructions and your first machine.

## Looking for Help?

- **Questions?** Check the [Troubleshooting & FAQ](troubleshooting.md)
- **Business terms?** See the [Glossary](glossary.md)
- **Contributing?** Review our [Contribution Guidelines](Development%20Guide/contribution-guidelines.md)

## Project Structure

The Automata Editor is built with a modern, non-monolithic architecture:

- **Frontend Application** — React + Vite with Visual and Code Editors
- **Backend API** — Express.js with hand-written HTTP endpoints
- **Database** — MongoDB for persistence
- **Documentation** — This MkDocs site, deployed on Cloudflare Pages

---

**Last Updated:** Check the repository for the latest version.  
**License:** [See project repository for details]

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
