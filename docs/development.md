# Development workflow and validation

## Prerequisites

- Python 3.12 or newer.
- .NET SDK 10.0.
- Node.js 22 LTS (or a compatible supported release) and npm.
- PostgreSQL for production-like integration testing; SQLite is planned for local controller tests.
- Docker Engine and Docker Compose for later deployment phases.

## Phase 1 scope

This phase creates project layout, dependency manifests, shared JSON contracts, environment variable templates, and architecture/development documentation. It does not implement a running API, agent, GUI, dashboard, installer, database migration, Docker deployment, or command execution.

## Suggested setup after implementation phases

- Controller: create a virtual environment and install `controller/requirements.txt` or use the declared `pyproject.toml` dependencies.
- Builder: install `builder/requirements.txt`; run the package entry point after its UI is implemented.
- Dashboard: `cd dashboard && npm install`; use `npm run dev` after application source is added.
- Agent: `dotnet build agent/ManagedAgent.csproj` after source files are implemented.

These are setup directions, not assertions that commands currently succeed. Do not copy production secrets into `.env.example` or commit local `.env` files.

## Phase 1 checks to run in a development environment

1. Parse each `schemas/*.schema.json` and `schemas/shared/*.schema.json` as JSON.
2. Validate example configuration documents against their matching JSON Schema using a Draft 2020-12 validator.
3. Parse Python project manifests with the applicable Python packaging tooling.
4. Parse `dashboard/package.json` as JSON.
5. Run `dotnet build` against the agent project once implementation source is added.
6. Review dependency versions and scan packages before production use.

No checks are reported as executed in this phase because the repository-editing environment does not provide a shell or runtime test execution.

## Change process

For each feature: inspect current files, define/adjust contracts, implement a small modular change, add focused tests, execute the relevant tests, address failures, and update documentation. Preserve working code and report any validation that was not actually run.
