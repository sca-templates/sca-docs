---
title: auth gRPC API
type: contract-grpc
status: planned
repo: sca-core
tags:
  - type/contract-grpc
  - domain/auth
  - stack/multi-lang
  - exposes-grpc
---

# Auth gRPC API

> What `nest-auth` exposes so other services can resolve a subject's scopes and roles.

## Proto

`@sca/contracts/proto/auth.proto` (in the [[sca-contracts]] package) — the single source of truth, regenerated for TS, Python, Go and Java.

## Methods

| Method | Request | Response | Description |
|---|---|---|---|
| `GetScopes` | `GetScopesRequest(sub, domain)` | `ScopesReply(scopes[])` | Scopes a subject has within a domain |
| `GetRoles` | `GetRolesRequest(sub)` | `RolesReply(roles[])` | Roles a subject has |

## Consumers

| Service | Use | Notes |
|---|---|---|
| [[nest-auth]] | Server | exposes the API over [[grpc]] |
| [[nest-notifications]] · [[nest-logging]] · [[py-ai]] | Clients | typed methods via `@sca/clients` |
| [[sca-clients]] | `ScopesGuard` | deny-by-default; calls `GetScopes` with cache-aside (Redis), invalidated by [[evt-permissions-changed]] |

## Status

Planned — proto not published yet.
