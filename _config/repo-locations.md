# Repo locations

> Logical name to physical location, resolved per environment. The vault links
> to repos with relative markdown links; use this table to resolve them.

## Local (development machine)

| Logical name | Path | Status |
|---|---|---|
| `sca-docs` | `~/projects/aws/docs` | active |
| `infra-vault` | `~/projects/infra-vault` | active |
| `infra-postgres-app` | `~/projects/infra-postgres-app` | active |
| `infra-redis` | `~/projects/infra-redis` | active |
| `infra-kafka` | `~/projects/infra-kafka` | active |
| `infra-consul` | `~/projects/infra-consul` | active |
| `infra-prometheus` | `~/projects/infra-prometheus` | active |
| `infra-grafana` | `~/projects/infra-grafana` | active |
| `nest-template` | `~/projects/node/nest-template` | active |
| `sca-core` | pnpm workspace hosting `@sca/core`, `@sca/contracts`, `@sca/connections`, `@sca/clients` | planned |
| `sca-auth` | — | planned |
| `sca-notifications` | — | planned |
| `sca-logging` | — | planned |
| `sca-ai` | — | planned |

## GitHub

| Repo | URL | Status |
|---|---|---|
| `sca-docs` | `https://github.com/sca-templates/sca-docs` | active |
| `infra-vault` | `https://github.com/sca-templates/infra-vault` | active |
| `infra-postgres-app` | `https://github.com/sca-templates/infra-postgres-app` | active |
| `infra-redis` | `https://github.com/sca-templates/infra-redis` | active |
| `infra-kafka` | `https://github.com/sca-templates/infra-kafka` | active |
| `infra-consul` | `https://github.com/sca-templates/infra-consul` | active |
| `infra-prometheus` | `https://github.com/sca-templates/infra-prometheus` | active |
| `infra-grafana` | `https://github.com/sca-templates/infra-grafana` | active |
| `nest-template` | `https://github.com/sca-templates/nest-template` | active |

> Planned repos get their URL when created. Keep this table in sync whenever a
> repo is created, renamed, or archived.

## Resolution by environment

- Relative links from a vault note to a repo doc use GitHub URLs (e.g.
  `https://github.com/sca-templates/<repo>/blob/main/<path>`). The table above
  resolves logical names to URLs per environment.
