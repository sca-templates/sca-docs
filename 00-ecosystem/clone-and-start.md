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
make all          # from aws/ — Vault, Postgres, Redis, Kafka, Consul, dev tools
make validate     # health-check every component before going further
```

The `aws/` repo is the [[self-hosted]] stack orchestrator; see its [README](../../README.md) for the full command reference.

## Step 2 — Clone the skeleton

```sh
git clone <nest-template url> sca-<name>
cd sca-<name> && pnpm install
```

`nest-template` is the [[modular-monolith]] skeleton (see its [handbook](../../node/nest-template/docs/handbook/INDEX.md)).

## Step 3 — Add the shared plumbing

```sh
pnpm add @sca/core @sca/contracts @sca/connections @sca/clients
```

The `@sca/*` packages carry zero business logic: contracts ([[grpc]] + [[proto]] + [[event]]s), connections and clients. One fix lands here, not in every service.

## Step 4 — Write the domain

- Follow the handbook's flow framework: Business Flows (BF), Technical Flows (TF), Technical Patterns (TP) under `docs/handbook/04.1-flow-definitions/`.
- Authenticate to infrastructure with [[service-account|service accounts]]; credentials come from Vault.
- Publish events through the [[outbox|outbox pattern]] so consumers can rely on delivery.

## Step 5 — Close the docs checklist

- Repo: standard README (§3 of the plan), `docs/` with `REFERENCE`/`DOMAIN`/`ABSENT` policies, fresh `kb/` note.
- Vault: service note in `01-services/`, contract notes in `02-contracts/`, then regenerate the connection map.
- All content in English; everything lands through a PR with review.

## Related

- [[super-template]] · [[ecosystem-overview]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
