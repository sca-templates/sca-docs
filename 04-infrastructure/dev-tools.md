---
title: dev-tools
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Dev tools

> MinIO (S3-compatible object storage) and MailHog (SMTP capture) — **local development only**, never production.

## Role in the stack

A copy of the old `development` stack, kept because both tools are very useful locally:

- **MinIO** — object storage compatible with the AWS **S3** API. Code talks S3 the same way in local and production; only the endpoint and credentials change.
- **MailHog** — SMTP capture server; it never sends real emails.

Both join the shared `kafka-network` so local services reach them by name (`minio`, `mailhog`). In production the S3 adapter is AWS S3 — see [[multi-cloud]].

## Access

| Endpoint | Local (development) | Notes | Production |
| ------------- | ----------------------- | ------------------------------------ | ---------- |
| MinIO API | `http://localhost:9000` | S3, `forcePathStyle: true` | N/A (use AWS S3) |
| MinIO console | `http://localhost:9001` | default `minioadmin`/`minioadmin123` | N/A |
| MailHog SMTP | `localhost:1025` | capture only | N/A (use AWS SES) |
| MailHog UI | `http://localhost:8025` | inbox viewer | N/A |

Credentials source: `dev/.env` (MinIO `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD`).

## Pointers

- Component README: [dev](https://github.com/sca-templates/dev)
- Related notes: [[self-hosted-stack]] · [[multi-cloud]]

## Status

Active — local development only.
