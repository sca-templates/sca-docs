---
title: {{sca-service}}
type: service
status: planned
repo: {{repo}}
tags:
  - type/service
  - domain/{{domain}}
  - stack/{{stack}}
  - exposes-grpc
  - publishes-events
---

# {{sca-service}}

> {{one line — purpose}}

## Domain

{{what it is, its boundaries, what it is NOT}}

## Connections

| Kind | Contract | Role |
|---|---|---|
| gRPC | [[{{grpc-contract}}]] | {{client or server}} |
| Event | [[{{event-contract}}]] | {{publisher or consumer}} |

## Pointers

- Repo: {{logical name, resolved in _config/repo-locations.md}}
- Handbook: {{relative link `../<repo>/docs/handbook/INDEX.md`}}
- README: {{relative link `../<repo>/README.md`}}

## Status

{{planned | active | deprecated}}
