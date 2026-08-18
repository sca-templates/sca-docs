---
title: nest-auth
type: service
status: planned
repo: nest-auth
tags:
  - type/service
  - domain/auth
  - stack/nestjs
  - exposes-grpc
  - publishes-events
---

# nest-auth

> Identity, authentication and authorization — the hub every other service leans on.

## Domain

Owns subjects, scopes and roles: who a user is, how they authenticate, and what they may do in each domain. It is the only service that mutates credentials and permission state; it is NOT a home for business domains, nor for sending notifications or logging details itself.

## Connections

| Kind | Contract | Role |
|---|---|---|
| gRPC | [[grpc-auth-api]] | server |
| Event | [[evt-auth-domain]] | publisher |
| Event | [[evt-permissions-changed]] | publisher |
| Event | [[evt-logging-anomaly-detected]] | consumer (defensive — enter safe mode) |

## Pointers

- Repo: `nest-auth`, resolved in `_config/repo-locations.md`
- Handbook/README links are added when the repo is created
- Related notes: [[microservice]] · [[service-account]] · [[sca-clients]]

## Status

Planned — repo not created yet.
