---
title: nest-logging
type: service
status: planned
repo: nest-logging
tags:
  - type/service
  - domain/logging
  - stack/nestjs
  - publishes-events
---

# nest-logging

> Technical and operational logging plus the security/business audit trail — the ecosystem's write-once record and anomaly engine.

## Domain

Consumes the [[evt-logging-audit]] stream (two categories: technical/operational logs and security/business audit), persists it with short retention for Category A and configurable retention for Category B, and keeps the audit trail [[idempotency|consistent]] and tamper-evident via hash chaining. Exposes a query API with access control, correlates with distributed tracing, and runs the anomaly engine that publishes [[evt-logging-anomaly-detected]] — the hook into Auth's safe mode. Sanitizes PII in two layers with reversible encryption. It does NOT interpret business events; it records and surfaces them.

## Connections

| Kind | Contract | Role |
|---|---|---|
| gRPC | [[grpc-auth-api]] | client |
| Event | [[evt-logging-audit]] | consumer |
| Event | [[evt-auth-domain]] | consumer (audit trail) |
| Event | [[evt-logging-anomaly-detected]] | publisher |

## Pointers

- Repo: `nest-logging`, resolved in `_config/repo-locations.md`
- Handbook/README links are added when the repo is created
- Related notes: [[microservice]] · [[event]] · [[kafka]] · [[postgres]]

## Status

Planned — repo not created yet.
