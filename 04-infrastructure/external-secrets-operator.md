---
title: external-secrets-operator
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# External Secrets Operator

> Projects secrets from [[vault]] into native Kubernetes Secrets — workloads consume standard Secrets, never Vault directly.

## Role in the platform

- Watches `ExternalSecret` resources referencing Vault paths; creates and continuously syncs the corresponding K8s Secrets.
- Rotation propagates automatically: when Vault rotates a dynamic credential, the next sync refreshes the Secret ([[adr-001-kubernetes-platform]]).
- Replaces the compose-era `.env` generation scripts on the platform path; the local stack keeps its script flow ([[self-hosted-stack]]).

## Deployment

- Cluster-scoped Helm install from `infra-kubernetes`.
- Per-environment secret references live in `envs/<env>/secrets.yaml` (Vault paths, never values).

## Pointers

- Related notes: [[vault]] · [[postgres]] · [[platform-overview]]

## Status

Planned — roadmap step 1 (base infrastructure) of the platform rollout ([[platform-overview]]).
