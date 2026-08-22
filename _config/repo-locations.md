# Repo locations

> Logical name to physical location, resolved per environment. The vault links
> to repos with relative markdown links; use this table to resolve them.

## Local (development machine)

| Logical name         | Path                                                                                     | Status  |
| -------------------- | ---------------------------------------------------------------------------------------- | ------- |
| `sca-docs`           | `~/projects/aws/docs`                                                                    | active  |
| `infra-vault`        | `~/projects/infra-vault`                                                                 | active  |
| `infra-postgres-app` | `~/projects/infra-postgres-app`                                                          | active  |
| `infra-redis`        | `~/projects/infra-redis`                                                                 | active  |
| `infra-kafka`        | `~/projects/infra-kafka`                                                                 | active  |
| `infra-consul`       | `~/projects/infra-consul`                                                                | active  |
| `infra-prometheus`   | `~/projects/infra-prometheus`                                                            | active  |
| `infra-grafana`      | `~/projects/infra-grafana`                                                               | active  |
| `infra-kong`         | —                                                                                        | planned |
| `infra-loki`         | —                                                                                        | planned |
| `infra-tempo`        | —                                                                                        | planned |
| `infra-unleash`      | —                                                                                        | planned |
| `local-dev-tool`     | —                                                                                        | planned |
| `infra-kubernetes`   | `~/projects/infra-kubernetes`                                                            | planned |
| `nest-template`      | `~/projects/node/nest-template`                                                          | planned |
| `sca-core`           | pnpm workspace hosting `@sca/core`, `@sca/contracts`, `@sca/connections`, `@sca/clients` | planned |
| `nest-auth`          | —                                                                                        | deprecated |
| `go-authz`           | —                                                                                        | planned |
| `keycloak-events-listener` | —                                                                                  | planned |
| `nest-notifications` | —                                                                                        | planned |
| `nest-logging`       | —                                                                                        | planned |
| `py-ai`              | —                                                                                        | planned |

## GitHub

| Repo                 | URL                                                   | Status  |
| -------------------- | ----------------------------------------------------- | ------- |
| `sca-docs`           | `https://github.com/sca-templates/sca-docs`           | active  |
| `infra-vault`        | `https://github.com/sca-templates/infra-vault`        | active  |
| `infra-postgres-app` | `https://github.com/sca-templates/infra-postgres-app` | active  |
| `infra-redis`        | `https://github.com/sca-templates/infra-redis`        | active  |
| `infra-kafka`        | `https://github.com/sca-templates/infra-kafka`        | active  |
| `infra-consul`       | `https://github.com/sca-templates/infra-consul`       | active  |
| `infra-prometheus`   | `https://github.com/sca-templates/infra-prometheus`   | active  |
| `infra-grafana`      | `https://github.com/sca-templates/infra-grafana`      | active  |
| `infra-kong`         | `https://github.com/sca-templates/infra-kong`         | planned |
| `infra-loki`         | `https://github.com/sca-templates/infra-loki`         | planned |
| `infra-tempo`        | `https://github.com/sca-templates/infra-tempo`        | planned |
| `infra-unleash`      | `https://github.com/sca-templates/infra-unleash`      | planned |
| `local-dev-tool`     | `https://github.com/sca-templates/local-dev-tool`     | planned |
| `infra-kubernetes`   | `https://github.com/sca-templates/infra-kubernetes`   | planned |
| `nest-template`      | —                                                     | planned |
| `go-authz`           | `https://github.com/sca-templates/go-authz`           | planned |
| `keycloak-events-listener` | `https://github.com/sca-templates/keycloak-events-listener` | planned |

> Scaffold repos (`infra-kong`, `infra-loki`, `infra-tempo`, `infra-unleash`,
> `local-dev-tool`, `infra-kubernetes`) exist on GitHub with README/LICENSE only;
> they stay `planned` until their content lands. `nest-template` is pending
> publication.

## Resolution by environment

- Relative links from a vault note to a repo doc use GitHub URLs (e.g.
  `https://github.com/sca-templates/<repo>/blob/main/<path>`). The table above
  resolves logical names to URLs per environment.
