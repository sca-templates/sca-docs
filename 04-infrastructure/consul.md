---
title: consul
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Consul

> Single-node HashiCorp Consul agent providing service discovery and TCP health checks for the stack.

## Role in the stack

- **Agent/server** (`hashicorp/consul:1.19`) with `-bootstrap-expect=1`, datacenter `dev`, running with `network_mode: host` so its TCP checks reach the published ports.
- **Registers** the stack services with TCP checks (`interval: 10s`, `timeout: 5s`): `postgres-app:5432`, `redis:6379`, `kafka:9092`, `kafka-connect:8083`, `vault:8201`.
- **DNS** — any [[microservice]] resolves a component as `<name>.service.consul` through `127.0.0.1:8600`.
- Gossip key (`CONSUL_GOSSIP_KEY`) comes from Vault locally and AWS Secrets Manager in production; no ACLs in dev. Production mirrors the same image and flags via Ansible + Terraform.

## Access

| Endpoint | Address                 | Notes                  |
| -------- | ----------------------- | ---------------------- |
| API + UI | `http://127.0.0.1:8500` | UI under `/ui`         |
| DNS      | `127.0.0.1:8600`        | `<svc>.service.consul` |

Credentials source: `CONSUL_GOSSIP_KEY` in Vault `secret/consul/dev`.

## Pointers

- Component README: [aws/consul/README.md](../../consul/README.md)
- Related notes: [[self-hosted-stack]] · [[microservice]]

## Status

Active — runs as part of the [[self-hosted]] stack.
