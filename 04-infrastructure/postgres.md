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

Known gaps (documented in the component README, deferred before production): no SSL/TLS, no scheduled backups, no monitoring dashboards, single instance.

## Access

| Endpoint | Address | Notes |
|---|---|---|
| PostgreSQL | `127.0.0.1:5432` | loopback only |
| pgAdmin | `http://localhost:8080` | web UI |

Credentials source: Vault `secret/postgres-app/dev` (`DATABASE_URL` mirrored by `postgres-app/scripts/vault-secrets.sh`); the local `.env` is the source of truth for container initialization.

## Pointers

- Component README: [aws/postgres-app/README.md](../../postgres-app/README.md)
- Related notes: [[self-hosted-stack]] · [[kafka]] · [[outbox]]

## Status

Active — runs as part of the [[self-hosted]] stack.
