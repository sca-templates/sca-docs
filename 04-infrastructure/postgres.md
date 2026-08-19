---
title: postgres
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# PostgreSQL

> PostgreSQL 16 with pgvector and pg_stat_statements — the relational store and the source database for Debezium CDC.

## Role in the stack

- **PostgreSQL 16** with `pgvector` (vector embeddings / similarity search) and `pg_stat_statements` (query performance tracking).
- **`wal_level=logical`** is active — required by the Debezium connector in [[kafka]] to capture changes (the foundation of the [[outbox|outbox pattern]]).
- **pgAdmin** companion UI for browsing.
- Port is bound to `127.0.0.1` only, so remote connections are blocked at the network level.
- `make snapshot` / `make restore` for manual backups.

Known gaps (documented in the component README, deferred before production): no SSL/TLS, no scheduled backups, single instance.

## Access

| Endpoint | Local (development) | Notes | Production |
|---|---|---|---|
| PostgreSQL | `127.0.0.1:5432` | loopback only | Same image; password from AWS Secrets Manager |
| pgAdmin | `http://localhost:8080` | web UI | Internal only |

Credentials source: Vault `secret/postgres-app/dev` (`DATABASE_URL` mirrored by `postgres-app/scripts/vault-secrets.sh`); the local `.env` is the source of truth for container initialization.

## Metrics

PostgreSQL metrics are exposed via `postgres-exporter` (port `:9187`). The exporter connects to `postgres-app-db:5432` and exposes `pg_*` metrics. Requires `pg_stat_statements` extension enabled (already configured in the init scripts).

| Metric | Type | What it measures |
|---|---|---|
| `pg_up` | Gauge | Database status (1 = running) |
| `pg_stat_activity_count` | Gauge | Connections by state (active, idle, idle in transaction) |
| `pg_stat_database_xact_commit` | Counter | Committed transactions |
| `pg_stat_database_xact_rollback` | Counter | Rolled back transactions |
| `pg_stat_database_deadlocks` | Gauge | Deadlocks detected |
| `pg_stat_user_tables_seq_scan` | Gauge | Sequential scans (high = missing index) |
| `pg_stat_user_tables_n_dead_tup` | Gauge | Dead tuples (bloat indicator) |
| `pg_stat_user_indexes_idx_scan` | Gauge | Index scans (low = unused index) |
| `pg_locks_count` | Gauge | Active locks by type |
| `pg_database_size_bytes` | Gauge | Database size |

Dashboard: [[observability#Dashboard map|postgres.json]] | Alerts: `postgres_down`, `postgres_deadlocks`, `postgres_rollbacks_high`

## Observability roadmap

- [ ] Dashboard `postgres.json`
- [ ] Alert rules (`postgres_down`, `postgres_deadlocks`, `postgres_rollbacks_high`)
- [ ] QA targets in `prometheus.yml`

## Pointers

- Component README: [postgres-app](https://github.com/sca-templates/postgres-app)
- Related notes: [[self-hosted-stack]] · [[observability]] · [[kafka]] · [[outbox]]

## Status

Active — runs as part of the [[self-hosted]] stack.
