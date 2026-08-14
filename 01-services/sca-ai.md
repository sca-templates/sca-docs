---
title: sca-ai
type: service
status: planned
repo: sca-ai
tags:
  - type/service
  - domain/ai
  - stack/python
---

# sca-ai

> AI agents — the ecosystem's analysis brain and the only service that is not NestJS.

## Domain

A Python monorepo hosting AI agents that support other services. Consumes scopes and roles via the [[grpc-auth-api]] as a client, and feeds on [[evt-auth-domain]] behavior and [[evt-logging-anomaly-detected]] signals for behavioral and anomaly analysis. It does NOT own user identity or audit records; it consumes them to produce insights.

## Connections

| Kind | Contract | Role |
|---|---|---|
| gRPC | [[grpc-auth-api]] | client |
| Event | [[evt-auth-domain]] | consumer (behavioral input) |
| Event | [[evt-logging-anomaly-detected]] | consumer (deep analysis) |

## Pointers

- Repo: `sca-ai`, resolved in `_config/repo-locations.md`
- Handbook/README links are added when the repo is created
- Related notes: [[microservice]] · [[multi-cloud]] · [[grpc]]

## Status

Planned — repo not created yet.
