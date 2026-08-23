---
title: keycloak
type: infra
status: active
repo: aws
tags:
  - type/infra
  - domain/infrastructure
  - stack/multi-lang
---

# Keycloak

> Self-hosted OIDC/JWT identity provider — authentication only; authorization lives in first-party services ([[adr-006-keycloak-authentication-only-and-go-authz]]).

## Role in the platform

- **Authentication only** — login, passwords, MFA/TOTP, email verification; issues and manages access/refresh/offline tokens. Access tokens are short-lived, validated by public signature at the edge ([[kong]]), and never extended — renewal goes through the refresh token.
- **Single realm** for the whole platform, **realm roles only** (no groups). Account types (employee, customer…) are distinguished by role, not by realms, because one person may hold several accounts of different types ([[realm-role]]).
- **One client per application** (web, mobile…), each with its own session policy: standard sessions are short windows with hard expiry; "remember me" is explicit opt-in requested by the client at login and produces a longer, explicitly revocable session.
- **Does NOT decide permissions** — scopes and fine-grained rules are computed by [[go-authz]] on every request and never embedded in tokens.
- **No mirror state outside Keycloak** — services reference its entities (roles, credentials, OTP, verification tokens) by external id/name; no synchronized local tables.

## Audit events

Keycloak is configured to emit user and admin events. A first-party listener plugin inside the Keycloak process (code: `keycloak-events-listener`) filters relevant events and forwards them asynchronously — never blocking login — over internal HTTP to [[go-authz]], which translates them into the business-audit model; [[nest-logging]] keeps the unified historical registry.

## Access (target)

| Endpoint                              | Notes                                        |
| ------------------------------------- | -------------------------------------------- |
| `https://<host>/auth` per environment | Behind [[kong]]; admin console internal only |

Realm/client configuration source of truth: `infra-kubernetes` (declarative realm exports).

## Pointers

- Decisions: [[adr-004-keycloak-identity]] · refined by [[adr-006-keycloak-authentication-only-and-go-authz]]
- Listener plugin repo: `keycloak-events-listener`, resolved in `_config/repo-locations.md`
- Related notes: [[kong]] · [[go-authz]] · [[platform-overview]]

## Status

Planned — roadmap step 3 (traffic & security) of the platform rollout ([[platform-overview]]).
