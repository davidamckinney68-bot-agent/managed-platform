# Architecture and component boundaries

## Purpose and scope

This project is intended for administrators managing endpoints they own or are explicitly authorized to administer. It provides visible enrollment, inventory, health and narrowly allowlisted diagnostics. It is not a general-purpose remote shell or covert monitoring tool.

## Logical architecture

```text
Administrator ── HTTPS ──> React Dashboard ── REST/WSS ──> Controller ──> PostgreSQL
                                  ▲                              ▲
                                  │                              │ HTTPS polling / authenticated WSS
                                  │                              │
Builder ── signed non-secret profile/package ──> Managed Agent ──┘
```

The Builder does not provision administrator credentials or reusable enrollment secrets into packages. Enrollment invitations are short-lived, one-time values created and distributed out of band by an authorized administrator. Per-agent credentials are issued only by the controller after policy checks and approval.

## Components

### Builder

Python desktop configuration application using PySide6. It validates controller addresses, port, organization and display metadata, platform, update channel, logging settings, and selected diagnostic modules. It emits a schema-versioned deployment profile and, in later phases, a signed package. Signing keys remain outside the application bundle and repository. Profile validation does not imply that a generated agent binary is trustworthy; package signature verification is a separate required check.

### Managed Agent

A visible .NET 10 worker installed with administrator consent. Components are separated into configuration, enrollment, authentication, transport, typed dispatch, diagnostic modules, health and audit. The agent accepts only predefined command types; it must not interpret administrator-provided strings as shell commands or load untrusted executable modules. TLS certificate validation remains enabled. Revocation and graceful shutdown are first-class states.

### Controller

Python 3.12+ and FastAPI REST/WebSocket service. PostgreSQL is the production database; SQLite is for development and isolated tests. Authentication, RBAC, enrollment, agent registry, command approval/state transitions, results, configuration, updates, health, and append-only audit are separate service boundaries. Sensitive administrative actions require authorization and audit recording.

### Dashboard

React, TypeScript, and Vite application. It is a client of controller APIs, not a trust boundary. It must not receive agent credentials, enrollment tokens, signing keys, or database secrets. WebSocket events supplement—not replace—authorized REST retrieval.

## Trust boundaries and security invariants

1. Browser-to-controller traffic is HTTPS; event traffic is WSS. Production TLS termination must be configured for TLS 1.3.
2. Agent-to-controller communication uses validated HTTPS/WSS and individual, revocable agent identity after enrollment.
3. Enrollment tokens are one-time, short-lived, stored as hashes, rate-limited, and excluded from logs.
4. Agent packages and profiles contain no reusable enrollment token, administrator secret, or private signing key.
5. Commands are typed, allowlisted, target-bound, expiring, and subject to recorded administrator approval. No arbitrary command text or shell execution is supported.
6. Revoked agents cannot authenticate or retrieve work. Duplicate/replayed enrollment and command requests are rejected.
7. Audit records are append-only at the application layer; production database permissions and backups must further limit alteration.
8. Remote support is out of scope for the initial implementation until a separately reviewed, visible, user-consented design is approved.

## Command lifecycle

The planned state machine is `PROPOSED → PENDING_APPROVAL → APPROVED → QUEUED → RUNNING → COMPLETED`, with `REJECTED`, `CANCELLED`, `FAILED`, and `EXPIRED` terminal/alternate states. Every transition is validated server-side and attributed to an actor or agent. Duplicate submission keys and expiry are enforced by the controller, not the dashboard.

Initial command identifiers are `system_info`, `process_list`, `disk_usage`, `network_interface_info`, `service_inventory`, and `agent_health`. Each refers to a structured operation, never an arbitrary executable or command line.

## Data and contracts

Versioned JSON Schemas under `schemas/` define the deployment profile, agent configuration, controller configuration, command envelope, and role names. Schemas describe data shape; authorization, expiry, uniqueness, signature verification, transactional state changes, and secret handling require runtime enforcement in later phases.

## Deployment direction

Ubuntu Server deployment will use Docker Compose with controller, dashboard, PostgreSQL, and Nginx services. PostgreSQL is internal-only and persistent. Secrets are injected through an operational secrets mechanism, not committed environment files. Nginx handles TLS and WebSocket forwarding. This document does not claim that deployment files or services exist yet.

## Phase boundaries

Phase 1 lays down contracts and manifests. Phases 2–9 implement services, typed agent behavior, UI, approval/audit, installers, and deployment. Phase 10 executes unit, integration, and deployment/security validation in an environment with the required runtimes. See [`development.md`](development.md).
