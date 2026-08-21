# Architecture

`sca-docs` is the Obsidian vault of the **sca** ecosystem: the single home for topology (who is what, what talks to what), decisions and conventions. Repo docs keep the depth; the vault links to them and never copies their content.

## Ecosystem at a glance

| Layer               | Members                                                                                                                       |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Services            | `nest-auth`, `nest-notifications`, `nest-logging`, `py-ai`                                                                    |
| Shared packages     | `@sca/*` — protos, schemas, clients, testing utilities                                                                        |
| Contracts           | Versioned gRPC APIs + Kafka topics, one note per contract                                                                     |
| Template            | `nest-template` — microservice skeleton + handbook                                                                            |
| Local dev stack     | `infra-*` repos (Vault, PostgreSQL, Redis, Kafka, Consul, Prometheus, Grafana) — Docker Compose + Makefiles, development only |
| Kubernetes platform | Everything the clusters run, declared once in `infra-kubernetes` (planned)                                                    |

## Platform

Every service ships as a container on Kubernetes across `dev`, `qa`, `prod`. Open-source operators inside the cluster keep it cloud-portable — managed services stay optional ([multi-cloud policy](../04-infrastructure/multi-cloud.md)):

- **Mesh / gateway** — Linkerd · Kong
- **Identity / secrets** — Keycloak · Vault + External Secrets Operator
- **Data / events** — PostgreSQL (CloudNativePG + Barman) · Redis (operator + Sentinel) · Kafka (Strimzi) + Debezium CDC
- **Observability** — Prometheus · Grafana · Loki · Tempo
- **Release / deploy / backup** — Unleash · ArgoCD · Velero

Full picture: [platform-overview](../00-ecosystem/platform-overview.md) · component notes under [04-infrastructure](../04-infrastructure/INDEX.md).

## Delivery

1. Trunk-Based Development: short-lived branches; `main` is the only branch that receives pull requests.
2. A merge triggers GitHub Actions: tests, image build, publish to GHCR.
3. The pipeline opens the image-tag PR into `infra-kubernetes` (`envs/dev`) and commits the same bump straight to `envs/qa`.
4. ArgoCD syncs `dev` and `qa`; only `prod` promotes through its own PR with manual approval.
5. Incomplete work ships behind Unleash feature flags.

Decision record: [ADR-003 — GitOps delivery with ArgoCD and Trunk-Based Development](../06-decisions/adr-003-gitops-argocd-trunk-based.md).

## Vault layout

```text
00-ecosystem/        Entry points: HOME, super-template, clone-and-start, conventions
01-services/         One note per microservice
02-contracts/        gRPC + event contract notes
03-connections-map/  Regenerated service/event/connection graph
04-infrastructure/   Local dev stack + target Kubernetes platform (+ multi-cloud policy)
05-packages/         @sca/* shared package notes
06-decisions/        ADRs (umbrella + focused) with INDEX
99-glossary/         Ubiquitous language, one term per note
_config/             Repo locations and other configuration
_templates/          Note scaffolds used by agents and humans
```

## Keeping it truthful

- Every vault note carries frontmatter (`title`, `type`, `status`, `repo`, `tags`) drawn from a closed tag taxonomy.
- Wikilinks (`[[…]]`) connect notes inside the vault; relative markdown links point to other repos' docs.
- One fact, one place: the vault references repo documentation instead of duplicating it.
- Catalogs stay in sync: each area's `INDEX.md` and the [connection map](../03-connections-map/connection-map.md) are updated whenever a service, contract, infrastructure or package note changes.
