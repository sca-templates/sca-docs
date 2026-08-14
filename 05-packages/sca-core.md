---
title: sca-core
type: package
status: planned
repo: sca-core
tags:
  - type/package
  - domain/packages
  - stack/multi-lang
---

# @sca/core

> The pure foundation of the shared packages: helpers, utilities, errors, types and constants with **zero dependencies**.

## Content

- `helpers` · `utils` · `errors` · `types` · `constants` — pure code with no business logic and no runtime dependencies.
- Pure auth constants shared by the guards, e.g. `IS_PUBLIC_KEY` and the scope format (`users:user:create`).
- Deep-import subpaths (`@sca/core/errors`, `@sca/core/types`) so consumers get the pieces they need without dragging NestJS.

## Dependencies

| Package | Why |
|---|---|
| — | None; pure code, zero deps |

## Role

The root of the dependency graph: everything else builds on it. Per the ecosystem rule, it carries only plumbing and pure code — the domain of each [[microservice]] stays in its own repo.

## Pointers

- Repo: `sca-core` (workspace), resolved in `_config/repo-locations.md`
- Consumers: [[sca-contracts]] · [[sca-connections]] · [[sca-clients]]
- Related notes: [[microservice]] · [[adr]]

## Status

Planned — package repo not created yet.
