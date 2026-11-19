# Component dependency inventory (Phase 1)

Versions are declared as compatibility ranges or major-version baselines and should be pinned/locked and scanned during implementation and release.

## Controller — Python

See `controller/requirements.txt` and `controller/pyproject.toml`: FastAPI, Uvicorn, Pydantic Settings, SQLAlchemy, Alembic, PostgreSQL driver, Argon2 password hashing, and pytest/httpx test tooling.

## Builder — Python

See `builder/requirements.txt` and `builder/pyproject.toml`: PySide6, Pydantic, JSON Schema validation, pytest, and pytest-qt.

## Dashboard — TypeScript

See `dashboard/package.json`: React, React DOM, Vite, TypeScript, and React type packages. No runtime application code is included yet.

## Agent — .NET

See `agent/ManagedAgent.csproj`: .NET 10 target framework and Microsoft.Extensions.Hosting/Http packages from the .NET shared ecosystem. Additional third-party dependencies should be introduced only with a documented need.

## Supply-chain notes

A manifest is not a lockfile. Generate and review lockfiles in the corresponding toolchain, pin transitive versions for release builds, enable automated dependency updates, and run dependency/container scanning in CI before deployment.
