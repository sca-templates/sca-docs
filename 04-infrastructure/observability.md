---
title: observability
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Observability

> Central monitoring: Prometheus (TSDB + scrape), Grafana (dashboards + alerting), per-service exporters. One Prometheus scrapes both QA and production; dashboards filter by `environment` label.

## Role in the stack

The observability layer turns raw metrics from every self-hosted component into visual dashboards and alert rules. Prometheus is the central time-series database; Grafana is the visualization and alerting UI. Each service exposes metrics either natively (Vault, Consul) or via a sidecar exporter (Postgres, Redis, Kafka Connect).

```text
┌─────────────────────────────────────────────────────────────┐
│                    EC2 PRODUCTION                           │
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────────┐ │
│  │  Prometheus  │───▶│   Grafana   │    │  Vault          │ │
│  │  (central)   │    │  (central)  │    │  Postgres       │ │
│  └──────┬───────┘    └─────────────┘    │  Redis          │ │
│         │                                │  Kafka          │ │
│         │ scrapea locales                │  Consul         │ │
│         ▼                                └─────────────────┘ │
│  exporters locales (:9187, :9121, :9309)                    │
│                                                             │
└────────────────────────┬────────────────────────────────────┘
                         │ scrapea via red interna AWS
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    EC2 QA                                   │
│                                                             │
│  Vault, Postgres, Redis, Kafka, Consul                      │
│  exporters (:9187, :9121, :9309)                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

Each scrape target carries an `environment` label (`qa` or `prod`) so dashboards can filter or compare both environments in the same panel.

## Platform pillars (target)

The Kubernetes platform completes the four-pillar model ([[platform-overview]], [[adr-001-kubernetes-platform]]):

- **Metrics** — the map below stays; in-cluster scraping adds Linkerd network golden signals per service pair ([[linkerd]]).
- **Logs** — [[loki]] centralizes container/application logs beside metrics in Grafana.
- **Traces** — [[tempo]] stores OpenTelemetry traces correlated across services.

Log and trace coverage lands with the platform rollout; everything below describes today's metrics layer.

## Architecture

### How metrics flow

```text
1. Service exposes /metrics (native or via exporter)
2. Prometheus scrapes every 15s
3. Prometheus stores as time-series (TSDB, 30-day retention)
4. Prometheus evaluates alert rules every 15s
5. Grafana executes PromQL queries against Prometheus API
6. Grafana renders panels + fires alerts to webhook
```

### Scraping model

Prometheus runs on the prod EC2 with `network_mode: host`. It reaches:

- **Local targets** via `127.0.0.1:<port>` (published loopback ports).
- **QA targets** via the QA EC2 internal IP (e.g., `10.0.1.10:9187`).

Each target group carries an `environment` label:

```yaml
scrape_configs:
  - job_name: postgres-prod
    static_configs:
      - targets: ['127.0.0.1:9187']
        labels:
          environment: 'prod'

  - job_name: postgres-qa
    static_configs:
      - targets: ['10.0.1.10:9187']
        labels:
          environment: 'qa'
