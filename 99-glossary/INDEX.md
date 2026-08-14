---
title: 99-glossary — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/contracts
---

# Glossary — INDEX

> Ubiquitous language of the `sca` ecosystem: one line per term, in English.

## Notes

| Note | What it is |
|---|---|
| [[adr]] | Architecture Decision Record |
| [[clean-architecture]] | Business logic independent of frameworks |
| [[event]] | A fact published to Kafka |
| [[grpc]] | RPC framework for inter-service contracts |
| [[idempotency]] | Repeating an event yields the same result |
| [[kafka]] | Distributed event-streaming platform |
| [[microservice]] | Independently deployable service owning its domain |
| [[modular-monolith]] | Single deployable, independent modules |
| [[multi-cloud]] | Mapping self-hosted components to cloud adapters |
| [[outbox]] | Reliable event publishing with the transaction |
| [[proto]] | Protocol Buffers contract language |
| [[self-hosted]] | Infrastructure run by the ecosystem itself |
| [[service-account]] | Non-human identity for infrastructure |

## Keywords

ubiquitous language, glossary, grpc, proto, kafka, event, outbox, idempotency, microservice, modular-monolith, clean-architecture, self-hosted, multi-cloud, service-account, adr

## Search order

1. Read [[grpc]], [[kafka]], [[outbox]] first — the three terms that appear in almost every contract note.
2. Then follow the per-term Related links as questions arise.
