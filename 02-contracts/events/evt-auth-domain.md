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

> The `auth.*` family of domain events, translated from Keycloak activity and own account mutations.

## Topics

- `auth.login.succeeded` · `auth.login.failed` — authentication outcomes.
- `auth.logout` · `auth.token.revoked` — session and token lifecycle.
- `auth.account.lifecycle` — registered, activated, locked.

## Producers

| Service      | When                                                                                  | Notes                                                                                         |
| ------------ | ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [[go-authz]] | translates filtered Keycloak user/admin events; publishes own account/scope mutations | listener → HTTP → translate → publish ([[adr-006-keycloak-authentication-only-and-go-authz]]) |

## Consumers

| Service                | Use                                                          | Idempotency              |
| ---------------------- | ------------------------------------------------------------ | ------------------------ |
| [[nest-logging]]       | audit trail (feeds [[evt-logging-audit]] / anomaly analysis) | dedupe by event id       |
| [[nest-notifications]] | account emails (welcome, account locked)                     | dedupe by correlation id |
| [[py-ai]]              | behavioral / anomaly input                                   | dedupe by event id       |

## Status

Planned — schema not published yet.
