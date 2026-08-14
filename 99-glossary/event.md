---
title: event
type: glossary
status: active
repo: sca-docs
tags:
  - type/glossary
  - domain/contracts
---

# event

A fact published to [[kafka]] announcing that something happened; services consume it and react with [[idempotency|idempotent]] handlers (`evt-*.md` contract notes).

## Related

- [[kafka]]
- [[outbox]]
- [[idempotency]]
