---
title: vault
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Vault

> HashiCorp Vault 3-node Raft cluster: the source of secrets for every other component, exposed to [[service-account|service accounts]] via AppRole.

## Role in the stack

A self-contained Vault cluster with TLS and service-to-service auth:

- **3 Raft nodes** — integrated storage, auto-discovery via `retry_join`, one leader + two voters.
- **TLS** — self-signed CA, mTLS between nodes, HTTPS for clients.
- **AppRole** — `role_id` (public) + `secret_id` (secret) per service; token TTL 1h default, max 24h.
- **KV v2** — secrets organized by `secret/<service>/<env>`.
- **Seal** — Shamir (5 keys, threshold 3) in dev; `awskms` in production.

Services authenticate at startup (AppRole login → read `secret/<service>/<env>`) and must renew their token before expiry. `make add-service` registers a new service; `make write-secrets` populates its KV path.

## Access

| Endpoint | Local (development) | Notes | Production |
|---|---|---|---|
| Cluster | `https://127.0.0.1:8201` (nodes 8201–8203) | leader + voters | Same cluster; `awskms` seal, real CA/ACME PCA TLS |
| Auth | `POST /v1/auth/approle/login` | returns a temporary token | Same API; AppRole delivered via SSM |
| Secrets | `GET /v1/secret/data/<svc>/<env>` | KV v2 | Same API; state in S3 with DynamoDB locking |

Credentials source: AppRole `role_id`/`secret_id` in `data/secrets/` (gitignored); unseal keys + root token in the same folder, GPG-encrypted when `GPG_RECIPIENT` is set.

## Metrics

Vault exposes metrics natively at `https://127.0.0.1:8201/v1/sys/metrics?format=prometheus`. No external exporter needed. The telemetry listener is configured in `vault/config/vault.hcl` with `telemetry { prometheus_retention_time = "30s" }`.

| Metric | Type | What it measures |
|---|---|---|
| `vault_core_active` | Gauge | Active node (1) or standby (0) |
| `vault_raft_state_leader` | Gauge | Raft leader state |
| `vault_raft_peers` | Gauge | Number of Raft peers |
| `vault_core_leader_changes_total` | Counter | Leader failover events |
| `vault_core_handle_request_count` | Counter | Requests handled (label: `code`) |
| `vault_secret_read_total` | Counter | Secret reads |
| `vault_secret_write_total` | Counter | Secret writes |
| `vault_token_create_total` | Counter | Token creations |
| `vault_token_lookup_total` | Counter | Token lookups |
| `vault_audit_log_request_total` | Counter | Audit log request volume |
| `vault_runtime_heap_objects` | Gauge | Heap objects |
| `vault_runtime_alloc_bytes` | Gauge | Allocated memory |

Dashboard: [[observability#Dashboard map|vault.json]] | Alerts: `vault_leader_changed`, `vault_request_errors`

## Observability roadmap

- [ ] Dashboard `vault.json`
- [ ] Alert rules (`vault_leader_changed`, `vault_request_errors`)
- [ ] QA targets in `prometheus.yml`

## Target platform

On Kubernetes, Vault stays the single source of truth ([[adr-001-kubernetes-platform]]):

- **External Secrets Operator** projects KV and dynamic secrets into native K8s Secrets consumed by workloads ([[external-secrets-operator]]).
- **Dynamic database credentials** feed [[postgres]]; rotation propagates on each sync.
- Environments reference Vault paths — never values — from `envs/<env>/secrets.yaml` in `infra-kubernetes`.

## Pointers

- Component README: [infra-vault](https://github.com/sca-templates/infra-vault)
- Related notes: [[self-hosted-stack]] · [[observability]] · [[service-account]] · [[external-secrets-operator]]

## Status

Active — runs as part of the [[self-hosted]] stack.
