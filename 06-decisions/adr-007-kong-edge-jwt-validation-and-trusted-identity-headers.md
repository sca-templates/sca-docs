---
title: adr-007-kong-edge-jwt-validation-and-trusted-identity-headers
type: adr
status: decided
repo: sca-docs
tags:
  - type/adr
  - domain/auth
---

# ADR-007: Kong validates Keycloak JWTs at the edge with OSS plugins and forwards X-User-Id from sub

- **Status:** decided
- **Date:** 2026-08-23

## Context

[[adr-004-keycloak-identity]] made Keycloak the platform IdP and announced edge validation in [[kong]]; [[adr-006-keycloak-authentication-only-and-go-authz]] moved fine-grained authorization to go-authz and declared that untrusted sessions are blocked at the edge, leaving the concrete edge mechanism open. The local Kong stack then booted for the first time and surfaced a hard constraint: **Kong Gateway OSS 3.7 does not bundle the enterprise `openid-connect` plugin** — the original declarative config referenced it and the gateway never started. Meanwhile first-party services need a verified request identity without each one parsing tokens, and any richer claims model is explicitly deferred.

## Decision

Bearer-only authentication at the edge using only OSS-bundled plugins, plus a strict upstream identity-header contract:

- **Validation** — the bundled `jwt` plugin verifies RS256 signature and `exp` against the realm's public key, pinned to a realm-level consumer whose credential key equals the token issuer (`http://127.0.0.1:8180/realms/sca` locally; per-environment values live in infra-kubernetes). No client secret is involved; bearer validation is stateless and makes no per-request call to Keycloak.
- **Render-time key injection** — the committed `kong/kong.yml` stays the editable SSOT with a placeholder; `scripts/render-kong-yml.sh` fetches the JWKS, strips the X.509 certificate down to its SPKI public key and writes `.generated/kong.yml` (gitignored), which compose mounts. A cached key allows offline re-renders and self-heals on any successful fetch (realm resets included); rotation = re-render.
- **Trusted identity headers** — after validation succeeds, a `post-function` (priority −1000, guaranteed to run after the `jwt` plugin's 1450) decodes the verified payload and sets `X-User-Id ← sub` on the upstream request, overwriting anything the client sent. Upstream services must never trust this header arriving from outside Kong.
- **Only `sub` travels** — additional claims (roles, scopes, device context) do not cross the edge today. A claims model beyond `sub` requires its own decision; fine-grained permissions remain go-authz's fresh evaluation per [[adr-006-keycloak-authentication-only-and-go-authz]]. Tokens must carry `sub`: clients therefore need Keycloak's default `basic` client scope (KC26).
- **Routing conventions** — one pair per exposed endpoint (`<endpoint>-service` + `<endpoint>-route`, `<name>-upstream` behind it), paths `/api/<endpoint>`, all guarded; rate limiting stays coarse at the edge (120 req/min per consumer/IP, `policy: local`).
- **Verification fixtures** — local-only client `sca-dev-cli` (password grant) and user `dev-tester` exist in the realm export so `make validate` proves guard + header forwarding end to end through an echo route; production has neither.

## Options considered

| Option | Why not / why chosen |
| --------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `openid-connect` plugin | Enterprise-only; absent from Kong OSS 3.7 — the reason the original config never booted   |
| Custom Lua plugin fetching the JWKS itself          | More first-party code on the hottest path; JWKS libs not bundled either                   |
| Token introspection against Keycloak                | Network hop per request; confidential client needed; couples edge availability to the IdP |
| Validation duplicated inside every service          | No single choke point; guard drift across stacks                                          |
| `jwt` plugin + `post-function` header (chosen)      | Both bundled in OSS; signature+exp verified strictly before the header is created         |

## Consequences

- Positive: the edge boots on stock OSS images; stateless validation adds no IdP dependency per request; the identity contract is continuously proven by `make validate` (401 guards, token grant, `X-User-Id == sub` through the echo route) and observed by the Grafana `kong-edge` dashboard.
- Negative: serverless functions run sandboxed (`require()` unavailable), so the claim is extracted by pattern from the decoded payload instead of a JSON parser; the pinned key must be re-rendered after realm key changes; the header contract only holds while services are reachable exclusively through Kong.

## Open point

- Unchanged from [[adr-006-keycloak-authentication-only-and-go-authz]]: how Kong learns that a session/device fails trust conditions (edge plugin consulting go-authz vs introspection) — follow-up design together with go-authz.

## Related

- Refines [[adr-004-keycloak-identity]]; executes the edge-trust clause of [[adr-006-keycloak-authentication-only-and-go-authz]]
- [[kong]] · [[keycloak]] · [[scope]]
