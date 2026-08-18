---
title: logging.audit
type: contract-event
status: planned
repo: sca-core
tags:
  - type/contract-event
  - domain/logging
  - publishes-events
---

# logging.audit

> The audit and technical log stream: every service emits what happened, consumed and persisted by `nest-logging`.

## Schema

Two payload variants on the same topic:

- **AuditLogEvent** — `actor`, `action`, `resource`, timestamp (who did what).
- **TechnicalLogEvent** — `service`, `level`, `trace` (request_id / correlation), timestamp.

Both correlate with the shared `request_id` traceability middleware.

## Producers

| Service | When | Notes |
|---|---|---|
| every service | on audited actions and system events | published via the [[outbox\|outbox pattern]] |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| [[nest-logging]] | persists, retention policy, query API | dedupe by event id |

## Status

Planned — schema not published yet.
