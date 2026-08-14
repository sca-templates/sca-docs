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
| [[sca-auth]] | identity, authentication, authorization | NestJS | planned |
| [[sca-notifications]] | transactional and security notifications | NestJS | planned |
| [[sca-logging]] | technical logging + security audit trail | NestJS | planned |
| [[sca-ai]] | AI agents | Python | planned |

## Keywords

services, microservices, auth, notifications, logging, ai, scopes, roles, safe-mode, audit

## Search order

1. Read [[sca-auth]] first — the identity hub every service leans on.
2. Then [[sca-logging]] (the audit base) and [[sca-notifications]].
3. [[sca-ai]] last — the only non-NestJS service.
