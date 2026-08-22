---
title: nest-auth
type: service
status: deprecated
repo: nest-auth
tags:
  - type/service
  - domain/auth
  - stack/nestjs
---

# nest-auth

> DEPRECATED before implementation — superseded by [[keycloak]] (identity & authentication) plus [[go-authz]] (authorization & audit bridge).

## Domain

Was planned as the identity, authentication and authorization hub: subjects, scopes and roles owned here, served over [[grpc-auth-api|grpc-auth-api GetScopes/GetRoles]] with a JWT-carried safe_mode flag. The repo was never created; the design it embodied was replaced by [[adr-006-keycloak-authentication-only-and-go-authz]]. Where its responsibilities went:

- Authentication, credentials, MFA, email verification, token lifecycle, roles catalog → [[keycloak]]
- Effective-scope computation and per-request checks → [[go-authz]] via [[grpc-authz-api]]
- Account-lifecycle and permission-change events → published by [[go-authz]] ([[evt-auth-domain]], [[evt-permissions-changed]])
- Safe mode → removed entirely: untrusted sessions/devices are blocked at the edge ([[kong]]) instead of degraded

## Pointers

- Replacements: [[keycloak]] · [[go-authz]]
- Decision: [[adr-006-keycloak-authentication-only-and-go-authz]]

## Status

Deprecated — kept as a tombstone so historical references resolve.
