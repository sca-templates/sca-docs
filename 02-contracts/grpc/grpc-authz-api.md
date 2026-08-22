---
title: authz gRPC API
type: contract-grpc
status: planned
repo: sca-core
tags:
  - type/contract-grpc
  - domain/auth
  - stack/multi-lang
  - exposes-grpc
---

# Authz gRPC API

> What `go-authz` exposes so every service can check a subject's effective scopes per request.

## Proto

`@sca/contracts/proto/authz.proto` (in the [[sca-contracts]] package) — the single source of truth, regenerated for TS, Python, Go and Java.

## Methods

| Method        | Request                                     | Response                        | Description                                                                                                                                                                            |
| ------------- | ------------------------------------------- | ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CheckScopes` | `CheckScopesRequest(sub, domain, scopes[])` | `CheckScopesReply(decisions[])` | Effective-scope check: realm roles + individual overrides (deny wins, expiry honored), evaluated fresh — never from JWT claims ([[adr-006-keycloak-authentication-only-and-go-authz]]) |

## Consumers

| Service                                               | Use           | Notes                                                                                                                                              |
| ----------------------------------------------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[go-authz]]                                          | Server        | exposes the API over [[grpc]]                                                                                                                      |
| [[nest-notifications]] · [[nest-logging]] · [[py-ai]] | Clients       | typed methods via `@sca/clients`; guards cache-aside in Redis, invalidated by [[evt-permissions-changed]], direct-call fallback when Redis is down |
| [[sca-clients]]                                       | `ScopesGuard` | deny-by-default                                                                                                                                    |

## Status

Planned — proto not published yet. Supersedes [[grpc-auth-api]] (`GetScopes`/`GetRoles`).
