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

`aws/` is the orchestrator for the whole local stack: Vault, PostgreSQL, Redis, Kafka (+ Debezium + Kafka UI) and Consul, plus local-only dev tools. Each component lives in its own folder with its own `Makefile`; the root `Makefile` coordinates them in dependency order.

```text
make all  →  Vault → secrets (Vault KV) → kafka-network → postgres-app → redis → kafka → consul → dev (MinIO + MailHog)
```

Shared plumbing: all components join the Docker network `kafka-network` and resolve each other by container name (`kafka-broker`, `postgres-app-db`, `redis`); [[consul]] additionally resolves `<svc>.service.consul` by DNS.

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

| Component | Endpoint | Notes |
|---|---|---|
| Vault | `https://127.0.0.1:8201` (8201–8203) | 3-node cluster, AppRole |
| PostgreSQL | `127.0.0.1:5432` | bound to loopback only |
| pgAdmin | `http://localhost:8080` | companion UI |
| Redis | `localhost:6379` | password auth |
| Kafka broker | `localhost:9092` | SASL_SSL / SCRAM-SHA-512 |
| Kafka Connect | `http://localhost:8083` | Debezium |
| Kafka UI | `http://localhost:8088` | `admin` user |
| Consul | `http://127.0.0.1:8500` · DNS `127.0.0.1:8600` | discovery + health checks |
| MinIO | `http://localhost:9000` API · `9001` console | local only |
| MailHog | `localhost:1025` SMTP · `8025` UI | local only |

## Pointers

- Orchestrator README: [aws/README.md](../../README.md)
- Components: [[vault]] · [[postgres]] · [[redis]] · [[kafka]] · [[consul]] · [[dev-tools]]
- Observability and gateway (Prometheus, Grafana, Kong) live in `aws/` too and are deferred as vault notes: [prometheus.md](../../prometheus.md) · [grafana.md](../../grafana.md) · [kong.md](../../kong.md)
- Failover strategy: [[multi-cloud]]

## Status

Active — every component runs as part of the local stack.
