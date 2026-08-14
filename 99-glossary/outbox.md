---
title: outbox
type: glossary
status: active
repo: sca-docs
tags:
  - type/glossary
  - domain/contracts
---

# outbox

Pattern that persists an [[event]] in the same transaction as the state change, then publishes it reliably to [[kafka]] (Debezium CDC in the self-hosted stack).

## Related

- [[event]]
- [[kafka]]
- [[idempotency]]
