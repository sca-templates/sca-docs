---
title: HOME
type: home
status: active
repo: sca-docs
tags:
  - type/home
  - domain/contracts
  - domain/infrastructure
  - domain/packages
---

# HOME

> The `sca` ecosystem: microservices cloned from `nest-template`, shared `@sca/*` packages, gRPC + Kafka contracts, a local dev stack converging on a portable Kubernetes [[platform-overview|platform]], and this vault that ties it all together.

## Layers

- **Ecosystem** → [[super-template]] · [[ecosystem-overview]] · [[platform-overview]] · [[clone-and-start]] · [[conventions]]
- **Microservices** → [[01-services/INDEX|Service catalog]]
- **Contracts** → [[02-contracts/INDEX|gRPC + Kafka contracts]]
- **Connections** → [[03-connections-map/INDEX|Ecosystem graph]]
- **Infrastructure** → [[04-infrastructure/INDEX|Local stack & Kubernetes platform]]
- **Packages** → [[05-packages/INDEX|@sca/* shared packages]]
- **Decisions** → [[06-decisions/INDEX|ADRs]]
- **Glossary** → [[99-glossary/INDEX|Ubiquitous language]]

## How to use this vault

- Start here, then follow the graph: service → contracts → infrastructure.
- Search by tag (`search://"tag:#type/contract-grpc"`) or by [[microservice]] name.
- The vault describes topology and links to repo docs; it never duplicates them. Follow [[conventions]] when editing.

## Repo map

| Repo                 | What it is                                    | Link                                                                                                                                                   |
| -------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `sca-docs`           | This vault                                    | [README](../README.md)                                                                                                                                 |
| `infra-vault`        | Secrets management                            | [README](https://github.com/sca-templates/infra-vault)                                                                                                 |
| `infra-postgres-app` | PostgreSQL + pgAdmin                          | [README](https://github.com/sca-templates/infra-postgres-app)                                                                                          |
| `infra-redis`        | Redis in-memory store                         | [README](https://github.com/sca-templates/infra-redis)                                                                                                 |
| `infra-kafka`        | Kafka + Debezium + Kafka UI                   | [[kafka\|component note]]                                                                                                                              |
| `infra-consul`       | Service discovery + health checks             | [README](https://github.com/sca-templates/infra-consul)                                                                                                |
| `infra-prometheus`   | Metrics + exporters                           | [README](https://github.com/sca-templates/infra-prometheus)                                                                                            |
| `infra-grafana`      | Dashboards + alerting                         | [README](https://github.com/sca-templates/infra-grafana)                                                                                               |
| `infra-kong`         | Edge gateway                                  | [README](https://github.com/sca-templates/infra-kong)                                                                                                  |
| `infra-loki`         | Log aggregation                               | [README](https://github.com/sca-templates/infra-loki)                                                                                                  |
| `infra-tempo`        | Distributed tracing                           | [README](https://github.com/sca-templates/infra-tempo)                                                                                                 |
| `infra-unleash`      | Feature flags                                 | [README](https://github.com/sca-templates/infra-unleash)                                                                                               |
| `local-dev-tool`     | S3 storage + SMTP capture (MinIO, MailHog)    | [README](https://github.com/sca-templates/local-dev-tool)                                                                                              |
| `infra-kubernetes`   | Kubernetes manifests + charts (GitOps source) | [README](https://github.com/sca-templates/infra-kubernetes)                                                                                            |
| `nest-template`      | Microservice skeleton + handbook              | unpublished                                                                                                                                            |
| `@sca/*`             | Shared plumbing packages                      | [[05-packages/INDEX\|planned]]                                                                                                                         |
| `sca-*`              | Domain microservices                          | [[01-services/INDEX\|planned]]                                                                                                                         |

> One repository per service — code, logic and image pipeline live with the service; every Kubernetes deployment artifact lives exclusively in `infra-kubernetes` ([[adr-005-per-service-repos-centralized-k8s-config]]). Scaffold repos hold only README/LICENSE until their content lands.

## Related

- [[super-template]] · [[ecosystem-overview]] · [[clone-and-start]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
