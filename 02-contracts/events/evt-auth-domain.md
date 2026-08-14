---
title: auth.* domain family
type: contract-event
status: planned
repo: sca-core
tags:
  - type/contract-event
  - domain/auth
  - publishes-events
---

# auth.* domain family

> The `auth.*` family of domain events produced by `sca-auth`.

## Topics

- `auth.login.succeeded` · `auth.login.failed` — authentication outcomes.
- `auth.logout` · `auth.token.revoked` — session and token lifecycle.
- `auth.account.lifecycle` — registered, activated, locked, safe-mode entered/exited.

## Producers

| Service | When | Notes |
|---|---|---|
| [[sca-auth]] | authentication and account lifecycle events | |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| [[sca-logging]] | audit trail (feeds [[evt-logging-audit]] / anomaly analysis) | dedupe by event id |
| [[sca-notifications]] | account emails (welcome, password reset, safe-mode alert) | dedupe by correlation id |
| [[sca-ai]] | behavioral / anomaly input | dedupe by event id |

## Status

Planned — schema not published yet.
