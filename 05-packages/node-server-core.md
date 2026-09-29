---
title: node-server-core
type: package
status: planned
repo: node-server-core
tags:
  - type/package
  - domain/packages
  - stack/nodejs
---

# @sca-templates/node-server-core

> Shared plumbing for the organization's **Node.js** services — helpers, error
> types and shared constants — so a fix lands once instead of in every
> repository.

## Content

- `src/index.ts` — the public entry point. Today it exports a single `ping()`
  placeholder; the real surface is not decided yet.
- Intended home for the plumbing every Node service repeats: helpers, error
  types, shared constants.
- Built with tsup to a single ESM bundle plus `.d.ts` (`dist/`), targeting
  `node22`. `sideEffects: false`.

Nothing here is business logic. The domain of each service stays in its own
repository.

## Dependencies

| Package | Why |
|---|---|
| — | None at runtime. devDependencies only: tsup, vitest, ESLint, Prettier, Husky, commitlint. |

## Role

The Node.js counterpart to [[sca-core]]: the same "pure foundation, zero runtime
dependencies" idea, packaged as an installable npm library for TypeScript
services.

Where [[sca-core]] is the `@sca/*` workspace foundation consumed inside this
org's NestJS services, this package is published from its own repository under
the `@sca-templates/` scope. It does **not** depend on, and is not part of, the
`sca-core` workspace.

## Pointers

- Repo: [`sca-templates/node-server-core`](https://github.com/sca-templates/node-server-core)
- README: [`blob/main/README.md`](https://github.com/sca-templates/node-server-core/blob/main/README.md)
- Agent guide: [`blob/main/AGENTS.md`](https://github.com/sca-templates/node-server-core/blob/main/AGENTS.md)
- CI templates consumed:
  [`shared-security-scan.yml`](https://github.com/sca-templates/CI-CD-Templates/blob/main/.github/workflows/shared-security-scan.yml),
  [`stack-node-ts.yml`](https://github.com/sca-templates/CI-CD-Templates/blob/main/.github/workflows/stack-node-ts.yml)
- Consumers: none yet.

## Status

`planned`. The repository, CI and release automation exist and are green, but:

- the package is **not on npm** — the `@sca-templates` scope does not exist on
  the registry, so it cannot be installed as a dependency yet;
- releases are **not running**: `.release-please-manifest.json` sits at `0.0.0`
  with no baseline tag, so release-please reports `No version for path .` and
  exits `0` without producing a release. A green Release check does not mean a
  release happened;
- the package is still a scaffold around `ping()`, pending a decision on its
  real surface.
