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

> Typed clients per microservice (`authz`, `notifications`, `logging`, `ai`) with **no business rules**, plus the shared auth guards and decorators.

## Content

- **Clients** — one per service: typed methods over the [[grpc]] API and [[event]]s, no business rules.
- **Shared auth** (module `auth`) — the *mechanism*: global `JwtAuthGuard` (validates the token signature only — populates `sub`, `device_id`; never trusts permission claims), `ScopesGuard` (deny-by-default; calls [[grpc-authz-api\|CheckScopes]] on every request with cache-aside in Redis invalidated by `permissions.changed`, falling back to direct gRPC when Redis is down), and decorators `@Public()`, `@Scopes(...)`, `@CurrentUser()`. There is no `@Roles(...)`: a role is a predefined bundle of [[scope|scopes]], so policy is declared with scopes only ([[adr-006-keycloak-authentication-only-and-go-authz]]).

## Dependencies

| Package             | Why                                            |
| ------------------- | ---------------------------------------------- |
| [[sca-contracts]]   | Protos and schemas for the typed clients       |
| [[sca-connections]] | gRPC factory, secrets and cache the guards use |

## Role

Inter-service calls become typed and safe by construction. The auth guard *executes* metadata that each microservice *declares*: the mechanism is shared here, the policy (`@Scopes('users:user:create')`) stays in the domain of each [[microservice]].

## Pointers

- Repo: `sca-core` (workspace), resolved in `_config/repo-locations.md`
- Consumers: [[go-authz]] · [[nest-notifications]] · [[nest-logging]] · [[py-ai]]
- Related notes: [[grpc]] · [[event]] · [[service-account]] · [[idempotency]]

## Status

Planned — package repo not created yet.
