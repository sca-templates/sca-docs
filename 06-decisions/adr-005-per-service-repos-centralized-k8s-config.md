---
title: adr-005-per-service-repos-centralized-k8s-config
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/infrastructure
---

# ADR-005: One repository per service, Kubernetes config centralized in `infra-kubernetes`

- **Status:** decided
- **Date:** 2026-08-21

## Context

Every [[microservice]] needs a home for its code and configuration, while the platform needs one auditable place declaring what runs on Kubernetes ([[adr-001-kubernetes-platform]], [[adr-003-gitops-argocd-trunk-based]]). Letting each service repository carry its own manifests would scatter deployment truth across repos, duplicate chart plumbing and split the GitOps audit trail ArgoCD relies on; a single monorepo would couple services that must evolve independently.

## Decision

Each service lives in **its own repository** under the GitHub organization:

- The service repo owns the **code, business logic and application configuration**, plus the Dockerfile and the GitHub Actions pipeline that tests, builds and publishes its image to GHCR.
- **All Kubernetes deployment configuration lives exclusively in `infra-kubernetes`**: Helm charts, per-environment values (`envs/dev`, `envs/qa`, `envs/prod`), ArgoCD Applications and Vault secret references.
- Service repositories contain no Kubernetes manifests — deploying or promoting a service is always a change in `infra-kubernetes`.

The service repo produces the image; where and how it runs is declared in `infra-kubernetes`.

## Options considered

| Option | Why not / why chosen |
|---|---|
| Manifests co-located in each service repo | Splits the GitOps audit trail across repos; duplicates chart logic; couples deploys to app-repo permissions |
| Monorepo for services + platform | One checkout, but couples independent delivery cadences and widens the review surface of every change |
| One repo per service + central `infra-kubernetes` (chosen) | Clean ownership per service; one declarative, reviewed home for everything the clusters run |

## Consequences

- Positive: services stay independently versioned and permissioned; one GitOps source of truth ([[gitops]]) with rollback as a revert commit; platform changes roll out to every service without touching their repos.
- Negative: adding a service touches two places (image pipeline in its repo, deployment declaration in `infra-kubernetes`); contributors must know which concern lives where; image tags flow only through the automated PR ([[adr-003-gitops-argocd-trunk-based]]).

## Related

- [[platform-overview]] — repository model and `infra-kubernetes` layout.
- [[adr-001-kubernetes-platform]] · [[adr-003-gitops-argocd-trunk-based]]
- [[ecosystem-overview]] · [[clone-and-start]]
