# Managed Endpoint Administration Platform

A modular, explicitly installed endpoint administration platform for authorized devices. The repository is organized as Builder → Managed Agent → Controller, with a separate administrator dashboard.

> **Status:** Phase 1 foundation (schemas, manifests, repository layout, and architecture documentation). Runtime services and deployment are not implemented yet.

## Repository map

See [`docs/architecture.md`](docs/architecture.md) for component boundaries and trust flows, and [`docs/development.md`](docs/development.md) for the phase plan and local setup prerequisites.

- `builder/` — Python/PySide6 configuration tooling foundation.
- `agent/` — .NET 10 managed agent foundation.
- `controller/` — Python/FastAPI controller foundation.
- `dashboard/` — React/TypeScript/Vite dashboard foundation.
- `schemas/` — versioned JSON contracts shared by components.
- `docs/` — architecture, security boundaries, and development notes.
- `installers/`, `deployment/`, `scripts/` — reserved for subsequent implementation phases.

## Configuration and secrets

Start from `.env.example` for local environment variable names. It contains placeholders only and must not be used as a production secret source. Keep private keys, passwords, enrollment tokens, and agent credentials outside version control. Deployment profiles are configuration artifacts; they must never contain reusable enrollment secrets or administrator credentials.

## Validation status

Phase 1 is limited to repository foundations. No build or test result is claimed: this work was authored through GitHub repository file operations, which do not provide a shell to execute validators. Run the checks listed in [`docs/development.md`](docs/development.md) in a development environment before relying on these files.
