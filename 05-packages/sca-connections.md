---
title: sca-connections
type: package
status: planned
repo: sca-core
tags:
  - type/package
  - domain/packages
  - stack/nestjs
---

# @sca/connections

> Connections to the [[self-hosted]] services with **multi-cloud failover**, plus the gRPC factory — the heavy plumbing package.

## Content

- NestJS dynamic modules for each connection (`SecretsModule.forRoot({ provider })`, etc.) with health checks that report the active provider.
- gRPC factory: mTLS, retry, timeout and discovery wiring.
- The heavy dependency graph (aws-sdk, kafka, vault, pg) — isolated here so consumers of [[sca-core]] or [[sca-contracts]] alone don't drag it in.

## Dependencies

| Package | Why |
|---|---|
| [[sca-core]] | Pure types/constants |
| [[sca-contracts]] | Protos and schemas the factory and clients use |

## Role

Every connection is a **port + adapters** — business logic talks to interfaces (`SecretProvider`, `CacheProvider`, `EventBusProvider`, `StorageProvider`, `MailProvider`, `DiscoveryProvider`); the package registers the active adapter from config:

- `CONNECTION_PROVIDER=local | aws | azure | gcp` — default per environment (`dev=local`, `prod=aws`), override per abstraction (`CONNECTION_PROVIDER_STORAGE=gcp`).
- Adding a cloud = writing one new adapter, without touching microservices or contracts.
- Active failover (probe → fallback + retry) is a later evolution on the same interfaces.
- Local adapters map to the [[self-hosted-stack]] components; see [[multi-cloud]] for the full matrix.

## Pointers

- Repo: `sca-core` (workspace), resolved in `_config/repo-locations.md`
- Infra adapters: [[vault]] · [[postgres]] · [[redis]] · [[kafka]] · [[consul]] · [[dev-tools]]
- Related notes: [[multi-cloud]] · [[self-hosted]] · [[service-account]] · [[grpc]] · [[kafka]]

## Status

Planned — package repo not created yet.
