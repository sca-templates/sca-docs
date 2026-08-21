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
- **`nest-*` / `py-*` microservices** — the domains: auth, notifications, logging, ai. The prefix names the framework: `nest-*` = NestJS/TypeScript, `py-*` = Python. Each is a `nest-template` clone consuming `@sca/*`, exposing its [[grpc]] API and publishing/consuming [[event]]s.
- **`infra-*` repos** — the [[self-hosted]] **local dev stack**: Vault, PostgreSQL, Redis, [[kafka]], Consul, Prometheus, Grafana. Each repo has its own `Makefile` with `make all`; none is a production target.
- **Kubernetes platform** — the deployment target for every environment ([[platform-overview]]): Linkerd, Kong, Keycloak, Vault + External Secrets, CloudNativePG + Barman, Strimzi Kafka + Debezium, Redis Sentinel, Prometheus/Grafana/Loki/Tempo, Unleash, ArgoCD, Velero — declared once in `infra-kubernetes`.
- **`sca-docs`** — this vault: the ecosystem's topology and conventions, linking to every repo instead of duplicating it.

## Why it exists

- Clone a new service with infrastructure and contracts already in sync — the domain advances at its own pace.
- A correction is made in one place (`@sca/*` or `nest-template`) and every service inherits it.
- The [[outbox|outbox pattern]] and [[grpc]] contracts are defined once and reused everywhere.

## Flow

1. Clone `nest-template` and open the workspace.
2. Add the `@sca/*` packages (`pnpm add`).
3. Start the local stack: `make all` in each `infra-*` repo.
4. Write the domain following the handbook's flow framework (BF / TF / TP).
5. Close the docs checklist: README, `docs/`, service note in the vault, contract notes.
6. Ship it: merge to `main` → GitHub Actions builds and publishes the image → image-tag PR (`dev`) + direct `qa` bump in `infra-kubernetes` → ArgoCD syncs both; only `prod` goes through a promotion PR, incomplete work behind Unleash flags.

## Repository map

| Repo                 | What it is                                    | Link                                                                                                                                                   |
| -------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `sca-docs`           | This vault: topology + conventions            | [README](../README.md)                                                                                                                                 |
| `infra-vault`        | Secrets management                            | [README](https://github.com/sca-templates/infra-vault)                                                                                                 |
| `infra-postgres-app` | PostgreSQL + pgAdmin                          | [README](https://github.com/sca-templates/infra-postgres-app)                                                                                          |
| `infra-redis`        | Redis in-memory store                         | [README](https://github.com/sca-templates/infra-redis)                                                                                                 |
| `infra-kafka`        | Kafka + Debezium + Kafka UI                   | [README](https://github.com/sca-templates/infra-kafka)                                                                                                 |
| `infra-consul`       | Service discovery + health checks             | [README](https://github.com/sca-templates/infra-consul)                                                                                                |
| `infra-prometheus`   | Metrics + exporters                           | [README](https://github.com/sca-templates/infra-prometheus)                                                                                            |
| `infra-grafana`      | Dashboards + alerting                         | [README](https://github.com/sca-templates/infra-grafana)                                                                                               |
| `infra-kubernetes`   | Kubernetes manifests + charts (GitOps source) | [planned](https://github.com/sca-templates/infra-kubernetes)                                                                                           |
| `nest-template`      | Microservice skeleton + handbook              | [README](https://github.com/sca-templates/nest-template) · [handbook](https://github.com/sca-templates/nest-template/blob/main/docs/handbook/INDEX.md) |
| `@sca/core`          | Shared core plumbing                          | [[05-packages/INDEX\|planned]]                                                                                                                         |
| `@sca/contracts`     | gRPC protos + event schemas                   | [[05-packages/INDEX\|planned]]                                                                                                                         |
| `@sca/connections`   | Connection / failover plumbing                | [[05-packages/INDEX\|planned]]                                                                                                                         |
| `@sca/clients`       | Typed clients for the contracts               | [[05-packages/INDEX\|planned]]                                                                                                                         |
| `nest-auth`          | Auth microservice                             | [[01-services/INDEX\|planned]]                                                                                                                         |
| `nest-notifications` | Notifications microservice                    | [[01-services/INDEX\|planned]]                                                                                                                         |
| `nest-logging`       | Logging microservice                          | [[01-services/INDEX\|planned]]                                                                                                                         |
| `py-ai`              | AI microservice                               | [[01-services/INDEX\|planned]]                                                                                                                         |

## Boundaries

- **Cloud-portable by design** — everything runs on the Kubernetes platform ([[platform-overview]]); managed services are optional per-project adapters or failover ([[multi-cloud]]).
- **Not a framework** — `nest-template` and `@sca/*` carry plumbing, never business logic.
- **Not a monolith** — each domain ships as its own [[microservice]], even though each one is a [[modular-monolith]] internally.
- **Not duplicated** — the vault describes topology and links; it never copies repo content.

## Related

- [[HOME]] · [[ecosystem-overview]] · [[clone-and-start]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
