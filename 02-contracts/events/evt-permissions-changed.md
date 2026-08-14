---
title: permissions.changed
type: contract-event
status: planned
repo: sca-core
tags:
  - type/contract-event
  - domain/auth
  - publishes-events
---

# permissions.changed

> A subject's scopes or roles changed; every service invalidates its authorization cache.

## Schema

Key fields: `subject`, `domain`, `changed_scopes` / `changed_roles`, `version`, timestamp.

## Producers

| Service | When | Notes |
|---|---|---|
| [[sca-auth]] | a subject's scopes or roles change | |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| every service | invalidates the `ScopesGuard` cache-aside entry (Redis) for `subject` + `domain` | naturally idempotent (invalidate, not compute) |

## Related

- [[grpc-auth-api]] · [[sca-clients]] · [[sca-auth]]
- [[event]] · [[idempotency]] · [[outbox]]

## Status

Planned — schema not published yet.
