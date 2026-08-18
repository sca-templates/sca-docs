---
title: 01-services — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
---

# Services — INDEX

> The [[microservice|microservices]] of the `sca` ecosystem: each owns one domain, shares contracts and packages, and links to its handbook.

## Services

| Note | Domain | Stack | Status |
|---|---|---|---|
| [[nest-auth]] | identity, authentication, authorization | NestJS | planned |
| [[nest-notifications]] | transactional and security notifications | NestJS | planned |
| [[nest-logging]] | technical logging + security audit trail | NestJS | planned |
| [[py-ai]] | AI agents | Python | planned |

## Keywords

services, microservices, auth, notifications, logging, ai, scopes, roles, safe-mode, audit

## Search order

1. Read [[nest-auth]] first — the identity hub every service leans on.
2. Then [[nest-logging]] (the audit base) and [[nest-notifications]].
3. [[py-ai]] last — the only non-NestJS service.
