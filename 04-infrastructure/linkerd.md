---
title: linkerd
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Linkerd

> Service mesh for the Kubernetes platform: per-pod mTLS identity, retries/timeouts policies and golden-signal network telemetry.

## Role in the platform

- Injects ultralight sidecars into every workload; each pod receives a workload identity used for **automatic mutual TLS** — east-west traffic is encrypted and authorized without application changes ([[adr-002-linkerd-service-mesh]]).
- **Policy layer** — declarative retries and timeouts per service route; authorization policies as the platform matures.
- **Telemetry** — exports latency, throughput and error rate per pair of services into [[prometheus]]; visualized in [[grafana]].
- Together with native Kubernetes DNS it replaces [[consul]] discovery and TCP health checks entirely.

## Deployment

- Cluster-scoped Helm install from `infra-kubernetes`; workloads opt in via the injection annotation.
- Upgrades ride the platform release train declared in Git and applied by ArgoCD ([[adr-003-gitops-argocd-trunk-based]]).

## Pointers

- Decision: [[adr-002-linkerd-service-mesh]]
- Related notes: [[platform-overview]] · [[prometheus]] · [[grafana]]

## Status

Planned — roadmap step 1 (base infrastructure) of the platform rollout ([[platform-overview]]).
