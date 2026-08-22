---
title: auth gRPC API
type: contract-grpc
status: deprecated
repo: sca-core
tags:
  - type/contract-grpc
  - domain/auth
  - stack/multi-lang
---

# Auth gRPC API

> DEPRECATED before publication — superseded by [[grpc-authz-api]] (`CheckScopes`, served by [[go-authz]]) per [[adr-006-keycloak-authentication-only-and-go-authz]].

## Proto

Was to be `@sca/contracts/proto/auth.proto` (in the [[sca-contracts]] package). The proto was never published; the design it embodied — `GetScopes`/`GetRoles` served by a NestJS hub with JWT-carried permission flags — was replaced by per-request scope evaluation in [[go-authz]].

## Methods (historical)

| Method      | Request                         | Response                | Description                          |
| ----------- | ------------------------------- | ----------------------- | ------------------------------------ |
| `GetScopes` | `GetScopesRequest(sub, domain)` | `ScopesReply(scopes[])` | Scopes a subject has within a domain |
| `GetRoles`  | `GetRolesRequest(sub)`          | `RolesReply(roles[])`   | Roles a subject has                  |

## Pointers

- Replacement: [[grpc-authz-api]]
- Decision: [[adr-006-keycloak-authentication-only-and-go-authz]]

## Status

Deprecated — kept as a tombstone so historical references resolve.
