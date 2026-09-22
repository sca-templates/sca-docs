---
title: argocd
type: infra
status: active
repo: aws
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
- Dev and qa Applications point at `main` and are synced on demand by `shared-service-promote.yml` via the ArgoCD API (no PR; a qa promote waits for human approval on the `qa` GitHub Environment).
- Prod Application reads its immutable signed `vX.Y.Z` tag from `argocd/services-prod.yaml` (the prod version registry), updated only through a `chore(services)` PR with manual approval; `shared-enforce-latest.yml` keeps GitHub `latest` equal to the running version ([[adr-003-gitops-argocd-trunk-based]], [[adr-008-shared-cicd-templates-promote-pin-model]]).
- Hand-off point with CI: GitHub Actions (shared workflows from `CI-CD-Templates`) builds and publishes images and calls the ArgoCD API for dev/qa; ArgoCD owns everything after the Application state changes in Git.

## Deployment

- Installed in-cluster from roadmap step 1 ([[platform-overview]]); Applications declared under `argocd/applications-<env>.yaml`; prod pins under `argocd/services-prod.yaml`.

## Pointers

- Decision: [[adr-003-gitops-argocd-trunk-based]] · [[adr-008-shared-cicd-templates-promote-pin-model]]
- Related notes: [[unleash]] · [[linkerd]] · [[external-secrets-operator]] · [[platform-overview]]

## Status

Planned — roadmap steps 1 & 6 (base infrastructure, CI/CD & GitOps) of the platform rollout.
