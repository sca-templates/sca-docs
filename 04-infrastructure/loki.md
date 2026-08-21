---
title: loki
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Loki

> Log aggregation pillar of observability: centralized container/application logs beside metrics and traces in Grafana.

## Role in the platform

- Stores logs indexed by labels only — cheap retention at platform scale.
- Shippers: Promtail on nodes and/or OpenTelemetry Collector pipelines feeding it.
- Correlates with [[prometheus]] metrics and [[tempo]] traces from Grafana panels (log↔trace links).

Part of the four-pillar model described in [[observability]].

## Deployment

- Helm install from `infra-kubernetes`; Grafana datasource provisioned alongside ([[grafana]]).

## Pointers

- Related notes: [[observability]] · [[grafana]] · [[prometheus]] · [[tempo]] · [[platform-overview]]

## Status

Planned — roadmap step 4 (observability) of the platform rollout ([[platform-overview]]).
