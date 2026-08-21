---
title: self-hosted-stack
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Self-hosted stack

> Each `infra-*` repo orchestrates its own [[self-hosted]] component with `make all`. **Local development only** — deployment targets run on the Kubernetes platform ([[platform-overview]]).

## Role in the stack

Each `infra-*` repo is self-contained: Vault, PostgreSQL, Redis, Kafka (+ Debezium + Kafka UI), Consul, Prometheus and Grafana, plus local-only dev tools. Every component lives in its own repo with its own `Makefile` (`make all` bootstraps everything).

```text
make all  →  Vault → secrets (Vault KV) → kafka-network → postgres-app → redis → kafka → consul → prometheus → grafana → dev (MinIO + MailHog)
```

[[observability]] sits across the entire stack: Prometheus scrapes every component, Grafana visualizes via per-service dashboards, and alert rules watch for failures.

Shared plumbing: all components join the Docker network `kafka-network` and resolve each other by container name (`kafka-broker`, `postgres-app-db`, `redis`); [[consul]] additionally resolves `<svc>.service.consul` by DNS. [[prometheus]] and [[grafana]] are the exception: they run on the host network (`network_mode: host`) because the UFW host firewall drops bridge → host-network traffic, and they talk to everything through published `127.0.0.1:<port>`.

### Secrets flow

1. `make setup` initializes/unseals Vault and creates the base AppRoles.
2. Per-project scripts (`vault-secrets.sh` / `gen-env.sh`) store and read credentials in Vault KV and generate a gitignored local `.env`.
3. Containers receive secrets via environment variables; they never talk to Vault at runtime.

### Key commands

| Command | Effect |
|---|---|
| `make all` / `make up` | Bootstrap + full stack (idempotent) |
| `make setup` | Bootstrap only: Vault, secrets, `.env`, network |
| `make validate` | Health-check every component |
| `make down` / `make stop` / `make restart` | Stack lifecycle |
| `make logs SVC=<name>` | Live logs for one component |
| `make clean` | Remove containers, volumes, `.env`, certs, Vault `data/` |

## Access

| Component | Local (development) | Notes | Production |
|---|---|---|---|
| Vault | `https://127.0.0.1:8201` (8201–8203) | 3-node cluster, AppRole | `awskms` seal, real CA/ACME PCA TLS |
| PostgreSQL | `127.0.0.1:5432` | bound to loopback only | Same image; password from AWS Secrets Manager |
| pgAdmin | `http://localhost:8080` | companion UI | Internal only |
| Redis | `localhost:6379` | password auth | Same image; password from AWS Secrets Manager |
| Kafka broker | `localhost:9092` | SASL_SSL / SCRAM-SHA-512 | Same image; real TLS certs |
| Kafka Connect | `http://localhost:8083` | Debezium | Same image; provisioned externally |
| Kafka UI | `http://localhost:8088` | `admin` user | Internal only |
| Consul | `http://127.0.0.1:8500` · DNS `127.0.0.1:8600` | discovery + health checks | Same image; gossip key from AWS Secrets Manager |
| Prometheus | `http://127.0.0.1:9090` | loopback only; scrapes via `127.0.0.1:<port>` | Same image |
| Grafana | `http://127.0.0.1:3000` | loopback only; user `admin` | Same image; provisioned via config management |
| Observability | Prometheus: `:9090` · Grafana: `:3000` | dashboards + alerting for all components | [[observability\|same stack]] |
| MinIO | `http://localhost:9000` API · `9001` console | local only | N/A (use AWS S3) |
| MailHog | `localhost:1025` SMTP · `8025` UI | local only | N/A (use AWS SES) |

## Pointers

- Component repos: [infra-vault](https://github.com/sca-templates/infra-vault) · [infra-postgres-app](https://github.com/sca-templates/infra-postgres-app) · [infra-redis](https://github.com/sca-templates/infra-redis) · [infra-kafka](https://github.com/sca-templates/infra-kafka) · [infra-consul](https://github.com/sca-templates/infra-consul) · [infra-prometheus](https://github.com/sca-templates/infra-prometheus) · [infra-grafana](https://github.com/sca-templates/infra-grafana)
- Components: [[vault]] · [[postgres]] · [[redis]] · [[kafka]] · [[consul]] · [[prometheus]] · [[grafana]] · [[observability]] · [[dev-tools]]
- Gateway: [[kong]] — Kong Ingress Controller lives on the Kubernetes platform
- Failover strategy: [[multi-cloud]]

## Status

Active — every component runs as part of the local stack.
