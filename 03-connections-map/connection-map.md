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
  AUTH[sca-auth]
  NOTIF[sca-notifications]
  LOGG[sca-logging]
  AI[sca-ai]

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
| [[grpc-auth-api]] | [[sca-auth]] | [[sca-notifications]] · [[sca-logging]] · [[sca-ai]] |

## Kafka

| Event | Producers | Consumers |
|---|---|---|
| [[evt-auth-domain]] | [[sca-auth]] | [[sca-logging]] · [[sca-notifications]] · [[sca-ai]] |
| [[evt-permissions-changed]] | [[sca-auth]] | every service (cache invalidation) |
| [[evt-notifications-requests-v1]] | any service | [[sca-notifications]] |
| [[evt-logging-audit]] | every service | [[sca-logging]] |
| [[evt-logging-anomaly-detected]] | [[sca-logging]] | [[sca-ai]] · [[sca-notifications]] · [[sca-auth]] |

## Edge cases

- `evt-permissions-changed` and `evt-logging-audit` have "every service" in a role — the graph edges grow as services are added.
- `evt-notifications-requests-v1` producers are not fixed; the map lists the known ones in the contract note.
