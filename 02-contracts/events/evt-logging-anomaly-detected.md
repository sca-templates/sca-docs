---
title: logging.anomaly.detected
type: contract-event
status: planned
repo: sca-core
tags:
  - type/contract-event
  - domain/logging
  - publishes-events
---

# logging.anomaly.detected

> Raised when `sca-logging` spots an anomaly in the [[evt-logging-audit]] stream.

## Schema

Key fields: `detected_pattern`, `scope` (subject / service / window), `severity`, `window` (time range), reference to the triggering log events.

## Producers

| Service | When | Notes |
|---|---|---|
| [[sca-logging]] | an anomaly is detected in the audit stream | |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| [[sca-ai]] | deep behavioral analysis of the anomaly | dedupe by window + pattern |
| [[sca-notifications]] | alerting (on-call / dashboard) | dedupe by window + pattern |
| [[sca-auth]] | defensive actions (e.g. enter safe mode) | dedupe by window + pattern |

## Status

Planned — schema not published yet.
