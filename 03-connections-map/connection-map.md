---
title: connection-map
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/contracts
---

# Connection Map

> Derived from `01-services/` and `02-contracts/`. Regenerate with the `sync-catalogs` skill whenever a service or contract note changes.

```mermaid
graph LR
  AUTH[nest-auth]
  NOTIF[nest-notifications]
  LOGG[nest-logging]
  AI[py-ai]

  AUTH -- GetScopes/GetRoles --> API[grpc-auth-api]
  API --> NOTIF
  API --> LOGG
  API --> AI

  AUTH --> AUTH_EV[evt-auth-domain]
  AUTH --> PERM[evt-permissions-changed]
  AUTH_EV --> NOTIF
  AUTH_EV --> LOGG
  AUTH_EV --> AI

  NOTIF --> REQ[evt-notifications-requests-v1]
  LOGG --> AUD[evt-logging-audit]
  LOGG --> ANOM[evt-logging-anomaly-detected]
  ANOM --> AI
  ANOM --> NOTIF
  ANOM --> AUTH
```

## gRPC

| API | Server | Clients |
|---|---|---|
| [[grpc-auth-api]] | [[nest-auth]] | [[nest-notifications]] · [[nest-logging]] · [[py-ai]] |

## Kafka

| Event | Producers | Consumers |
|---|---|---|
| [[evt-auth-domain]] | [[nest-auth]] | [[nest-logging]] · [[nest-notifications]] · [[py-ai]] |
| [[evt-permissions-changed]] | [[nest-auth]] | every service (cache invalidation) |
| [[evt-notifications-requests-v1]] | any service | [[nest-notifications]] |
| [[evt-logging-audit]] | every service | [[nest-logging]] |
| [[evt-logging-anomaly-detected]] | [[nest-logging]] | [[py-ai]] · [[nest-notifications]] · [[nest-auth]] |

## Edge cases

- `evt-permissions-changed` and `evt-logging-audit` have "every service" in a role — the graph edges grow as services are added.
- `evt-notifications-requests-v1` producers are not fixed; the map lists the known ones in the contract note.
