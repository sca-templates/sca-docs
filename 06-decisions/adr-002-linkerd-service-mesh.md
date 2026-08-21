---
title: adr-002-linkerd-service-mesh
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-002: Linkerd as the service mesh; Consul retired

- **Status:** decided
- **Date:** 2026-08-21

## Context

Services communicate over gRPC and Kafka events. East-west traffic needs encryption with workload identity, resilience policies (retries, timeouts) and latency/throughput/error telemetry per service pair. Today only the local stack resolves services via [[consul]] DNS plus TCP health checks — a local-only concern that carries no mTLS identity and no L7 telemetry, and has no production equivalent at all.

## Decision

Adopt Linkerd as the platform service mesh on Kubernetes. Every service pod gets injected proxies providing:

- automatic mutual TLS between pods, with per-workload identities;
- policy-based retries and timeouts;
- golden-signal metrics per pair of services (latency, throughput, error rate) exported to Prometheus.

Service discovery becomes native Kubernetes DNS. Consul's discovery/health-check role ends once services run inside the cluster.

## Options considered

| Option | Why not / why chosen |
|---|---|
| Istio | Broader feature set, heavier operational surface than needed |
| Consul service mesh | Keeps alive the Consul operational burden we are removing |
| Kubernetes NetworkPolicies only | No workload identity, no retries/timeouts, no traffic telemetry |

## Consequences

- Positive: encrypted, identity-verified east-west traffic by default; free network golden signals feeding [[observability]]; simple policy model; one less external system to operate ([[consul]]).
- Negative: sidecar proxy resource overhead per pod; one more control plane to upgrade alongside the platform.

## Related

- [[platform-overview]] · [[adr-001-kubernetes-platform]]
- Superseded component: [[consul]] · Telemetry consumer: [[observability]]
