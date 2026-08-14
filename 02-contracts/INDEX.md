---
title: 02-contracts — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/contracts
---

# Contracts — INDEX

> The ecosystem's agreements between [[microservice|microservices]]: gRPC APIs and Kafka [[event]]s, defined once in `@sca/contracts` and materialized here.

## gRPC contracts

| Note | What it is | Status |
|---|---|---|
| [[grpc-auth-api]] | `auth.proto`: `GetScopes` / `GetRoles` | planned |

## Kafka events

| Note | What it is | Status |
|---|---|---|
| [[evt-notifications-requests-v1]] | Request to send a notification | planned |
| [[evt-logging-audit]] | Audit + technical log events | planned |
| [[evt-logging-anomaly-detected]] | Anomaly detected in the log stream | planned |
| [[evt-permissions-changed]] | A subject's scopes/roles changed | planned |
| [[evt-auth-domain]] | The `auth.*` domain family | planned |

## Keywords

contracts, grpc, proto, kafka, events, outbox, idempotency, auth, notifications, logging, permissions, audit, anomaly

## Search order

1. Read [[grpc-auth-api]] first — the only gRPC API and the base for the guards.
2. Then [[evt-permissions-changed]] (touches every service) and [[evt-auth-domain]] (the busiest family).
3. The remaining events as needed: [[evt-notifications-requests-v1]], [[evt-logging-audit]], [[evt-logging-anomaly-detected]].
