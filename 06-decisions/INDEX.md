---
title: 06-decisions — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
---

# Decisions — INDEX

> Ecosystem-level [[adr|ADRs]]: architecture decisions that affect the whole `sca` stack, not a single service.

## Notes

| Note                                                  | What it is                                                                                                                                                                            | Date       | Status  |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | ------- |
| [[adr-001-kubernetes-platform]]                       | Umbrella: adopt the portable Kubernetes platform with OSS operators; Compose demoted to local dev                                                                                     | 2026-08-21 | decided |
| [[adr-002-linkerd-service-mesh]]                      | Linkerd for mTLS/retries/network telemetry; K8s DNS replaces Consul discovery                                                                                                         | 2026-08-21 | decided |
| [[adr-003-gitops-argocd-trunk-based]]                 | Trunk-Based Development + GitHub Actions/GHCR + image-tag PRs into `infra-kubernetes`, synced by ArgoCD                                                                               | 2026-08-21 | decided |
| [[adr-004-keycloak-identity]]                         | Keycloak OIDC/JWT identity provider; Kong validates tokens at the edge                                                                                                                | 2026-08-21 | decided |
| [[adr-005-per-service-repos-centralized-k8s-config]]  | One repository per service (code + image pipeline); all Kubernetes deployment config centralized in `infra-kubernetes`                                                                | 2026-08-21 | decided |
| [[adr-006-keycloak-authentication-only-and-go-authz]] | Keycloak authenticates only; fine-grained authorization (effective scopes, deny-over-grant) lives in `go-authz` — no safe mode, edge blocking instead; unified audit via nest-logging | 2026-08-21 | decided |
| [[adr-007-kong-edge-jwt-validation-and-trusted-identity-headers]] | Kong OSS validates realm JWTs (`jwt` plugin + pinned RS256 key) and forwards `X-User-Id ← sub` via post-function; only `sub` crosses the edge | 2026-08-23 | decided |
| [[adr-008-shared-cicd-templates-promote-pin-model]] | Consume reusable workflows from `CI-CD-Templates`; dev/qa promote via ArgoCD Application sync (qa approval-gated), prod promotes by pinning a signed `vX.Y.Z` tag; `latest` = prod reality | 2026-09-22 | decided |

## Keywords

decisions, adr, architecture, trade-offs, context, consequences, kubernetes, portability, operators, linkerd, mtls, consul-retirement, gitops, argocd, trunk-based-development, helm, ghcr, keycloak, oidc, jwt, kong, unleash, repository-model, one-repo-per-service, centralized-deployment, authz, go-authz, scopes, realm-role, check-scopes, audit, edge-validation, trusted-identity-headers, x-user-id, cicd-templates, reusable-workflows, promote-pin-model, service-promote, adopt-prod, enforce-latest, release-flow

## Search order

1. Start with [[adr-001-kubernetes-platform]] for the umbrella decision; the focused ADRs refine its slices.
2. Scan this table for the topic you need — each ADR is a self-contained context → decision → consequences file.
3. Cross-reference [[platform-overview]] and [[99-glossary/INDEX|glossary]] terms referenced inside each ADR.
