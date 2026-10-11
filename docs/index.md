# Automata Editor Documentation

Welcome to the comprehensive documentation for the **Automata Editor** — an interactive, web-based tool for learning and visualizing Turing Machines through synchronized visual and code editors.

**Use it now:** <https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net>

## What is the Automata Editor?

The Automata Editor is an educational platform designed for computer science students and educators working in Formal Languages and Automata (FLA). It provides a hands-on environment for:

- **Visualizing Turing Machines** through interactive state diagrams
- **Editing machine definitions** using a synchronized code editor
- **Executing computations** with animated tape visualization and resource tracking
- **Exploring variants** including multi-tape, multi-step and nondeterministic Turing Machines
- **Collaborating in real-time** on machine designs with other users

## Key Features by Tier

### Basic Features
- Create, read, update, and delete Turing Machines (CRUD)
- Visual state diagram editor: add states, draw transitions, drag to arrange
- Synchronized text-based code editor
- Alphabet configuration
- Computation visualization with tape animation
- Export capabilities (images, tables)

### Intermediate Features
- Test case management with expected outputs
- Step-through debugger for computation tracing
- Complexity plotting (time and space analysis)
- Support for multiple TM variants (standard, multi-tape, multi-step, stationary moves)
- Machine sharing functionality

### Advanced Features
- Nondeterministic Turing Machine (NTM) support
- Computation tree visualization and branch traversal
- Real-time collaborative editing with multiple users
- API keys and a marking endpoint for scripts and marking tools

## Quick Navigation

<div class="grid cards" markdown>

-   🚀 **[Features](Features/index.md)**
    
    What the editor can do today: simulation, machine management, test cases, machine variants, nondeterminism, export, and sharing.

-   🎨 **[UI Design](ui-design.md)**
    
    How the editor is laid out: the two editors, the simulation, the side bar, the status bar, and the phone layout.

-   ⚙️ **[Technical Architecture](Technical%20Architecture/index.md)**
    
    Understand the system design, backend and frontend components, and how they interact.

-   🔗 **[API Documentation](API%20Documentation/index.md)**
    
    How to mark machines from a script with an API key, and the reference for REST endpoints, authentication, WebSocket events, and data models.

-   🔧 **[Development Guide](Development%20Guide/index.md)**
    
    Contribute to the project with coding standards, testing strategies, and CI/CD information.

-   📝 **[Project Foundations](Project%20Foundations/git-methodology.md)**
    
    Git methodology, project structure, and technology stack documentation.

</div>

## Getting Started

New to the Automata Editor? Start with the **[Getting Started](getting-started.md)** guide for setup instructions and your first machine.

## Looking for Help?

- **Business terms?** See the [Glossary](glossary.md)
- **Contributing?** Review our [Contribution Guidelines](Development%20Guide/contribution-guidelines.md)

## Project Structure

The Automata Editor is built with a modern, non-monolithic architecture:

- **Frontend Application** — React + Vite with Visual and Code Editors
- **Shared Core Package** — `@brh/automata-core`: the machine model, parser, simulator, and test cases, published to the Gitea npm registry
- **Backend API** — Express.js with hand-written HTTP endpoints and a WebSocket for simultaneous editing
- **Database** — MongoDB for persistence
- **Documentation** — This MkDocs site, deployed on GitHub Pages

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
