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

> Raised when `nest-logging` spots an anomaly in the [[evt-logging-audit]] stream.

## Schema

Key fields: `detected_pattern`, `scope` (subject / service / window), `severity`, `window` (time range), reference to the triggering log events.

## Producers

| Service | When | Notes |
|---|---|---|
| [[nest-logging]] | an anomaly is detected in the audit stream | |

## Consumers

| Service | Use | Idempotency |
|---|---|---|
| [[py-ai]] | deep behavioral analysis of the anomaly | dedupe by window + pattern |
| [[nest-notifications]] | alerting (on-call / dashboard) | dedupe by window + pattern |
| [[nest-auth]] | defensive actions (e.g. enter safe mode) | dedupe by window + pattern |

## Status

Planned — schema not published yet.
