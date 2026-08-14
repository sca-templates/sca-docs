---
title: 04-infrastructure — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/infrastructure
---

# Infrastructure — INDEX

> The [[self-hosted]] topology of the `sca` ecosystem, orchestrated by the `aws/` repo, plus the [[multi-cloud]] failover strategy.

## Notes

| Note | What it is | Status |
|---|---|---|
| [[self-hosted-stack]] | The `aws/` orchestrator: `make all`, ports, secrets flow | active |
| [[vault]] | 3-node Raft cluster, source of secrets | active |
| [[postgres]] | PostgreSQL 16 + pgvector, Debezium source | active |
| [[redis]] | Redis 7 with AOF + password auth | active |
| [[kafka]] | KRaft stack + Debezium + Kafka UI | active |
| [[consul]] | Service discovery + TCP health checks | active |
| [[dev-tools]] | MinIO + MailHog — local only | active |
| [[multi-cloud]] | Port → managed adapter matrix (local/AWS/Azure/GCP) | active |

## Keywords

infrastructure, self-hosted, vault, postgres, redis, kafka, consul, debezium, minio, mailhog, multi-cloud, failover, service discovery

## Search order

1. Read [[self-hosted-stack]] first — the whole stack in one view.
2. Then the component you care about: [[vault]], [[postgres]], [[redis]], [[kafka]], [[consul]].
3. [[dev-tools]] for local-only tooling, [[multi-cloud]] for the failover strategy.
