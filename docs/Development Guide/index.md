# Development Guide

This section provides comprehensive guidance for developers contributing to the Automata Editor project.

## Overview

The Development Guide covers:

- Setting up your development environment
- Coding standards and conventions
- Contribution workflow and guidelines
- Testing strategies and requirements
- CI/CD pipeline configuration
- Performance optimization techniques
- Debugging and troubleshooting

## Sections

- **[Coding Standards](coding-standards.md)** — Code style, naming conventions, best practices
- **[Contribution Guidelines](contribution-guidelines.md)** — How to contribute, PR process, workflow
- **[Testing Strategy](testing-strategy.md)** — Unit tests, integration tests, E2E tests, test coverage
- **[CI/CD Pipeline](ci-cd-pipeline.md)** — Automated checks, deployment process, release management

## Getting Started

### Prerequisites

- Node.js 18+
- Git and Gitea access
- PostgreSQL or MongoDB (depending on component)
- A text editor or IDE (VS Code, WebStorm, etc.)

### Quick Start

```bash
# Clone the repository
git clone <repository-url>

# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm test
```

### Development Workflow

1. Create a working branch from the current release branch
2. Make changes following [Coding Standards](coding-standards.md)
3. Write tests for new features (see [Testing Strategy](testing-strategy.md))
4. Ensure all tests pass locally
5. Submit a pull request with a clear description
6. Address review feedback
7. Merge when approved

## Useful Links

- **Repository**: Gitea instance
- **Issue Tracker**: Gitea Projects
- **CI/CD**: Gitea Actions
- **Documentation**: This guide

---

**For detailed information, see the specific documentation pages.**

**Related**: [User Guide](../User%20Guide/index.md) | [Technical Architecture](../Technical%20Architecture/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
