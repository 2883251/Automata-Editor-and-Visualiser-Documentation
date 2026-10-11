# Automata Editor Documentation

This repository contains the official documentation for the Automata Editor project, built with MkDocs and the Material theme.

## Quick Start

### Prerequisites

- Python 3.12 (the version CI builds with)
- pip

### Installation

```bash
# Install dependencies
pip install -r requirements.txt
```

### Building the Documentation

```bash
# Serve documentation locally (http://localhost:8000)
mkdocs serve

# Build static site, failing on broken links (what CI runs)
mkdocs build --strict
```

The built site will be in the `site/` directory.

## Project Structure

```
.
├── mkdocs.yml           # MkDocs configuration
├── requirements.txt     # Python dependencies
├── docs/
│   ├── index.md        # Homepage
│   ├── getting-started.md
│   ├── glossary.md
│   ├── ui-design.md
│   ├── performance.md
│   ├── proof-of-meetings.md
│   ├── user-testing-feedback.md
│   ├── rubric-proof.md
│   ├── Features/
│   ├── Technical Architecture/
│   ├── API Documentation/
│   ├── Development Guide/
│   ├── Project Foundations/
│   └── assets/
└── site/               # Build output (generated)
```

## Documentation Sections

- **[Getting Started](docs/getting-started.md)** — Open the app and build a first machine
- **[Features](docs/Features/index.md)** — What the editor can do
- **[Technical Architecture](docs/Technical%20Architecture/index.md)** — System design and component architecture
- **[API Documentation](docs/API%20Documentation/index.md)** — REST API and WebSocket reference
- **[Development Guide](docs/Development%20Guide/index.md)** — Contributing and development setup
- **[Project Foundations](docs/Project%20Foundations/index.md)** — Git methodology, project structure, and tech stack
- **[UI Design](docs/ui-design.md)** — Layout, accessibility, visual design, responsiveness and navigation
- **[Performance](docs/performance.md)** — Measured performance
- **[Proof of Meetings](docs/proof-of-meetings.md)** — Meeting evidence log
- **[User Testing](docs/user-testing-feedback.md)** — Feedback from user testing and what was done with it
- **[Rubric Proof](docs/rubric-proof.md)** — Where each rubric criterion is evidenced

## Deployment

The documentation is automatically built and deployed to **GitHub Pages** on every push to the main branch. Gitea mirrors the repository to GitHub, where a GitHub Actions workflow builds and publishes it. A Gitea Actions workflow also runs the strict build on every push and pull request.

## Contributing

To contribute to the documentation:

1. Clone this repository
2. Create a feature branch
3. Make your changes to the relevant `.md` files
4. Test locally with `mkdocs serve`
5. Submit a pull request

Documentation contributions follow the same workflow as code contributions. See the [Contribution Guidelines](docs/Development%20Guide/contribution-guidelines.md) for details.

## Building and Deployment

### Local Build

```bash
mkdocs build
```

### Deployment to GitHub Pages

Push changes to the main branch. The CI/CD pipeline automatically builds and deploys the documentation.

## License

Documentation is part of the Automata Editor project. See the main repository for license information.

## Contact

For questions about the documentation, please refer to the project's issue tracker or communication channels.

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].