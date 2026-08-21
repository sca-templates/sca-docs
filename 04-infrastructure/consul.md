---
title: consul
type: infra
status: deprecated
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Consul

> Single-node HashiCorp Consul agent providing service discovery and TCP health checks for the stack.

## Role in the stack

- **Agent/server** (`hashicorp/consul:1.19`) with `-bootstrap-expect=1`, datacenter `dev`, running with `network_mode: host` so its TCP checks reach the published ports.
- **Registers** the stack services with TCP checks (`interval: 10s`, `timeout: 5s`): `postgres-app:5432`, `redis:6379`, `kafka:9092`, `kafka-connect:8083`, `vault:8201`.
- **DNS** — any [[microservice]] resolves a component as `<name>.service.consul` through `127.0.0.1:8600`.
- Gossip key (`CONSUL_GOSSIP_KEY`) comes from Vault locally and AWS Secrets Manager in production; no ACLs in dev. Production mirrors the same image and flags.

## Access

| Endpoint | Local (development) | Notes | Production |
| --- | --- | --- | --- |
| API + UI | `http://127.0.0.1:8500` | UI under `/ui` | Same image; gossip key from AWS Secrets Manager |
| DNS | `127.0.0.1:8600` | `<svc>.service.consul` | Same |

Credentials source: `CONSUL_GOSSIP_KEY` in Vault `secret/consul/dev`.

## Metrics

Consul exposes metrics natively at `http://127.0.0.1:8500/v1/agent/metrics?format=prometheus`. However, in the current configuration, only `up` is available — Consul does not export telemetry by default.

**Known gap:** Enable `telemetry { prometheus_retention_time = "30s" }` in Consul config to get `consul_*` metrics (leader status, health check results, service count, KV operations). This is documented in [[observability#Known gaps]].

| Metric | Type | What it measures | Status |
|---|---|---|---|
| `up` | Gauge | Consul agent status | Available |
| `consul_raft_leader` | Gauge | Raft leader status | Requires telemetry config |
| `consul_health_check_status` | Gauge | Health check results | Requires telemetry config |
| `consul_catalog_services_total` | Gauge | Registered services | Requires telemetry config |

Dashboard: [[observability#Dashboard map|consul.json]] (minimal — only `up` until telemetry is enabled)

## Observability roadmap

- [ ] Enable telemetry in Consul config
- [ ] Dashboard `consul.json`
- [ ] QA targets in `prometheus.yml`

## Pointers

- Component README: [infra-consul](https://github.com/sca-templates/infra-consul)
- Related notes: [[self-hosted-stack]] · [[observability]] · [[microservice]]
- Successor on the platform: [[linkerd]] · decision: [[adr-002-linkerd-service-mesh]]

## Status

Deprecated on the platform path — superseded by Kubernetes DNS plus Linkerd mTLS identity ([[adr-002-linkerd-service-mesh]]). It still runs inside the local Compose stack ([[self-hosted-stack]]) until services migrate to Kubernetes.
