---
title: redis
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Redis

> Redis 7 with AOF persistence and password authentication — caching and ephemeral state for the services.

## Role in the stack

- **Redis 7** (`redis:7-alpine`) with AOF persistence in a named volume (`redis_data:/data`).
- **Password auth** — the password comes from Vault locally (`secret/redis/dev`, via AppRole) and from AWS Secrets Manager in production; `make env` generates the gitignored `.env`.
- Docker-native healthcheck (`redis-cli ping`); container is marked unhealthy after 5 consecutive failures.
- Production adds `--maxmemory 256mb`, `--maxmemory-policy allkeys-lru`, `--appendfsync everysec`, after [[consul]] is up and before any service that depends on Redis.

## Access

| Endpoint | Local (development) | Notes | Production |
|---|---|---|---|
| Redis | `localhost:6379` | `redis-cli -a $REDIS_PASSWORD` | Same image; password from AWS Secrets Manager, `--maxmemory 256mb` |
| On `kafka-network` | `redis:6379` | container name resolution | Same |

Credentials source: Vault `secret/redis/dev` (dev; legacy `secret/api-template/dev` is migrated once) / AWS Secrets Manager `sca/<env>/redis_password` (prod).

## Metrics

Redis metrics are exposed via `redis-exporter` (port `:9121`). The exporter connects to `redis:6379` and exposes `redis_*` metrics.

| Metric | Type | What it measures |
|---|---|---|
| `redis_up` | Gauge | Redis status (1 = running) |
| `redis_connected_clients` | Gauge | Connected clients |
| `redis_memory_used_bytes` | Gauge | Memory usage |
| `redis_memory_max_bytes` | Gauge | Max memory configured |
| `redis_keyspace_hits_total` | Counter | Cache hits |
| `redis_keyspace_misses_total` | Counter | Cache misses |
| `redis_evicted_keys_total` | Counter | Evicted keys (memory full) |
| `redis_expired_keys_total` | Counter | Expired keys |
| `redis_commands_processed_total` | Counter | Commands processed |
| `redis_slowlog_length` | Gauge | Slow queries in slowlog |
| `redis_mem_fragmentation_ratio` | Gauge | Memory fragmentation (>1.5 = problem) |
| `redis_rdb_last_save_timestamp_seconds` | Gauge | Last RDB save |

Dashboard: [[observability#Dashboard map|redis.json]] | Alerts: `redis_down`, `redis_evictions`, `redis_hit_ratio_low`

## Observability roadmap

- [ ] Dashboard `redis.json`
- [ ] Alert rules (`redis_down`, `redis_evictions`, `redis_hit_ratio_low`)
- [ ] QA targets in `prometheus.yml`

## Target platform (operator + Sentinel)

In-cluster target ([[adr-001-kubernetes-platform]]): Redis managed by its operator behind Sentinel — automatic master failover replaces the single-instance posture. Credentials keep coming from Vault, now projected into K8s Secrets by [[external-secrets-operator]].

## Pointers

- Component README: [infra-redis](https://github.com/sca-templates/infra-redis)
- Related notes: [[self-hosted-stack]] · [[observability]] · [[service-account]]

## Status

Active — runs as part of the [[self-hosted]] stack.
