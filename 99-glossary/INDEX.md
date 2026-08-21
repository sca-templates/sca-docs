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
| [[cdc]] | Streaming database changes into topics (Debezium) |
| [[clean-architecture]] | Business logic independent of frameworks |
| [[event]] | A fact published to Kafka |
| [[feature-flag]] | Runtime toggle of functionality per environment |
| [[gitops]] | Clusters synced to the state declared in Git |
| [[grpc]] | RPC framework for inter-service contracts |
| [[helm]] | Templated Kubernetes packaging per environment |
| [[idempotency]] | Repeating an event yields the same result |
| [[jwt]] | Signed token carrying verified claims |
| [[kafka]] | Distributed event-streaming platform |
| [[microservice]] | Independently deployable service owning its domain |
| [[modular-monolith]] | Single deployable, independent modules |
| [[mtls]] | Mutual TLS with per-pod workload identities |
| [[multi-cloud]] | Mapping self-hosted components to cloud adapters |
| [[oidc]] | Identity layer over OAuth 2.0 |
| [[operator]] | Kubernetes controller encoding ops knowledge |
| [[outbox]] | Reliable event publishing with the transaction |
| [[pitr]] | Restore PostgreSQL to any moment (base backup + WALs) |
| [[proto]] | Protocol Buffers contract language |
| [[self-hosted]] | Infrastructure run by the ecosystem itself |
| [[service-account]] | Non-human identity for infrastructure |
| [[service-mesh]] | mTLS, resilience and telemetry between services |
| [[sidecar]] | Helper container beside the app pod |
| [[trunk-based-development]] | Short-lived branches merged into main often |

## Keywords

ubiquitous language, glossary, grpc, proto, kafka, event, outbox, idempotency, microservice, modular-monolith, clean-architecture, self-hosted, multi-cloud, service-account, adr, gitops, argocd, trunk-based-development, helm, operator, service-mesh, mtls, sidecar, oidc, jwt, keycloak, kong, cdc, debezium, pitr, feature-flag, unleash

## Search order

1. Read [[grpc]], [[kafka]], [[outbox]] first — the three terms that appear in almost every contract note.
2. On the platform path: [[gitops]], [[service-mesh]], [[oidc]] — then follow the per-term Related links as questions arise.
