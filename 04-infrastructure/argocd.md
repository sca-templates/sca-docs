---
title: argocd
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# ArgoCD

> GitOps engine: syncs the declared state of `infra-kubernetes` into every cluster and corrects drift.

## Role in the platform

- One Application per environment path (`envs/dev`, `envs/qa`, `envs/prod`); watches Helm charts plus per-environment values.
- Applies the declared state continuously; any manual cluster change is reconciled back to Git.
- After each merge, the pipeline opens the `dev` image-tag PR and commits the `qa` bump directly; only `prod` promotes through its own PR with manual approval ([[adr-003-gitops-argocd-trunk-based]]).
- Hand-off point with CI: GitHub Actions builds and publishes images, then opens the image-tag PR; ArgoCD owns everything after merge.

## Deployment

- Installed in-cluster from roadmap step 1 ([[platform-overview]]); Applications declared under `argocd/applications-<env>.yaml`.

## Pointers

- Decision: [[adr-003-gitops-argocd-trunk-based]]
- Related notes: [[unleash]] · [[linkerd]] · [[external-secrets-operator]] · [[platform-overview]]

## Status

Planned — roadmap steps 1 & 6 (base infrastructure, CI/CD & GitOps) of the platform rollout.
