---
title: 03-connections-map — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/contracts
---

# Connections Map — INDEX

> The graph of how [[microservice|microservices]] talk to each other: [[grpc]] APIs and Kafka [[event]]s, derived from the service and contract notes.

## Map

[[connection-map]]

## How to read it

1. Nodes are services (from `01-services/`) and contracts (from `02-contracts/`).
2. gRPC edges = client/server; Kafka edges = publisher/consumer.
3. Keep this map in sync with `01-services/` and `02-contracts/` (see the `sync-catalogs` skill).
