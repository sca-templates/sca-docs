---
title: multi-cloud
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Multi-cloud

> Failover strategy: every [[self-hosted]] component maps to an equivalent managed adapter per cloud, so the ecosystem can run without the local stack.

## Role in the stack

The portable Kubernetes platform ([[platform-overview]]) is the primary substrate; cloud-native managed services are optional adapters — principal for a project or disaster-recovery failover — judged on cost, availability requirements, lock-in tolerance and operational complexity ([[adr-001-kubernetes-platform]]). The matrix below maps each component to its managed equivalent per cloud; the `@sca/connections` package remains the single place that swaps the adapter.

## Port → adapter matrix

| Component | Local | AWS | Azure | GCP |
|---|---|---|---|---|
| Vault (`8201`) | [[vault]] cluster | AWS Secrets Manager (+ KMS seal) | Azure Key Vault | Secret Manager |
| PostgreSQL (`5432`) | [[postgres]] | RDS | Azure Database for PostgreSQL | Cloud SQL |
| Redis (`6379`) | [[redis]] | ElastiCache | Azure Cache for Redis | Memorystore |
| Kafka (`9092`) | [[kafka]] | MSK (or Kinesis for streaming) | Event Hubs | Pub/Sub |
| MinIO (`9000`) | [[dev-tools]] | S3 | Blob Storage | Cloud Storage |
| MailHog (`1025`) | [[dev-tools]] | SES | Communication Services | — |
| Consul (`8500/8600`) | [[consul]] | retired — K8s DNS + Linkerd ([[adr-002-linkerd-service-mesh]]) | — | — |

### Notes

- **Consul** retires on the platform path: Kubernetes DNS plus Linkerd cover discovery and workload identity ([[adr-002-linkerd-service-mesh]]).
- **Secrets** follow the [[service-account|service-account]] model in every cloud (IAM roles / managed identities / service accounts).

## Pointers

- Related notes: [[self-hosted-stack]] · [[multi-cloud]] · [[service-account]]

## Status

Active — documents the adapter/failover policy; the Kubernetes platform ([[platform-overview]]) is primary.
