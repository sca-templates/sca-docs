---
title: kafka
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Kafka

> KRaft-mode Kafka stack with production-grade security (SASL_SSL/SCRAM-SHA-512 + TLS + ACLs), Debezium CDC and Kafka UI.

## Role in the stack

- **Kafka broker** in KRaft mode (no ZooKeeper) — `apache/kafka:3.7.1`.
- **Debezium (Kafka Connect)** — captures PostgreSQL (`postgres-app`) changes via the `pgoutput` plugin into `kt.public.<table>` topics; replication slot `kt_slot`, publication `kt_publication`, JSON converters, no Schema Registry.
- **Kafka UI** — `provectuslabs/kafka-ui`, development browser.
- **Security** — SASL_SSL / SCRAM-SHA-512 on the client listener, TLS truststore JKS, ACLs with `allow.everyone.if.no.acl.found=false`. Users: `kafka-admin` (super), `debezium` (read/write on `kt.*`), `kafka-ui` (global read).
- **Topics** are created up front (`kt.events`, `kt.jobs`, `kt.notifications`) and by Debezium (`kt.public.*`).

TLS + SASL are **mandatory** for every client. Secrets only enter containers via `.env`; the same Compose runs in production with externally provisioned `.env`/`secrets/`. This is the transport for ecosystem [[event]]s, delivered reliably via the [[outbox|outbox pattern]] and consumed with [[idempotency|idempotent]] handlers.

## Access

| Endpoint | Local (development) | Notes | Production |
|---|---|---|---|
| Broker | `localhost:9092` (host) · `kafka-broker:9092` (network) | SASL_SSL required | Same image; real TLS certs, advertised host is EC2 IP |
| Connect (Debezium) | `http://localhost:8083` | connector `debezium-postgres` | Same image; provisioned externally |
| Kafka UI | `http://localhost:8088` | user `admin`, password in `.env` | Internal only |

Credentials source: SCRAM users (`kafka-admin`, `debezium`, `kafka-ui`) with passwords in `.env` / Vault `secret/kafka/dev`.

## Pointers

- Component README: [kafka](https://github.com/sca-templates/kafka)
- Related notes: [[self-hosted-stack]] · [[postgres]] · [[event]] · [[outbox]] · [[idempotency]]

## Status

Active — runs as part of the [[self-hosted]] stack.
