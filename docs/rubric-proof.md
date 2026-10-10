# Rubric Proof

Cumulative proof index for all milestone rubric criteria across Sprints 1–4. Each criterion appears once at its highest standard. The **Proof** column links to the documentation page(s) that demonstrate the requirement is met. Items marked `TODO` have no confident proof yet and are flagged for later.

---

| Criterion | Sprint(s) | Proof | Notes |
|---|---|---|---|
| Version Control | S1 | [Tech Stack §7](Project%20Foundations/tech-stack.md#7-devops-deployment) | Gitea hosts all four repositories; issue tracking and project board live there |
| Documentation Site | S1, S3 | [Home](index.md) | This site — MkDocs with Material theme, deployed to GitHub Pages, non-trivial content |
| Getting Started / Dev Guides | S1 | [Getting Started](getting-started.md), [Contribution Guidelines](Development%20Guide/contribution-guidelines.md) | User-facing quickstart and full developer contribution workflow |
| Work Tracker | S1 | [Project Methodology §Board](Project%20Foundations/project-methodology.md#7-work-tracking) | Gitea project board spanning all repos: Backlog → Ready → In Progress → In Review → Done |
| Git Methodology | S1, S4 | [Git Methodology](Project%20Foundations/git-methodology.md) | Branching model, commit conventions, PR workflow, release process |
| Project Methodology | S1, S2, S3 | [Project Methodology](Project%20Foundations/project-methodology.md) | Relaxed Scrum with motivation, ceremonies, sprint roadmap, risk register |
| Tech Stack | S1 | [Tech Stack](Project%20Foundations/tech-stack.md) | Full stack listing with motivation for every technology choice |
| Stakeholder Interaction / Reviews | S1, S2 | [Proof of Meetings](proof-of-meetings.md) | Recordings and notes from all client and team meetings across sprints |
| Initial Design & Dev Plan | S1 | [System Overview](Technical%20Architecture/system-overview.md), [Project Methodology §Roadmap](Project%20Foundations/project-methodology.md#4-sprint-roadmap) | Architecture design plus sprint-by-sprint roadmap with feature targets |
| Feature Implementation | S1, S2, S3, S4 | [Features](Features/index.md) | All features listed with tier, status, and links to dedicated pages |
| Automated Testing / Testing | S2, S3, S4 | [Testing Strategy](Development%20Guide/testing-strategy.md) | Frameworks, shipped suites, coverage expectations, Playwright e2e |
| Testing Documentation | S2 | [Testing Strategy](Development%20Guide/testing-strategy.md) | Formal process documented: unit, integration, e2e, and user feedback strategy |
| API (Availability, Design, Deployment) | S2, S3, S4 | [API Overview](API%20Documentation/index.md), [REST Endpoints](API%20Documentation/rest-endpoints.md) | Live API with full endpoint reference, HTTP methods, error codes |
| User Feedback | S2, S3 | [User Testing Feedback](user-testing-feedback.md) | Formal testing with 7 participants, demographics, themes, issue mapping |
| Bug Tracker | S2 | [Project Methodology §Board](Project%20Foundations/project-methodology.md#7-work-tracking) | Issues tracked on Gitea project board; bug labels used across repos |
| Database Documentation (Schema, Deployment, Motivation) | S2, S4 | [Data Models](API%20Documentation/data-models.md) | Full MongoDB schema, deployment info (Atlas M0), and technology-choice rationale |
| Third-Party Code Documentation | S2 | [Tech Stack](Project%20Foundations/tech-stack.md) | Every dependency listed with role and motivation columns |
| Improvement (integrating feedback) | S3 | [User Testing Feedback](user-testing-feedback.md) | Feedback themes mapped to Gitea issues showing integration into development |
| CI/CD Pipeline | S1, S4 | [CI/CD Pipeline](Development%20Guide/ci-cd-pipeline.md) | Gitea Actions CI gate + Azure deploy workflows + GitHub Pages docs deploy |
| Integration (External API) | S4 | [Tech Stack §9](Project%20Foundations/tech-stack.md#9-external-api-integration) | Auth0 as external API integration — identity service consumed by the app |
| Tools (code quality, trackers) | S4 | [Coding Standards](Development%20Guide/coding-standards.md), [Project Methodology](Project%20Foundations/project-methodology.md) | ESLint, TypeScript strict mode, Gitea board, conventional commits |
| Deployment (App + API + Docs) | S4 | [CI/CD Pipeline](Development%20Guide/ci-cd-pipeline.md), [System Overview §Deployment](Technical%20Architecture/system-overview.md#deployment-topology) | Azure App Service (frontend + backend), GitHub Pages (docs), all automated |
| Performance (App) | S3, S4 | `TODO` | No dedicated performance documentation or benchmarks in the docs site |
| Performance (API) | S4 | `TODO` | No load-testing or API performance documentation |
| Accessibility (App) | S4 | `TODO` | `ui-design.md` mentions screen-reader names and keyboard focus but no dedicated accessibility audit |
| Aesthetics (App) | S4 | `TODO` | `ui-design.md` describes layout but does not evaluate visual styling or design consistency |
| User Experience (App) | S4 | `TODO` | `ui-design.md` covers interaction design partially; no dedicated UX evaluation |
| Responsiveness (App) | S4 | `TODO` | `ui-design.md` mentions a mobile notice but no responsive breakpoint documentation |
| App Structure (navigation) | S4 | `TODO` | `ui-design.md` describes layout but does not evaluate navigation complexity |
| API Architecture (established patterns) | S4 | `TODO` | Endpoints follow REST conventions but no explicit discussion of architectural pattern adherence |
| Production Data | S4 | `TODO` | No documentation addresses production data vs. testing data |

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder-IDE [Qwen3.8-Max].
