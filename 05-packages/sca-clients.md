---
title: sca-clients
type: package
status: planned
repo: sca-core
tags:
  - type/package
  - domain/packages
  - stack/nestjs
---

# @sca/clients

> Typed clients per microservice (`auth`, `notifications`, `logging`, `ai`) with **no business rules**, plus the shared auth guards and decorators.

## Content

- **Clients** — one per service: typed methods over the [[grpc]] API and [[event]]s, no business rules.
- **Shared auth** (module `auth`) — the *mechanism*: global `JwtAuthGuard` (validates the token, populates `sub`, `device_id`, `safe_mode`), `ScopesGuard` (deny-by-default, resolves scopes via gRPC `GetScopes` + cache-aside in Redis, invalidated by `permissions.changed`), and decorators `@Public()`, `@Scopes(...)`, `@Roles(...)`, `@CurrentUser()`.

## Dependencies

| Package | Why |
|---|---|
| [[sca-contracts]] | Protos and schemas for the typed clients |
| [[sca-connections]] | gRPC factory, secrets and cache the guards use |

## Role

Inter-service calls become typed and safe by construction. The auth guard *executes* metadata that each microservice *declares*: the mechanism is shared here, the policy (`@Scopes('users:user:create')`) stays in the domain of each [[microservice]] — the same boundary as the handbook's identity-and-authentication.

## Pointers

- Repo: `sca-core` (workspace), resolved in `_config/repo-locations.md`
- Consumers: [[nest-auth]] · [[nest-notifications]] · [[nest-logging]] · [[py-ai]]
- Related notes: [[grpc]] · [[event]] · [[service-account]] · [[idempotency]]

## Status

Planned — package repo not created yet.
