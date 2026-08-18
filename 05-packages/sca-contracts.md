---
title: sca-contracts
type: package
status: planned
repo: sca-core
tags:
  - type/package
  - domain/packages
  - stack/multi-lang
---

# @sca/contracts

> The single source of truth for inter-service contracts: `.proto` files for gRPC and Kafka [[event]]s, with multi-language codegen.

## Content

- `.proto` files defining the gRPC APIs and Kafka event schemas, language-independent ([[proto]]).
- Codegen pipeline for **TS, Python, Go and Java** — `nest-auth` (NestJS) generates its server; `nest-notifications`, `nest-logging` (NestJS) and `py-ai` (Python) generate their clients.
- Initial contracts: `auth.proto` (`GetScopes`, `GetRoles`) and the event schemas (see [[grpc-auth-api]] and the `02-contracts/` notes).

## Dependencies

| Package | Why                                 |
| ------- | ----------------------------------- |
| —       | Only build-tools; language-agnostic |

## Role

The "kept in sync" mechanism: a contract change is edited **once** here, regenerated everywhere, and a consumer that did not update breaks at compile/runtime — never silently. Contract notes in the vault link to these protos; the vault never duplicates them.

## Pointers

- Repo: `sca-core` (workspace), resolved in `_config/repo-locations.md`
- Contract notes: [[grpc-auth-api]] · [[evt-notifications-requests-v1]] · [[evt-logging-audit]] · [[evt-permissions-changed]]
- Related notes: [[grpc]] · [[proto]] · [[event]] · [[idempotency]]

## Status

Planned — package repo not created yet.
