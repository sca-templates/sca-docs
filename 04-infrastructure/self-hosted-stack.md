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

> The `aws/` repo orchestrates every [[self-hosted]] component of the ecosystem with one command: `make all`.

## Role in the stack

`aws/` is the orchestrator for the whole local stack: Vault, PostgreSQL, Redis, Kafka (+ Debezium + Kafka UI), Consul, Prometheus and Grafana, plus local-only dev tools. Each component lives in its own folder with its own `Makefile`; the root `Makefile` coordinates them in dependency order.

```text
make all  →  Vault → secrets (Vault KV) → kafka-network → postgres-app → redis → kafka → consul → prometheus → grafana → dev (MinIO + MailHog)
```

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
| MinIO | `http://localhost:9000` API · `9001` console | local only | N/A (use AWS S3) |
| MailHog | `localhost:1025` SMTP · `8025` UI | local only | N/A (use AWS SES) |

## Pointers

- Orchestrator README: [aws](https://github.com/sca-templates/aws)
- Components: [[vault]] · [[postgres]] · [[redis]] · [[kafka]] · [[consul]] · [[prometheus]] · [[grafana]] · [[dev-tools]]
- Gateway (Kong) is deferred as a vault note: [kong](https://github.com/sca-templates/kong)
- Failover strategy: [[multi-cloud]]

## Status

Active — every component runs as part of the local stack.
