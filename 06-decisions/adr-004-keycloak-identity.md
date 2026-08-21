---
title: adr-004-keycloak-identity
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-004: Keycloak as identity provider; Kong validates at the edge

- **Status:** decided
- **Date:** 2026-08-21

## Context

User-facing APIs need standard authentication (OIDC/JWT). Issuing and validating tokens per service fragments security logic and key rotation; managed IdPs (Cognito, Auth0) conflict with the portability and cost principles of the platform ([[adr-001-kubernetes-platform]]).

## Decision

Run Keycloak self-hosted on Kubernetes as the single OIDC/JWT identity provider for users and clients. Kong Ingress Controller validates tokens at the edge, so unauthenticated traffic never reaches a service; backends trust gateway-verified claims. Realm and client configuration stays versioned declaratively alongside the rest of `infra-kubernetes`.

## Options considered

| Option | Why not / why chosen |
|---|---|
| Cloud IdP (Cognito/Auth0) | Vendor lock-in; per-user pricing grows with scale |
| Per-service JWT libraries | Fragmented security logic; key rotation handled N times |
| API keys only | No user context, weak revocation story |

## Consequences

- Positive: portable identity across clouds; centralized realm/user management; authentication cost offloaded from services to the gateway.
- Negative: Keycloak itself must run HA and be upgraded carefully; realm configuration becomes part of platform operations.

## Related

- [[platform-overview]] · [[adr-001-kubernetes-platform]]
