# Tagging and naming

> Metadata for the vault. One fact, one place: the closed taxonomy below is the
> only source of truth for frontmatter values and file names.

## Naming

- Folders: `NN-area/` (numeric order).
- Files: kebab-case English.
- ADRs: `adr-NNN-kebab-title.md`.
- Contracts: `grpc-<domain>.md`, `evt-<topic>.md`.
- Services: `<sca-name>.md`, with a folder `01-services/<sca-name>/INDEX.md` when the note grows.
- Glossary: direct term as the file name.

## Frontmatter

Required on vault notes:

```yaml
---
title: sca-auth
type: service            # home | moc | service | contract-grpc | contract-event | infra | package | adr | glossary
status: active           # planned | active | deprecated | decided
repo: sca-auth           # logical name, resolved in _config/repo-locations.md
tags:
  - type/service
  - domain/auth
  - stack/nestjs
  - exposes-grpc
  - publishes-events
---
```

## Tag taxonomy (closed)

| Prefix | Allowed values |
|---|---|
| `type/*` | `home`, `moc`, `service`, `contract-grpc`, `contract-event`, `infra`, `package`, `adr`, `glossary` |
| `domain/*` | `auth`, `notifications`, `logging`, `ai`, `infrastructure`, `contracts`, `packages` |
| `stack/*` | `nestjs`, `python`, `multi-lang` |
| `connectivity/*` | `exposes-grpc`, `consumes-grpc`, `publishes-events`, `consumes-events` |
| `status/*` | `planned`, `active`, `deprecated`, `decided` |

The frontmatter `type` field maps to a `type/*` tag; `status` maps to a
`status/*` tag. Do not invent values outside this taxonomy.

## Frontmatter `type` values

| Value | Note type | Template |
|---|---|---|
| `home` | Home MOC (vault root) | `_templates/template-home.md` |
| `moc` | Map of content (`INDEX.md`) | `_templates/template-moc.md` |
| `service` | Service note | `_templates/template-service.md` |
| `contract-grpc` | gRPC contract | `_templates/template-contract-grpc.md` |
| `contract-event` | Kafka event contract | `_templates/template-contract-event.md` |
| `infra` | Infrastructure note | `_templates/template-infra.md` |
| `package` | `@sca/*` package | `_templates/template-package.md` |
| `adr` | Ecosystem decision | `_templates/template-adr.md` |
| `glossary` | Term | `_templates/template-glossary.md` |
