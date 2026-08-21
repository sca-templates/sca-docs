---
title: unleash
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Unleash

> Self-hosted feature flags: enable or disable functionality per environment without redeploying code.

## Role in the platform

- Services integrate through Unleash SDKs (wired once in `nest-template` plumbing).
- Flags-as-code per environment: `feature-flags/dev.yaml`, `feature-flags/qa.yaml`, `feature-flags/prod.yaml` in `infra-kubernetes`.
- Decouples release from deployment ([[adr-003-gitops-argocd-trunk-based]]): incomplete work ships dark behind a flag and activates gradually during environment promotion.

## Deployment

- Helm install from `infra-kubernetes`; flag definitions applied per environment path.

## Pointers

- Decision: [[adr-003-gitops-argocd-trunk-based]]
- Related notes: [[platform-overview]]

## Status

Planned — roadmap step 5 (feature flags) of the platform rollout ([[platform-overview]]).