```

### Networking

Prometheus and Grafana run on the **host network** (`network_mode: host`) and bind to loopback only. This is required because UFW drops TCP from Docker bridges to host-network services. Every scrape target is a published `127.0.0.1:<port>` for local targets, or an internal AWS IP for QA targets.

## Metrics coverage

| Service | Has metrics | Method | Exporter port | Key metrics | Known gap |
|---|---|---|---|---|---|
| Vault | Yes | Native `/v1/sys/metrics` | 8201 | `vault_core_active`, `vault_secret_read_total`, `vault_audit_log_*` | — |
| PostgreSQL | Yes | `postgres-exporter` sidecar | 9187 | `pg_up`, `pg_stat_activity_count`, `pg_stat_database_xact_*`, `pg_stat_user_tables_*` | — |
| Redis | Yes | `redis-exporter` sidecar | 9121 | `redis_up`, `redis_memory_used_bytes`, `redis_keyspace_hits_total`, `redis_evicted_keys_total` | — |
| Kafka Connect | Yes | JMX exporter sidecar | 9309 | `debezium_connector_status`, `debezium_task_status` | Requires `JMXPORT=8778` on Kafka Connect |
| Consul | Partial | Native `/v1/agent/metrics` | 8500 | `up` only | Telemetry not enabled; no `consul_*` metrics |
| Prometheus | Yes | Self-scrape | 9090 | `prometheus_tsdb_head_series`, `prometheus_scrape_duration_seconds` | — |
| Grafana | No | Native `/metrics` exists but not scraped | — | — | Not configured as scrape target |
| Kafka broker | No | Commented out | 9308 | — | Exporter exists but is disabled |

## Dashboard map

All dashboards live in `grafana/dashboards/` and are provisioned by the `sca-local` provider. Each panel queries Prometheus via the `prometheus` datasource (`uid: prometheus`). Dashboards use the `environment` label to filter or compare QA vs production.

| Dashboard | File | Sections | Paneles de datos |
|---|---|---|---|
| **Stack Overview** | `stack-overview.json` | Architecture Health, Service Map, Cross-Service Metrics | ~10 |
| **Vault** | `vault.json` | Cluster Status, Request Activity, Secrets & Tokens, Audit, Resources | ~13 |
| **Postgres** | `postgres.json` | Health, Connections, Transactions, Performance, Storage, Replication | ~14 |
| **Redis** | `redis.json` | Health, Memory, Cache Performance, Commands, Persistence | ~13 |
| **Kafka** | `kafka.json` | Broker Health, Kafka Connect, Debezium CDC, Topics | ~12 |
| **Consul** | `consul.json` | Health (minimal — telemetry gap) | 1 |
| **Prometheus** | `prometheus.json` | Health, TSDB, Scrape Performance, Alerting | ~10 |

Microservice dashboards (`ms-auth`, `ms-notifications`, `ms-logging`, `ms-ai`) are populated-empty until the services run. See [[grafana]] for the full dashboard list.

### Stack Overview dashboard

The overview dashboard provides a single-pane view of the entire architecture:

- **Row 1 — Architecture Health**: One stat panel per service (`vault_core_active`, `pg_up`, `redis_up`, `up{job="kafka-connect"}`, `up{job="consul"}`, `up{job="prometheus"}`). Green = healthy, red = down.
- **Row 2 — Service Map**: Node graph panel showing services as nodes and scrape connections as edges. Node color reflects health status.
- **Row 3 — Cross-Service Metrics**: Timeseries panels comparing key metrics across services and environments (request rate, memory usage, error rate).

### Per-service dashboards

Each service dashboard drills into the metrics exposed by that service's exporter. Panels use PromQL queries that filter by `environment` label to compare QA vs production:

```promql
# Example: commit rate comparison
sum(rate(pg_stat_database_xact_commit{environment="qa"}[5m]))
sum(rate(pg_stat_database_xact_commit{environment="prod"}[5m]))
```

## Alert rules

All rules are defined in `grafana/provisioning/alerting/rules.yml` and evaluated every 1 minute. Contact point is `sca-local` (webhook to discard port — no external integration).

| Rule | Service | PromQL | Threshold | Severity |
|---|---|---|---|---|
| `infra-targets-down` | All | `up == 0` | any target down for 2m | warning |
| `vault_leader_changed` | Vault | `increase(vault_core_leader_changes_total[1h]) > 0` | any change | warning |
| `vault_request_errors` | Vault | `rate(vault_core_handle_request_count{code!="200"}[5m]) > 0.1` | > 0.1 errors/s | warning |
| `postgres_down` | Postgres | `pg_up == 0` | down | critical |
| `postgres_deadlocks` | Postgres | `sum(pg_stat_database_deadlocks) > 0` | any deadlock | warning |
| `postgres_rollbacks_high` | Postgres | `rate(pg_stat_database_xact_rollback[5m]) / rate(pg_stat_database_xact_commit[5m]) > 0.05` | > 5% rollback ratio | warning |
| `redis_down` | Redis | `redis_up == 0` | down | critical |
| `redis_evictions` | Redis | `rate(redis_evicted_keys_total[5m]) > 0` | any eviction | warning |
| `redis_hit_ratio_low` | Redis | `rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) < 0.9` | < 90% hit ratio | warning |
| `kafka_connect_down` | Kafka | `up{job="kafka-connect"} == 0` | down | critical |
| `kafka_connector_failed` | Kafka | `debezium_connector_status != 1` | connector not running | critical |
| `prometheus_scrape_slow` | Prometheus | `prometheus_scrape_duration_seconds > 10` | scrape > 10s | warning |

## Roadmap

- [ ] Dashboard `stack-overview.json`
- [ ] Dashboard `vault.json`
- [ ] Dashboard `postgres.json`
- [ ] Dashboard `redis.json`
- [ ] Dashboard `kafka.json`
- [ ] Dashboard `consul.json`
- [ ] Dashboard `prometheus.json`
- [ ] Alert rules (12 reglas)
- [ ] QA targets in `prometheus.yml`
- [ ] Enable Consul telemetry
- [ ] Enable Grafana exporter
- [ ] Instrument `nest-template` with Prometheus client
- [ ] Uncomment microservice scrape jobs

## Known gaps

1. **Consul telemetry disabled** — only `up` is available. Enabling telemetry in the Consul config will unlock `consul_*` metrics.
2. **Grafana not scraped** — Grafana exposes `/metrics` natively but is not configured as a Prometheus target.
3. **Kafka broker exporter disabled** — the JMX exporter for the broker exists but is commented out in both `compose.yml` and `prometheus.yml`.
4. **Kafka Connect JMX** — requires `JMXPORT=8778` to be set on the Kafka Connect container; without it, only `up` is available.
5. **Microservice dashboards empty** — `ms-auth`, `ms-notifications`, `ms-logging`, `ms-ai` render "No data" until the services exist and expose metrics.
6. **No SSL/TLS on Postgres** — deferred before production.

## Pointers

- Prometheus config: [prometheus.yml](../prometheus/prometheus.yml)
- Prometheus compose: [compose.yml](../prometheus/compose.yml)
- Grafana dashboards: [dashboards/](../grafana/dashboards/)
- Grafana alerting: [provisioning/alerting/](../grafana/provisioning/alerting/)
- Vault notes: [[vault]] · [[postgres]] · [[redis]] · [[kafka]] · [[consul]] · [[prometheus]] · [[grafana]]
- Component READMEs: [infra-prometheus](https://github.com/sca-templates/infra-prometheus) · [infra-grafana](https://github.com/sca-templates/infra-grafana)

## Status

Active — central observability for QA and production environments.
