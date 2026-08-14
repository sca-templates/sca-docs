# sca ecosystem — documentation

The `sca` ecosystem as a whole: a repeatable way to spin up domain microservices on shared, self-hosted infrastructure — with contracts and docs already in sync. This repository is the Obsidian vault that holds the ecosystem's **topology** (who is what, what talks to what) and **conventions**; depth lives in each repo's own docs.

## What is the sca ecosystem

- **`nest-template`** — the modular-monolith + clean-architecture skeleton every microservice is cloned from. It carries the structure, the flow framework and the docs handbook; zero business logic.
- **`@sca/*` packages** — shared plumbing with zero business logic: core, contracts (gRPC protos + event schemas), connections and clients. One fix lands in one package, not in every service.
- **`sca-*` microservices** — the domains (auth, notifications, logging, ai). Each is a `nest-template` clone consuming `@sca/*`, exposing its gRPC API and publishing/consuming Kafka events.
- **`aws/`** — the self-hosted infrastructure orchestrator: Vault, PostgreSQL, Redis, Kafka, Consul. One command (`make all`) brings the whole stack up.
- **`sca-docs`** — this vault: topology, conventions and pointers to every repo.

## Why it exists

- Clone a new service with infrastructure and contracts already in sync — the domain advances at its own pace.
- A correction is made in one place (`@sca/*`, `nest-template` or `aws/`) and every service inherits it.
- The outbox pattern and gRPC contracts are defined once and reused everywhere.

## Flow

1. Start the stack: `make all` in `aws/`.
2. Clone `nest-template` and open the workspace.
3. Add the `@sca/*` packages (`pnpm add`).
4. Write the domain following the handbook's flow framework.
5. Close the docs checklist: README, `docs/`, service note in the vault, contract notes.

## Repository map

| Repo | What it is | Status |
|---|---|---|
| `sca-docs` | This vault: topology + conventions — [README](README.md), [super template](00-ecosystem/super-template.md), [HOME](00-ecosystem/HOME.md) | active |
| `aws` | Self-hosted stack orchestrator — [README](../README.md) | active |
| `nest-template` | Microservice skeleton + handbook — [README](../../node/nest-template/README.md), [handbook](../../node/nest-template/docs/handbook/INDEX.md) | active |
| `@sca/core`, `@sca/contracts`, `@sca/connections`, `@sca/clients` | Shared plumbing packages | planned |
| `sca-auth`, `sca-notifications`, `sca-logging`, `sca-ai` | Domain microservices | planned |

## Boundaries

- **Not a cloud platform** — the ecosystem is self-hosted by design; cloud adapters are a failover option.
- **Not a framework** — `nest-template` and `@sca/*` carry plumbing, never business logic.
- **Not a monolith** — each domain ships as its own microservice, even though each one is a modular monolith internally.
- **Not duplicated** — the vault describes topology and links; it never copies repo content.

## Documentation

The vault is built incrementally: each area gets its catalog (`INDEX.md`) as the ecosystem docs land, and `03-connections-map/connection-map.md` is regenerated whenever a contract or service changes. Start at `00-ecosystem/HOME.md`; see `00-ecosystem/conventions.md` for how to contribute notes.

## Tooling and MCP

Markdown lint, link check, editor config and the Obsidian/GitHub MCP setup are documented in [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT — see [LICENSE](LICENSE).
