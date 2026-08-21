---
title: kong
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Kong

> Kong Ingress Controller: edge routing, rate limiting and OIDC/JWT validation for every public API.

## Role in the platform

- North-south gateway at the cluster edge; complements the east-west mesh ([[linkerd]]) — Kong fronts external traffic, Linkerd secures service-to-service traffic.
- Validates Keycloak tokens at the edge ([[adr-004-keycloak-identity]]), so unauthenticated requests never reach a service.
- Plugin ecosystem for rate limiting, transformations and telemetry that feeds the observability stack.

## Deployment

- Helm install from `infra-kubernetes`; routing declared via Ingress/Gateway resources inside each service chart.
- Per-environment behavior (rate limits, exposed routes) parameterized through `envs/<env>` values.

## Pointers

- Decision: [[adr-004-keycloak-identity]]
- Related notes: [[keycloak]] · [[linkerd]] · [[platform-overview]]

## Status

Planned — roadmap step 3 (traffic & security) of the platform rollout ([[platform-overview]]).
