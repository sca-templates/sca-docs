---
title: conventions
type: home
status: active
repo: sca-docs
tags:
  - type/home
  - domain/contracts
---

# Conventions

> The living norm of this documentation system: how the vault is laid out, named, tagged and kept in sync.

## Principles

- **One fact, one place** — depth lives in the repos, topology/maps in the vault, pointers in READMEs. A fact is edited in exactly one file.
- **Docs-as-code** — every change lands through a pull request with review.
- **English only** — notes, commits and PR descriptions.
- **No Dataview** — catalogs are manual tables maintained by checklist.

## Vault layout

```text
00-ecosystem/        Home, super template, ecosystem overview, clone-and-start, conventions
01-services/         Service catalog (sca-* microservices)
02-contracts/        gRPC contracts (grpc/) + Kafka events (events/)
03-connections-map/  The ecosystem graph (mermaid + matrices)
04-infrastructure/   Local dev stack + target Kubernetes platform (+ multi-cloud policy)
05-packages/         @sca/* shared packages
06-decisions/        Ecosystem-level [[adr|ADRs]]
99-glossary/         Ubiquitous language (one line per term)
_templates/          Note templates
_config/             Metadata: repo locations, tagging and naming
```

## Naming and frontmatter

- Folders `NN-area/`, files kebab-case English, ADRs `adr-NNN-kebab-title.md`.
- Frontmatter is required on vault notes: `title`, `type`, `status`, `repo`, `tags`.
- Tags come from the closed taxonomy only: `type/*`, `domain/*`, `stack/*`, `connectivity/*`, `status/*`. Do not invent values; the source of truth is `_config/tagging-and-naming.md`.

## Links

- **Inside the vault** → wikilinks `[[…]]` (backlinks + graph), including [[99-glossary/INDEX|glossary]] terms.
- **Toward repos** → relative markdown links `[label](../<repo>/path.md)`, resolved per environment in `_config/repo-locations.md`.
- Never absolute paths, never copy repo content into the vault.

## Catalogs

- `01-services/INDEX.md`, `02-contracts/INDEX.md`, `04-infrastructure/INDEX.md`, `05-packages/INDEX.md`, `06-decisions/INDEX.md` are manual tables updated in the same PR that touches a note.
- `03-connections-map/connection-map.md` is regenerated whenever a contract or service note changes.

## Status

Active — this is a living document; amend it when the system changes, never the repo guides.
