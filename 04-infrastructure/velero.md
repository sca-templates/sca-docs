---
title: velero
type: infra
status: planned
repo: infra-kubernetes
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Velero

> Backup and restore of Kubernetes resources and persistent volumes — the cluster-level disaster-recovery tool.

## Role in the platform

- Scheduled backups of namespaces, resources and PV snapshots to S3-compatible object storage.
- Disaster recovery drills restore into an alternate cluster ([[adr-001-kubernetes-platform]], roadmap step 7).
- Complements Barman: Barman covers PostgreSQL point-in-time recovery specifically ([[postgres]]); Velero covers everything else.

## Deployment

- Helm install from `infra-kubernetes`; backup schedules and retention declared per environment.

## Pointers

- Related notes: [[postgres]] · [[multi-cloud]] · [[platform-overview]]

## Status

Planned — roadmap step 7 (backups & recovery) of the platform rollout ([[platform-overview]]).
