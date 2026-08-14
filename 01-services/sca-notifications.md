---
title: sca-notifications
type: service
status: planned
repo: sca-notifications
tags:
  - type/service
  - domain/notifications
  - stack/nestjs
---

# sca-notifications

> Transactional and security notifications — sends messages when other services ask, with delivery guarantees and user preferences.

## Domain

Consumes notification requests ([[evt-notifications-requests-v1]]) and delivers them through per-channel adapters (email, sms, …) with `ChannelRouter` failover. Ingests through two doors: Kafka as the main one and HTTP as the secondary (better over-deliver than miss). Handles [[idempotency]]/deduplication, templating and i18n, preferences and legal opt-out, per-channel rate limiting, and tracks delivery state in PostgreSQL with provider webhooks. It does NOT decide *whether* to notify — that stays in the producer's domain.

## Connections

| Kind | Contract | Role |
|---|---|---|
| gRPC | [[grpc-auth-api]] | client |
| Event | [[evt-notifications-requests-v1]] | consumer |
| Event | [[evt-auth-domain]] | consumer (account emails) |

## Pointers

- Repo: `sca-notifications`, resolved in `_config/repo-locations.md`
- Handbook/README links are added when the repo is created
- Related notes: [[microservice]] · [[event]] · [[idempotency]] · [[kafka]]

## Status

Planned — repo not created yet.
