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

> A subject's scopes or roles changed; guards invalidate their Redis cache-aside entries.

## Schema

Key fields: `subject`, `domain`, `changed_scopes` / `changed_roles`, `version`, timestamp.

## Producers

| Service      | When                                                                        | Notes                                                                                        |
| ------------ | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| [[go-authz]] | a role assignment or scope override changes (deny-over-grant, expiry-aware) | source of truth for effective scopes ([[adr-006-keycloak-authentication-only-and-go-authz]]) |

## Consumers

| Service       | Use                                                                                                                                                                                                                             | Idempotency                                    |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| every service | invalidates the `ScopesGuard` cache-aside entry (Redis) for `subject` + `domain` — TTL bounds staleness if the event is missed; when Redis is unavailable, guards fall back to calling [[grpc-authz-api\|CheckScopes]] directly | naturally idempotent (invalidate, not compute) |

## Related

- [[grpc-authz-api]] · [[sca-clients]] · [[go-authz]]
- [[event]] · [[idempotency]] · [[outbox]]

## Status

Planned — schema not published yet.
