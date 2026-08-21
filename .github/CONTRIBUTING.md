# Contributing to sca-docs

> Every fact lives in one place. This vault describes the ecosystem's topology and links to repo docs; it never copies them. Docs-as-code: all changes land through a PR with review.

## Ground rules

- **English only** — notes, commits, and PR descriptions are written in English.
- **One fact, one place** — depth lives in the repos, topology/maps in the vault, pointers in READMEs. A fact is edited in exactly one file, never duplicated.
- **Docs-as-code** — every change goes through a pull request and is reviewed.

## Conventions

### Vault layout

```text
00-ecosystem/        Home, super template, conventions
01-services/         Service catalog (nest-* / py-* microservices)
02-contracts/        gRPC contracts + Kafka events
03-connections-map/  The ecosystem graph (mermaid + matrices)
04-infrastructure/   Self-hosted topology + multi-cloud
05-packages/         @sca/* shared packages
06-decisions/        Ecosystem-level ADRs
99-glossary/         Ubiquitous language (one line per term)
_templates/          Note templates
_config/             Metadata: repo locations, tagging and naming
```

### Naming and frontmatter

- Folders are `NN-area/`, files are kebab-case English.
- ADRs: `adr-NNN-kebab-title.md`. Contracts: `grpc-<domain>.md`, `evt-<topic>.md`.
- Frontmatter is required on vault notes:

```yaml
---
title: nest-auth
type: service # home | moc | service | contract-grpc | contract-event | infra | package | adr | glossary
status: active # planned | active | deprecated | decided
repo: nest-auth # logical name, resolved in _config/repo-locations.md
tags:
  - type/service
  - domain/auth # auth | notifications | logging | ai | infrastructure | contracts | packages
  - stack/nestjs # nestjs | python | multi-lang
  - exposes-grpc # connectivity
  - publishes-events
---
```

Tags come from the closed taxonomy: `type/*`, `domain/*`, `stack/*`, `connectivity/*` (`exposes-grpc`, `consumes-grpc`, `publishes-events`, `consumes-events`), `status/*`.

### Links

- **Inside the vault** → wikilinks `[[…]]` (backlinks + graph).
- **Toward repos** → relative markdown links `[label](../<repo>/path.md)`. Real paths are resolved per environment in `_config/repo-locations.md`.

## Contribution flow

1. Branch off `main`: `git checkout -b feat/<topic>`.
2. Create or edit notes following the conventions above.
3. Keep catalogs in sync: update `INDEX.md` tables and regenerate `03-connections-map/connection-map.md` when a contract or service note changes.
4. Run the checks (see Tooling).
5. Open a PR and fill the checklist from the template.

## Definition of done

- [ ] Content is in English.
- [ ] Frontmatter is valid and uses the closed tag taxonomy.
- [ ] `INDEX.md` catalogs are updated.
- [ ] `connection-map.md` is regenerated if contracts/services were touched.
- [ ] No fact is duplicated across files.
- [ ] `markdownlint` and link check pass (CI runs them too).

## Tooling

```sh
# Lint all markdown
npx --yes markdownlint-cli2 "**/*.md"

# Check links in a single file (config lives in .github/)
npx --yes markdown-link-check -c .github/markdown-link-check.json <file>
```

## MCP setup (one-time, per clone)

`opencode.json` declares two MCP servers with **no secrets inside the repo**: `obsidian` and `github`. Both read their token from the repo-local `.secrets/` directory at startup.

1. **Obsidian** — install and enable the community plugin _Local REST API with MCP_ (Adam Coddington), then in its settings turn on **Enable HTTP server** (plain HTTP on `127.0.0.1:27123`). Keep Obsidian running while using the server. Find your API key under Settings → Local REST API.
2. **Tokens** — create the two files in the repo root once per clone:

   ```sh
   mkdir -p .secrets && chmod 700 .secrets
   printf '%s' '<obsidian-api-key>' > .secrets/obsidian-api-key
   printf '%s' '<github-token>'     > .secrets/github-token
   chmod 600 .secrets/*
   ```

   - The GitHub token is a fine-grained PAT (`github_pat_`) with **Read** access to `Contents`, `Issues`, `Pull requests`, and `Metadata` on the `sca-templates` repositories.

3. **Restart opencode** — config and `{file:}` values are read at startup, not hot-reloaded.

Quick check that both endpoints answer:

```sh
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:27123/          # 200
curl -s -o /dev/null -w '%{http_code}\n' \
  -H "Authorization: Bearer $(cat .secrets/github-token)" \
  https://api.github.com/user                                             # 200
```

## License

This repository is licensed under the MIT License (see [LICENSE](LICENSE)).
