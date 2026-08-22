---
title: go-authz
type: service
status: planned
repo: go-authz
tags:
  - type/service
  - domain/auth
  - stack/go
  - exposes-grpc
  - publishes-events
---

# go-authz

> Fine-grained authorization and Keycloak event ingestion — the single scope decision point every service leans on.

## Domain

Owns effective-scope calculation for accounts: [[realm-role|realm roles]] assigned in Keycloak + individual overrides (deny beats grant, expiry supported), evaluated fresh on every request and exposed via gRPC `CheckScopes` — never read from JWT claims. Receives the events its listener plugin filters from [[keycloak]] over an internal HTTP endpoint, translates them into the business-audit model, and publishes domain events. It does NOT authenticate users (that is Keycloak's job), does NOT embed authorization data in tokens, and keeps NO mirror tables of Keycloak-administered entities — it references them by external identifier/name ([[adr-006-keycloak-authentication-only-and-go-authz]]).

## Connections

| Kind  | Contract                         | Role                                                          |
| ----- | -------------------------------- | ------------------------------------------------------------- |
| gRPC  | [[grpc-authz-api]]               | server                                                        |
| Event | [[evt-permissions-changed]]      | publisher                                                     |
| Event | [[evt-auth-domain]]              | publisher                                                     |
| Event | [[evt-logging-audit]]            | publisher (translated Keycloak events + own access decisions) |
| Event | [[evt-logging-anomaly-detected]] | consumer (revoke sessions / block devices at the edge)        |

The internal HTTP endpoint for the Keycloak listener is not a vault contract; it is documented in [[keycloak]].

## Pointers

- Repo: `go-authz`, resolved in `_config/repo-locations.md`
- Handbook/README links are added when the repo is created
- Deployment: Kubernetes config lives exclusively in `infra-kubernetes`; this repo ships code and the image — [[adr-005-per-service-repos-centralized-k8s-config]]
- Related notes: [[microservice]] · [[grpc]] · [[keycloak]] · [[adr-006-keycloak-authentication-only-and-go-authz]]

## Status

Planned — repo not created yet. Implementation language Go: native gRPC, low resource footprint, minimal deploy image — built for a high volume of per-request invocations.
