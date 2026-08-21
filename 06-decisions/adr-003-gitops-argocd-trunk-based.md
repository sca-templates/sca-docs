---
title: adr-003-gitops-argocd-trunk-based
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-003: GitOps delivery with ArgoCD and Trunk-Based Development

- **Status:** decided
- **Date:** 2026-08-21

## Context

Three environments (`dev`, `qa`, `prod`) must stay reproducible and auditable. Manual deployments drift, lack review trails and make rollback slow; long-lived release branches accumulate merge debt while teams ship small increments that must reach production safely — including unfinished work hidden behind toggles.

## Decision

Combine Trunk-Based Development with GitOps:

1. Developers integrate short-lived branches into `main` frequently — `main` is the only branch that receives pull requests.
2. A merge triggers GitHub Actions: test, build, publish the image to GHCR.
3. The pipeline opens an image-tag PR into `infra-kubernetes` (`envs/dev`) and commits the same bump straight to `envs/qa`.
4. ArgoCD watches `infra-kubernetes` — one Application per environment path (`envs/dev`, `envs/qa`, `envs/prod`) — and syncs the declared state into each cluster.
5. Only `prod` promotes through its own PR against `infra-kubernetes`, gated by manual approval; Unleash flags decouple feature release from deployment.

The Git history of `infra-kubernetes` is the deployment audit trail; rollback is a revert commit.

## Options considered

| Option                          | Why not / why chosen                                      |
| ------------------------------- | --------------------------------------------------------- |
| Push-based deploy scripts       | Clusters drift from the repo; no self-healing convergence |
| Long-lived environment branches | Merge hell; changes diverge across environments           |
| Manual helm/kubectl deploys     | No audit trail, no review gate, error-prone               |

## Consequences

- Positive: every change reviewed and traceable in Git; clusters converge to the declared state (drift corrected automatically); instant rollback by reverting a commit.
- Negative: discipline required — small batches, incomplete work behind Unleash flags; CI owns image tags, so pipeline failures block deploys.

## Related

- [[platform-overview]] · [[adr-001-kubernetes-platform]]
