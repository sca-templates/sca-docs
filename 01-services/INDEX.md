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
| [[go-authz]] | fine-grained authorization (effective scopes) + Keycloak audit bridge | Go | planned |
| [[nest-notifications]] | transactional and security notifications | NestJS | planned |
| [[nest-logging]] | technical logging + security/business audit trail (unified registry) | NestJS | planned |
| [[py-ai]] | AI agents | Python | planned |
| [[nest-auth]] | identity/authentication/authorization hub — replaced by [[keycloak]] + [[go-authz]] | NestJS | deprecated |

## Keywords

services, microservices, auth, authz, go-authz, check-scopes, scopes, realm-role, notifications, logging, ai, audit

## Search order

1. Read [[go-authz]] first — the authorization decision point every service leans on; authentication itself is [[keycloak]].
2. Then [[nest-logging]] (the unified audit registry) and [[nest-notifications]].
3. [[py-ai]] last — the only non-NestJS service.
