---
title: adr-001-kubernetes-platform
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-001: Adopt a portable Kubernetes platform

- **Status:** decided
- **Date:** 2026-08-21

## Context

The `sca` ecosystem grew around a self-hosted Docker Compose stack ([[self-hosted-stack]]). Its Makefiles were always scoped to local development — never a production substrate — yet no production story existed beyond ad-hoc adapters. Running production on Compose-per-host gives no high availability, no self-healing and no portable multi-cloud path, while coupling operations to hand-maintained machines. Conversely, going fully managed would reintroduce vendor lock-in and direct costs the ecosystem deliberately avoids.

## Decision

Adopt Kubernetes as the only deployment substrate for all environments (`dev`, `qa`, `prod`) and all services, using open-source in-cluster operators instead of proprietary managed services:

- **Traffic & identity** — Linkerd (mesh, mTLS), Kong Ingress Controller (gateway), Keycloak (OIDC/JWT IdP).
- **Secrets** — Vault HA (Raft), synchronized into Kubernetes via External Secrets Operator.
- **Data & events** — CloudNativePG + Barman (PostgreSQL/PITR), Strimzi Kafka + Debezium (outbox CDC), Redis Operator + Sentinel.
- **Observability** — Prometheus, Grafana, Loki, Tempo, OpenTelemetry.
- **Delivery** — Helm charts applied by ArgoCD (GitOps) from the central `infra-kubernetes` repository; GitHub Actions + GHCR build images on trunk merges; Trunk-Based Development; Unleash for feature flags.
- **Resilience** — Velero for cluster and volume backups to S3-compatible storage.

The local Compose stack keeps doing what it always did: serving developers ([[clone-and-start]]). It is not a deployment target anymore.

## Options considered

| Option | Why not / why chosen |
|---|---|
| Docker Compose on VMs (status quo) | No HA/self-healing, host-coupled operations; demoted to local-dev only |
| Fully managed cloud services | Fast start, but lock-in plus direct cost; retained as optional per-project adapter ([[multi-cloud]]) |
| Serverless-first | Poor fit for long-lived gRPC/event-driven services and stateful stores |

## Consequences

- Positive: cloud portability without app rewrites; HA and automated failover by default; full GitOps audit trail; one declarative source (`infra-kubernetes`) for every environment.
- Negative: an operator fleet to install, upgrade and monitor; a real learning curve for the team; baseline control-plane resource overhead.
- Consul's discovery/health-check role is retired in favor of Kubernetes DNS + Linkerd ([[adr-002-linkerd-service-mesh]]).

## Related

- [[platform-overview]] — the resulting platform in one page.
- [[adr-002-linkerd-service-mesh]] · [[adr-003-gitops-argocd-trunk-based]] · [[adr-004-keycloak-identity]]
- [[multi-cloud]] · [[self-hosted-stack]]
