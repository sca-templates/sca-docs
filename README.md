# sca ecosystem — documentation

The `sca` ecosystem as a whole: a repeatable way to spin up domain microservices on shared infrastructure — a local dev stack at hand, a portable Kubernetes platform as deployment target — with contracts and docs already in sync. This repository is the Obsidian vault that holds the ecosystem's **topology** (who is what, what talks to what) and **conventions**; depth lives in each repo's own docs.

## What is the sca ecosystem

- **`nest-template`** — the modular-monolith + clean-architecture skeleton every microservice is cloned from. It carries the structure, the flow framework and the docs handbook; zero business logic.
- **`@sca/*` packages** — shared plumbing with zero business logic: core, contracts (gRPC protos + event schemas), connections and clients. One fix lands in one package, not in every service.
- **`nest-*` / `py-*` microservices** — the domains (auth, notifications, logging, ai). The prefix names the framework: `nest-*` = NestJS/TypeScript, `py-*` = Python. Each is a `nest-template` clone consuming `@sca/*`, exposing its gRPC API and publishing/consuming Kafka events.
- **`infra-*`** repos — the self-hosted **local development stack**: Vault, PostgreSQL, Redis, Kafka, Consul, Prometheus, Grafana. Each repo has its own `Makefile` with `make all`; none of them is a production target.
- **Kubernetes platform** — the deployment target for every environment (`dev`, `qa`, `prod`): Linkerd, Kong, Keycloak, Vault + External Secrets, CloudNativePG + Barman, Strimzi Kafka + Debezium, Redis Sentinel, Prometheus/Grafana/Loki/Tempo, Unleash, ArgoCD, Velero — declared once in `infra-kubernetes`. See [platform overview](00-ecosystem/platform-overview.md).
- **`sca-docs`** — this vault: topology, conventions and pointers to every repo.

## Why it exists

- Clone a new service with infrastructure and contracts already in sync — the domain advances at its own pace.
- A correction is made in one place (`@sca/*` or `nest-template`) and every service inherits it.
- The outbox pattern and gRPC contracts are defined once and reused everywhere.

## Flow

### Local development

1. Start the stack: `make all` in each `infra-*` repo.
2. Clone `nest-template` and open the workspace.
3. Add the `@sca/*` packages (`pnpm add`).
4. Write the domain following the handbook's flow framework.
5. Close the docs checklist: README, `docs/`, service note in the vault, contract notes.

### Delivery (GitOps)

1. Integrate short-lived branches into `main` frequently (trunk-based development).
2. Merges trigger GitHub Actions: tests run, the image builds and publishes to GHCR.
3. The pipeline opens an image-tag PR against `infra-kubernetes`.
4. Merging lets ArgoCD sync the change into `dev`; promote to `qa` and `prod` via PRs with manual approval.
5. Ship incomplete work behind Unleash feature flags.

## Repository map

| Repo                                                              | What it is                                                                                                                                                                               | Status  |
| ----------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| `sca-docs`                                                        | This vault: topology + conventions — [README](README.md), [super template](00-ecosystem/super-template.md), [HOME](00-ecosystem/HOME.md)                                                 | active  |
| `infra-vault`                                                     | Secrets management — [README](https://github.com/sca-templates/infra-vault)                                                                                                              | active  |
| `infra-postgres-app`                                              | PostgreSQL + pgAdmin — [README](https://github.com/sca-templates/infra-postgres-app)                                                                                                     | active  |
| `infra-redis`                                                     | Redis in-memory store — [README](https://github.com/sca-templates/infra-redis)                                                                                                           | active  |
| `infra-kafka`                                                     | Kafka + Debezium + Kafka UI — [README](https://github.com/sca-templates/infra-kafka)                                                                                                     | active  |
| `infra-consul`                                                    | Service discovery + health checks — [README](https://github.com/sca-templates/infra-consul)                                                                                              | active  |
| `infra-prometheus`                                                | Metrics + exporters — [README](https://github.com/sca-templates/infra-prometheus)                                                                                                        | active  |
| `infra-grafana`                                                   | Dashboards + alerting — [README](https://github.com/sca-templates/infra-grafana)                                                                                                         | active  |
| `infra-kubernetes`                                                | Kubernetes manifests + Helm charts: GitOps source of every environment                                                                                                                   | planned |
| `nest-template`                                                   | Microservice skeleton + handbook — [README](https://github.com/sca-templates/nest-template), [handbook](https://github.com/sca-templates/nest-template/blob/main/docs/handbook/INDEX.md) | active  |
| `@sca/core`, `@sca/contracts`, `@sca/connections`, `@sca/clients` | Shared plumbing packages                                                                                                                                                                 | planned |
| `nest-auth`, `nest-notifications`, `nest-logging`, `py-ai`        | Domain microservices                                                                                                                                                                     | planned |

## Boundaries

- **Cloud-portable by design** — everything deploys on Kubernetes with open-source operators ([platform overview](00-ecosystem/platform-overview.md)); managed services are optional per-project adapters or failover ([multi-cloud](04-infrastructure/multi-cloud.md)).
- **Not a framework** — `nest-template` and `@sca/*` carry plumbing, never business logic.
- **Not a monolith** — each domain ships as its own microservice, even though each one is a modular monolith internally.
- **Not duplicated** — the vault describes topology and links; it never copies repo content.

## Documentation

The vault is built incrementally: each area gets its catalog (`INDEX.md`) as the ecosystem docs land, and `03-connections-map/connection-map.md` is regenerated whenever a contract or service changes. Start at `00-ecosystem/HOME.md`; see `00-ecosystem/conventions.md` for how to contribute notes.

## Tooling and MCP

Markdown lint, link check, editor config and the Obsidian/GitHub MCP setup are documented in [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT — see [LICENSE](LICENSE).
