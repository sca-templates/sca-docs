---
title: adr-006-keycloak-authentication-only-and-go-authz
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/auth
---

# ADR-006: Keycloak authenticates only; fine-grained authorization lives in go-authz

- **Status:** decided
- **Date:** 2026-08-21

## Context

[[adr-004-keycloak-identity]] adopted Keycloak as the platform IdP but left open where authorization lives. The initial ecosystem design concentrated identity, authentication and authorization in one NestJS service (`nest-auth`): subjects, scopes and roles mutated there, guards resolved them over gRPC `GetScopes`/`GetRoles`, a `safe_mode` flag traveled inside the JWT to degrade permissions of live sessions, and local tables mirrored entities Keycloak administers. That couples permissions to token contents, fragments the audit trail, forces realm-per-account-type splits, invites drift through mirror tables, and makes degraded sessions hard to reason about and audit.

## Decision

Keycloak is responsible exclusively for identity and authentication — login, passwords, MFA/TOTP, email verification, issuance and lifecycle of access/refresh/offline tokens, and the roles catalog. Everything authorization-shaped belongs to the application's own domain, with [[go-authz]] as the single decision point:

- **Scopes** — fine-grained authorization (scopes; permissions per endpoint/page/field) is computed by our own logic, never delegated to Keycloak and never embedded in the JWT. Effective scopes of an account = its [[realm-role|realm roles]] + individual overrides, deny taking precedence over grant, overrides supporting expiry — evaluated on every request ([[scope]]).
- **No mirror tables** — nothing replicates entities Keycloak administers (roles, credentials, OTP, verification tokens); references use the external name/identifier, without any synchronization.
- **One realm, realm roles only** — no groups (they add no value here). Account types (employee, customer…) are distinguished by role, not by separate realms, because one person may hold more than one account of different types. A role is conceptually a predefined bundle of business scopes.
- **go-authz** — dedicated service in Go (native gRPC support, low resource consumption, minimal deployment image; high invocation volume) owning scope assignments/overrides. It exposes `CheckScopes` over gRPC ([[grpc-authz-api]]) so any internal service verifies an active account without external HTTP or intermediate microservices on the critical path.
- **Sessions** — short-lived access tokens validated by public signature, never extended (renewed via refresh token); standard web session is a short window with hard expiry; "remember me" is explicit opt-in requested at login producing a longer, explicitly revocable session; every application registers as its own Keycloak client with its own session policy.
- **Trust at the edge, not degraded modes** — there is no safe mode downgrading permissions inside a live session: when a session or device does not meet expected trust conditions, access is blocked entirely at the network edge ([[kong]]) before reaching any business service.
- **Audit pipeline** — Keycloak emits user and admin events. A first-party listener plugin inside the Keycloak process filters relevant events and forwards them asynchronously — never blocking login — over an internal HTTP endpoint to go-authz, which translates them into the business-audit model and publishes them onto [[evt-logging-audit]]. Translation lives in go-authz, not in the plugin, so mapping changes do not force plugin rebuilds/redeploys. Business audit (account, scope, device changes; access decisions) and Keycloak-derived audit share the single historical registry owned by [[nest-logging]].
- **Process hygiene** — each responsibility runs in its own container: Keycloak alone, go-authz alone; no mixed-process images. No flow-orchestration tools (n8n-like) nor extra single-purpose microservices when an existing or planned service can host the route.

## Options considered

| Option                                              | Why not / why chosen                                                                                |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Authorization inside Keycloak (client scopes / UMA) | Couples policy to the IdP; token claims become a permission snapshot that outlives revocations      |
| Mirror tables synced from Keycloak                  | Two sources of truth; drift plus sync machinery to build and debug                                  |
| One realm per account type                          | Same person may hold several accounts across types; roles already distinguish them inside one realm |
| Keep nest-auth as identity + authorization hub      | One process mixing two natures; NestJS/gRPC overhead on the hottest call path                       |
| JWT-carried permission flags (safe_mode)            | Stale claims within live sessions; degraded states are hard to audit                                |

## Consequences

- Positive: single fresh evaluation point per request; Keycloak stays close to stock (only one small first-party plugin); one audit pipeline converging into [[nest-logging]]'s tamper-evident registry; account types scale by role instead of realm sprawl.
- Negative: go-authz becomes critical-path infrastructure requiring HA; scope checks depend on gRPC plus Redis availability (guards fall back to direct calls when Redis fails); the listener plugin is first-party code shipped alongside Keycloak upgrades.

## Open point

- How [[kong]] learns that a session/device fails trust conditions (edge plugin consulting go-authz vs token introspection) is deliberately undecided — follow-up design before platform rollout step 3.

## Related

- Refines [[adr-004-keycloak-identity]]; deployment model per [[adr-005-per-service-repos-centralized-k8s-config]]
- [[go-authz]] · [[grpc-authz-api]] · [[keycloak]] · [[nest-logging]]
- [[scope]] · [[realm-role]]
