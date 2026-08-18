---
title: notifications.requests.v1
type: contract-event
status: planned
repo: sca-core
tags:
  - type/contract-event
  - domain/notifications
  - publishes-events
---

# notifications.requests.v1

> A request to send a notification to a user; produced by any service, consumed by `nest-notifications`.

## Schema

Key fields: `recipient`, `channel` (email / sms), `template` + variables or inline `body`, and `correlation_id` — the [[idempotency]] key the consumer dedupes on.

## Producers

| Service | When | Notes |
|---|---|---|
| [[nest-auth]] | account lifecycle (welcome, password reset, safe-mode alert) | |
| any service | a user-facing action completes | published via the [[outbox\|outbox pattern]] |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| [[nest-notifications]] | renders and sends through the provider (MailHog local / SES prod) | dedupe by `correlation_id` |

## Status

Planned — schema not published yet.
