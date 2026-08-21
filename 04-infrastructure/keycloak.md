---
title: keycloak
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Keycloak

> Self-hosted OIDC/JWT identity provider for users and clients — portable across clouds, no managed-IdP lock-in.

## Role in the platform

- Issues tokens for end users and service clients; realms and clients are declared declaratively and versioned in `infra-kubernetes`.
- Kong validates issued tokens at the edge ([[kong]], [[adr-004-keycloak-identity]]); backends trust gateway-verified claims only.
- Identity travels with the platform: migrating clouds keeps users, clients and configuration intact.

## Access (target)

| Endpoint | Notes |
|---|---|
| `https://<host>/auth` per environment | Behind [[kong]]; admin console internal only |

Realm/client configuration source of truth: `infra-kubernetes` (declarative realm exports).

## Pointers

- Decision: [[adr-004-keycloak-identity]]
- Related notes: [[kong]] · [[platform-overview]]

## Status

Planned — roadmap step 3 (traffic & security) of the platform rollout ([[platform-overview]]).
