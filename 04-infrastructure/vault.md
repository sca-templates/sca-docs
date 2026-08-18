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

## Pointers

- Component README: [vault](https://github.com/sca-templates/vault)
- Related notes: [[self-hosted-stack]] · [[service-account]]

## Status

Active — runs as part of the [[self-hosted]] stack.
