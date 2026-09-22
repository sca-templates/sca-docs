---
title: adr-008-shared-cicd-templates-promote-pin-model
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-008: Shared CI/CD templates and the promote/pin release model

- **Status:** decided
- **Date:** 2026-09-22

## Context

[[adr-003-gitops-argocd-trunk-based]] established Trunk-Based Development with GitHub Actions, GHCR and ArgoCD as the delivery backbone, but its mechanism — an image-tag PR into `envs/dev` plus a direct `qa` bump — was only the first implementation. Two forces pushed for a change:

1. **Pipeline duplication.** Every repository defined its own workflow YAML (lint, tests, release, deploy), so a fix to shared logic had to be replicated across repos and drifted apart.
2. **Release model mismatch.** Image-tag PRs mixed "what shipped" with "where it runs", gave no immutable prod version, and let GitHub `latest` drift away from reality.

The ecosystem also grew a dedicated repository for automation primitives: [`CI-CD-Templates`](https://github.com/sca-templates/CI-CD-Templates), holding reusable workflows, composite actions and rulesets for the whole `sca-templates` organization.

## Decision

1. **Consume shared workflows.** Every repository calls the reusable workflows in `sca-templates/CI-CD-Templates` via `uses: sca-templates/CI-CD-Templates/.github/workflows/<file>@main` (sha-pinned where required), instead of defining pipeline logic locally. Local workflows exist only as thin wrappers that set inputs and permissions.
2. **Promote dev/qa by Application sync, not by PR.** `shared-service-promote.yml` syncs the service's `dev`/`qa` ArgoCD Application to a selected branch/tag/commit through the ArgoCD API. The dev sync runs immediately; a real qa promote runs against the `qa` GitHub Environment and waits for human approval (Required reviewers). There are no `deploy/*` refs.
3. **Promote prod by pin, not by bump.** An immutable signed `vX.Y.Z` tag (produced by `shared-release-flow.yml`) is pinned in `argocd/services-prod.yaml` of `infra-kubernetes` through a `chore(services)` PR opened by `shared-adopt-prod.yml` — the only PR in the model — gated by manual approval.
4. **`latest` = reality.** After a successful prod sync, `shared-enforce-latest.yml` corrects GitHub's `latest` release to the version running in prod.
5. **Two bots, two planes.** `sca-bot-release` owns releases (release PRs, signed tags); `sca-deploy-bot` owns deploys (prod pins, `latest` marker). Neither touches the cluster; dev/qa syncs run with a scoped ArgoCD API token.

ADR-003 remains the record for the GitOps + trunk-based principles; this ADR records the change of mechanism underneath those principles.

## Options considered

| Option | Why not / why chosen |
|---|---|
| Keep image-tag PRs for every env | PRs as deploy mechanism are slow and mix version with placement; no immutable prod version |
| Duplicate workflow YAML per repo | Drift and fan-out maintenance; rejected in favor of one library repo |
| Monorepo for pipelines | Org is multi-repo by design (ADR-005); a separate `CI-CD-Templates` repo keeps the library portable |
| Fully automated prod on merge | Prod must stay a human go/no-go; pin PR + manual approval keeps the audit trail |

## Consequences

- Positive: one place to fix CI/CD logic for the whole org; immutable signed prod versions; `latest` always truthful; dev/qa promotion without PR noise; clear separation of release and deploy planes.
- Negative: consumers depend on an external repo's `main` (mitigated by sha-pinning where needed); the model requires GitHub Environments, scoped ArgoCD tokens and two bot accounts to be configured; docs that describe the old image-tag flow must be rewritten (this ADR is that rewrite for the vault).

## Related

- [[adr-003-gitops-argocd-trunk-based]] · [[adr-005-per-service-repos-centralized-k8s-config]]
- [[platform-overview]] · [[argocd]]
- [`CI-CD-Templates`](https://github.com/sca-templates/CI-CD-Templates) · [service release model](https://github.com/sca-templates/CI-CD-Templates/blob/main/docs/service-release-model.md)
