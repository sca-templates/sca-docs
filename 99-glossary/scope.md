---
title: scope
type: glossary
status: active
repo: sca-docs
tags:
  - type/glossary
  - domain/auth
---

# scope

A named business permission (e.g. `users:user:create`) checked per request against an account's effective set — roles plus individual overrides, deny over grant, expiry-aware; never read from JWT claims.

## Related

- [[realm-role]]
- [[go-authz]] · [[grpc-authz-api]]
- [[adr-006-keycloak-authentication-only-and-go-authz]]
