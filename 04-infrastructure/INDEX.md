---
title: 04-infrastructure — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/infrastructure
---

# Infrastructure — INDEX

> The [[self-hosted]] topology of the `sca` ecosystem, with each component in its own `infra-*` repo, plus the [[multi-cloud]] failover strategy.

## Notes

| Note | What it is | Status |
|---|---|---|
| [[self-hosted-stack]] | The self-hosted stack: `make all` per repo, ports, secrets flow | active |
| [[vault]] | 3-node Raft cluster, source of secrets | active |
| [[postgres]] | PostgreSQL 16 + pgvector, Debezium source | active |
| [[redis]] | Redis 7 with AOF + password auth | active |
| [[kafka]] | KRaft stack + Debezium + Kafka UI | active |
| [[consul]] | Service discovery + TCP health checks | active |
| [[prometheus]] | Central 30d TSDB + bundled exporters | active |
| [[grafana]] | Central Grafana: dashboards + alerting (Prometheus-backed) | active |
| [[observability]] | Central monitoring: Prometheus + Grafana, per-service dashboards | active |
| [[dev-tools]] | MinIO + MailHog — local only | active |
| [[multi-cloud]] | Port → managed adapter matrix (local/AWS/Azure/GCP) | active |

## Keywords

infrastructure, self-hosted, vault, postgres, redis, kafka, consul, prometheus, grafana, debezium, minio, mailhog, multi-cloud, failover, service discovery, observability, metrics, dashboards, alerting, exporters

## Search order

1. Read [[self-hosted-stack]] first — the whole stack in one view.
2. Then the component you care about: [[vault]], [[postgres]], [[redis]], [[kafka]], [[consul]], [[prometheus]], [[grafana]].
3. [[observability]] for metrics coverage, dashboard map, and alert rules.
4. [[dev-tools]] for local-only tooling, [[multi-cloud]] for the failover strategy.
