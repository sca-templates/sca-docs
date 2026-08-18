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

> The `sca` ecosystem: microservices cloned from `nest-template`, shared `@sca/*` packages, gRPC + Kafka contracts, self-hosted infrastructure, and this vault that ties it all together.

## Layers

- **Ecosystem** → [[super-template]] · [[ecosystem-overview]] · [[clone-and-start]] · [[conventions]]
- **Microservices** → [[01-services/INDEX|Service catalog]]
- **Contracts** → [[02-contracts/INDEX|gRPC + Kafka contracts]]
- **Connections** → [[03-connections-map/INDEX|Ecosystem graph]]
- **Infrastructure** → [[04-infrastructure/INDEX|Self-hosted topology]]
- **Packages** → [[05-packages/INDEX|@sca/* shared packages]]
- **Decisions** → [[06-decisions/INDEX|ADRs]]
- **Glossary** → [[99-glossary/INDEX|Ubiquitous language]]

## How to use this vault

- Start here, then follow the graph: service → contracts → infrastructure.
- Search by tag (`search://"tag:#type/contract-grpc"`) or by [[microservice]] name.
- The vault describes topology and links to repo docs; it never duplicates them. Follow [[conventions]] when editing.

## Repo map

| Repo | What it is | Link |
|---|---|---|
| `sca-docs` | This vault | [README](../README.md) |
| `aws` | Self-hosted stack orchestrator | [README](https://github.com/sca-templates/aws) |
| `nest-template` | Microservice skeleton + handbook | [README](https://github.com/sca-templates/nest-template) · [handbook](https://github.com/sca-templates/nest-template/blob/main/docs/handbook/INDEX.md) |
| `@sca/*` | Shared plumbing packages | [[05-packages/INDEX\|planned]] |
| `sca-*` | Domain microservices | [[01-services/INDEX\|planned]] |

## Related

- [[super-template]] · [[ecosystem-overview]] · [[clone-and-start]] · [[conventions]]
- [[99-glossary/INDEX|Glossary]]
