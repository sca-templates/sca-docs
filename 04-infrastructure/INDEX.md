---
title: 04-infrastructure — INDEX
type: moc
status: active
repo: sca-docs
tags:
  - type/moc
  - domain/infrastructure
---

# Infrastructure — INDEX

> Infrastructure of the `sca` ecosystem: the [[self-hosted-stack|local dev stack]] (one `infra-*` repo per component) converging on the portable Kubernetes platform ([[platform-overview]]), with the [[multi-cloud|multi-cloud adapter policy]].

## Notes

| Note | What it is | Status |
|---|---|---|
| [[self-hosted-stack]] | Local dev stack: `make all` per repo, ports, secrets flow | active |
| [[vault]] | 3-node Raft cluster, source of secrets; projected by ESO on the platform | active |
| [[external-secrets-operator]] | Projects Vault secrets into native K8s Secrets | planned |
| [[postgres]] | PostgreSQL 16 + pgvector, Debezium source; CloudNativePG + Barman target | active |
| [[redis]] | Redis 7 with AOF + password auth; operator + Sentinel target | active |
| [[kafka]] | KRaft stack + Debezium + Kafka UI; Strimzi HA target | active |
| [[consul]] | Local discovery + TCP health checks; superseded by K8s DNS + Linkerd | deprecated |
| [[linkerd]] | Service mesh: mTLS identity, retries/timeouts, network telemetry | planned |
| [[kong]] | Edge gateway: routing, rate limiting, OIDC/JWT validation | planned |
| [[keycloak]] | Self-hosted OIDC/JWT identity provider | planned |
| [[unleash]] | Feature flags per environment without redeploys | planned |
| [[argocd]] | GitOps engine syncing `infra-kubernetes` into every cluster | planned |
| [[prometheus]] | Central TSDB + bundled exporters | active |
| [[grafana]] | Central Grafana: dashboards + alerting (Prometheus-backed) | active |
| [[loki]] | Log aggregation pillar beside metrics in Grafana | planned |
| [[tempo]] | Distributed tracing backend (OpenTelemetry) | planned |
| [[velero]] | Cluster resources + PV backup and DR restore | planned |
| [[observability]] | Metrics map, dashboards, alert rules; platform pillars ahead | active |
| [[dev-tools]] | MinIO + MailHog — local only | active |
| [[multi-cloud]] | Managed-service adapter / failover matrix | active |

## Keywords

infrastructure, self-hosted, kubernetes-platform, vault, external-secrets, postgres, cloudnativepg, barman, redis, sentinel, kafka, strimzi, debezium, consul, linkerd, mtls, service-mesh, kong, gateway, keycloak, oidc, jwt, unleash, feature-flags, argocd, gitops, prometheus, grafana, loki, tempo, opentelemetry, tracing, velero, backup, disaster-recovery, multi-cloud, failover, observability

## Search order

1. Read [[self-hosted-stack]] first — the local stack in one view.
2. Read [[platform-overview]] — the target platform these notes converge to.
3. Then the component you care about: existing ([[vault]], [[postgres]], [[redis]], [[kafka]], [[prometheus]], [[grafana]]) or planned ([[linkerd]], [[kong]], [[keycloak]], [[external-secrets-operator]], [[argocd]], [[unleash]], [[loki]], [[tempo]], [[velero]]).
4. [[observability]] for metrics coverage, dashboards and alert rules.
5. [[dev-tools]] for local-only tooling, [[multi-cloud]] for the managed-service adapter policy.
