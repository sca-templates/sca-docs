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

## Pointers

- Component README: [redis](https://github.com/sca-templates/redis)
- Related notes: [[self-hosted-stack]] · [[service-account]]

## Status

Active — runs as part of the [[self-hosted]] stack.
