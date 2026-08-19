# Project Foundations

This section documents the foundational aspects of the Automata Editor project, including development methodology, version control practices, and technology choices.

## Overview

Project Foundations covers:

- **[Git Methodology](git-methodology.md)** — Version control strategy, branching model, commit conventions
- **[Project Methodology](project-methodology.md)** — Agile process, team structure, sprint planning
- **[Tech Stack](tech-stack.md)** — Technology choices, frameworks, libraries, deployment platforms

## Quick Reference

### Development Process

The project follows a Scrum-derived agile methodology with fixed sprints, prioritized backlogs, and regular stakeholder reviews.

### Version Control

Code is managed in Gitea with a three-level branching model:
- `main` — Deployed state
- `dev/sprint-<number>` — Release branches
- Feature/fix branches — Short-lived working branches

### Technology

The project uses a modern, non-monolithic stack:
- **Frontend**: React + Vite
- **Backend**: Node.js + Express
- **Database**: MongoDB
- **Deployment**: Azure (frontend & backend), Cloudflare Pages (docs)

---

**For detailed information, see the specific documentation pages.**

**Related**: [Development Guide](../Development%20Guide/index.md) | [API Documentation](../API%20Documentation/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
