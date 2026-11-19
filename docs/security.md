# Security boundaries and operational assumptions

- Manage only devices under explicit organizational authorization. Installation must be visible and consent/notice requirements must be met.
- The initial product scope is inventory, health, and a fixed diagnostic allowlist. It excludes arbitrary shell execution, stealth, persistence evasion, process injection, credential collection, packet capture, and endpoint-security bypass.
- Never commit secrets. `.env.example` contains placeholders. Production values must come from a secrets manager or protected deployment environment.
- Use password hashing with Argon2id for administrator passwords, short-lived sessions, role-based authorization, per-agent credentials, revocation, TLS certificate validation, rate limiting, and secure headers when runtime components are implemented.
- The controller must enforce command allowlists and approval independently of the dashboard and agent UI. Log sensitive administrative transitions without logging tokens or credentials.
- Protect signing keys separately from build outputs. Verify signatures and package hashes before installation/update. Key rotation and compromise response must be documented operationally.
- Restrict database network access to the controller and operators. Back up PostgreSQL and protect backups. Application-level append-only audit behavior is not a substitute for controlled database privileges and tamper-evident external retention.
- Remote desktop is not included in the Phase 1 contracts or implementation. Any future implementation requires a separate threat model, visible local consent, authorization, expiration, and immediate disconnect.
