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

The [[self-hosted]] stack is the default; production is orchestrated with Ansible roles and Terraform modules (both under `aws/`). The matrix below maps each component's port to its managed equivalent in each cloud. The `@sca/connections` package is the single place that swaps the adapter.

## Port → adapter matrix

| Component | Local | AWS | Azure | GCP |
|---|---|---|---|---|
| Vault (`8201`) | [[vault]] cluster | AWS Secrets Manager (+ KMS seal) | Azure Key Vault | Secret Manager |
| PostgreSQL (`5432`) | [[postgres]] | RDS | Azure Database for PostgreSQL | Cloud SQL |
| Redis (`6379`) | [[redis]] | ElastiCache | Azure Cache for Redis | Memorystore |
| Kafka (`9092`) | [[kafka]] | MSK (or Kinesis for streaming) | Event Hubs | Pub/Sub |
| MinIO (`9000`) | [[dev-tools]] | S3 | Blob Storage | Cloud Storage |
| MailHog (`1025`) | [[dev-tools]] | SES | Communication Services | — |
| Consul (`8500/8600`) | [[consul]] | self-hosted on EC2 | — | — |

### Notes

- **Consul** has no direct managed equivalent; production keeps a self-hosted agent on EC2 (`terraform/modules/consul`, `ansible/roles/consul`).
- **Kafka** also has a `terraform/modules/kinesis` alternative for stream ingestion when a fully managed path is preferred over MSK.
- **Secrets** follow the [[service-account|service-account]] model in every cloud (IAM roles / managed identities / service accounts).
- Adapter modules in `aws/`: `terraform/modules/{vault,rds,elasticache,kafka,kinesis,s3,secrets-manager,consul}` and `ansible/roles/{vault,postgresql,redis,kafka,consul,observability}`.

## Pointers

- Terraform modules: [aws/terraform/modules](../../terraform/modules)
- Ansible roles: [aws/ansible/roles](../../ansible/roles)
- Related notes: [[self-hosted-stack]] · [[multi-cloud]] · [[service-account]]

## Status

Active — documents the failover strategy; the local stack remains the default.
