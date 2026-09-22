---
title: clone-and-start
type: home
status: active
repo: sca-docs
tags:
  - type/home
  - domain/contracts
  - domain/infrastructure
  - domain/packages
---

# Clone and start

> The flow to bring up a new `sca-*` [[microservice]]: infrastructure first, then clone, plumbing, domain and docs.

## Prerequisites

- `pnpm` (see `packageManager` in `nest-template`), Docker ≥ 24, Docker Compose v2, `make`, `curl`.

## Step 1 — Start the infrastructure

```sh
# Start each infrastructure repo (Vault, Postgres, Redis, Kafka, Consul, etc.)
cd infra-vault && make all && make validate
cd ../infra-postgres-app && make all && make validate
cd ../infra-redis && make all && make validate
cd ../infra-kafka && make all && make validate
cd ../infra-consul && make all && make validate
```

Each `infra-*` repo is self-contained with its own `Makefile`; see each repo's README for the full command reference.

## Step 2 — Clone the skeleton

```sh
git clone <nest-template url> sca-<name>
cd sca-<name> && pnpm install
```

`nest-template` is the [[modular-monolith]] skeleton; its handbook lives under `docs/handbook/` in the repository.

## Step 3 — Add the shared plumbing

```sh
pnpm add @sca/core @sca/contracts @sca/connections @sca/clients
```

The `@sca/*` packages carry zero business logic: contracts ([[grpc]] + [[proto]] + [[event]]s), connections and clients. One fix lands here, not in every service.

## Step 4 — Write the domain

- Follow the handbook's flow framework: Business Flows (BF), Technical Flows (TF), Technical Patterns (TP) under `docs/handbook/04.1-flow-definitions/`.
- Authenticate to infrastructure with [[service-account|service accounts]]; credentials come from Vault.
- Publish events through the [[outbox|outbox pattern]] so consumers can rely on delivery.
- Authenticate users via Keycloak OIDC/JWT; Kong validates tokens at the edge (platform rollout).
- Gate incomplete functionality behind Unleash [[feature-flag|feature flags]] per environment.

## Step 5 — Close the docs checklist

- Repo: standard README (§3 of the plan), `docs/` with `REFERENCE`/`DOMAIN`/`ABSENT` policies, fresh `kb/` note.
- Vault: service note in `01-services/`, contract notes in `02-contracts/`, then regenerate the connection map.
- All content in English; everything lands through a PR with review.

## Step 6 — Ship it

1. Integrate short-lived branches into `main` frequently ([[trunk-based-development]]).
2. The merge triggers GitHub Actions (shared workflows from `CI-CD-Templates`): tests, image build, publish to GHCR as `sha-<commit>`.
3. `shared-service-promote.yml` syncs the service's ArgoCD Applications for `dev` and `qa` via the ArgoCD API (no PR; a qa promote waits for human approval on the `qa` GitHub Environment).
4. Only `prod` moves through a pin: a signed `vX.Y.Z` tag is pinned in `infra-kubernetes` (`argocd/services-prod.yaml`) via a `chore(services)` PR — manual approval gates it ([[adr-003-gitops-argocd-trunk-based]], [[adr-008-shared-cicd-templates-promote-pin-model]]).
5. The service repo never carries Kubernetes manifests: every deployment or promotion change lands exclusively in `infra-kubernetes` ([[adr-005-per-service-repos-centralized-k8s-config]]).

## Related

- [[super-template]] · [[ecosystem-overview]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
