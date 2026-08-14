---
title: super-template
type: home
status: active
repo: sca-docs
tags:
  - type/home
  - domain/contracts
  - domain/infrastructure
  - domain/packages
---

# Super template

> The `sca` ecosystem as a whole: the skeleton, the shared plumbing, the services, the infrastructure and this documentation — one reproducible system.

## What the ecosystem is

- **`nest-template`** — the [[modular-monolith]] + [[clean-architecture]] skeleton every [[microservice]] is cloned from. It carries the structure, the flow framework and the docs handbook; zero business logic.
- **`@sca/*` packages** — shared plumbing with zero business logic: core, contracts ([[grpc]] + [[proto]] + events), connections and clients. One fix lands in one package, not in every service.
- **`sca-*` microservices** — the domains: auth, notifications, logging, ai. Each is a `nest-template` clone consuming `@sca/*`, exposing its [[grpc]] API and publishing/consuming [[event]]s.
- **`aws/`** — the [[self-hosted]] infrastructure orchestrator: Vault, PostgreSQL, Redis, [[kafka]], Consul, plus local-only dev tools. One command (`make all`) brings the whole stack up.
- **`sca-docs`** — this vault: the ecosystem's topology and conventions, linking to every repo instead of duplicating it.

## Why it exists

- Clone a new service with infrastructure and contracts already in sync — the domain advances at its own pace.
- A correction is made in one place (`@sca/*`, `nest-template`, or `aws/`) and every service inherits it.
- The [[outbox|outbox pattern]] and [[grpc]] contracts are defined once and reused everywhere.

## Flow

1. Clone `nest-template` and open the workspace.
2. Add the `@sca/*` packages (`pnpm add`).
3. Start the stack: `make all` in `aws/`.
4. Write the domain following the handbook's flow framework (BF / TF / TP).
5. Close the docs checklist: README, `docs/`, service note in the vault, contract notes.

## Repository map

| Repo | What it is | Link |
|---|---|---|
| `sca-docs` | This vault: topology + conventions | [README](../README.md) |
| `aws` | Self-hosted stack orchestrator | [README](../../README.md) |
| `nest-template` | Microservice skeleton + handbook | [README](../../node/nest-template/README.md) · [handbook](../../node/nest-template/docs/handbook/INDEX.md) |
| `@sca/core` | Shared core plumbing | [[05-packages/INDEX\|planned]] |
| `@sca/contracts` | gRPC protos + event schemas | [[05-packages/INDEX\|planned]] |
| `@sca/connections` | Connection / failover plumbing | [[05-packages/INDEX\|planned]] |
| `@sca/clients` | Typed clients for the contracts | [[05-packages/INDEX\|planned]] |
| `sca-auth` | Auth microservice | [[01-services/INDEX\|planned]] |
| `sca-notifications` | Notifications microservice | [[01-services/INDEX\|planned]] |
| `sca-logging` | Logging microservice | [[01-services/INDEX\|planned]] |
| `sca-ai` | AI microservice | [[01-services/INDEX\|planned]] |

## Boundaries

- **Not a cloud platform** — the ecosystem is [[self-hosted]] by design; cloud adapters are a failover option ([[multi-cloud]]).
- **Not a framework** — `nest-template` and `@sca/*` carry plumbing, never business logic.
- **Not a monolith** — each domain ships as its own [[microservice]], even though each one is a [[modular-monolith]] internally.
- **Not duplicated** — the vault describes topology and links; it never copies repo content.

## Related

- [[HOME]] · [[ecosystem-overview]] · [[clone-and-start]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
