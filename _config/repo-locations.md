# Repo locations

> Logical name to physical location, resolved per environment. The vault links
> to repos with relative markdown links; use this table to resolve them.

## Local (development machine)

| Logical name | Path | Status |
|---|---|---|
| `sca-docs` | `~/projects/aws/docs` | active |
| `aws` (infra stack orchestrator) | `~/projects/aws` | active |
| `nest-template` | `~/projects/node/nest-template` | active |
| `sca-core` | pnpm workspace hosting `@sca/core`, `@sca/contracts`, `@sca/connections`, `@sca/clients` | planned |
| `sca-auth` | — | planned |
| `sca-notifications` | — | planned |
| `sca-logging` | — | planned |
| `sca-ai` | — | planned |

## GitHub

| Repo | URL | Status |
|---|---|---|
| `sca-docs` | `https://github.com/sca-node-template/sca-docs` | active |
| `nest-template` | `https://github.com/sca-node-template/nest-template` | active |

> Planned repos get their URL when created. Keep this table in sync whenever a
> repo is created, renamed, or archived.

## Resolution by environment

Relative links from a vault note to a repo doc are environment-specific because
the repos are not siblings on every machine:

- `~/projects/aws` and `~/projects/node` are both under `~/projects`, so
  `../../node/<repo>/path.md` reaches `nest-template` from `aws/docs/…`.
- On other environments the mapping may differ; update this note instead of
  hard-coding paths in notes.
