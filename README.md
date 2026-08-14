# sca-docs

Central Obsidian knowledge base for the sca ecosystem: microservices, gRPC & Kafka contracts, shared @sca/* packages, self-hosted infrastructure, and the super template. Every fact lives in one place; this vault links to repo docs instead of duplicating them.

## What this is

The vault is the single home for the ecosystem's **topology** (who is what, what talks to what) and **conventions** (naming, frontmatter, tags). Depth lives in each repo's own docs; vault notes link out to them with relative paths, never copy them.

- One fact, one place — a fact is edited in exactly one file.
- Docs-as-code — all changes land through a pull request with review.
- English only — notes, commits, and PR descriptions.

## Repository layout

```text
00-ecosystem/        Home, super template, conventions
01-services/         Service catalog (sca-* microservices)
02-contracts/        gRPC contracts (grpc/) + Kafka events (events/)
03-connections-map/  The ecosystem graph (mermaid + matrices)
04-infrastructure/   Self-hosted topology + multi-cloud
05-packages/         @sca/* shared packages
06-decisions/        Ecosystem-level ADRs
99-glossary/         Ubiquitous language (one line per term)
_templates/          Note templates
_config/             Metadata: repo locations, tagging and naming
```

## Documentation

The vault is built incrementally: each area gets its catalog (`INDEX.md`) as the ecosystem docs land, and `03-connections-map/connection-map.md` is regenerated whenever a contract or service changes. Start at `00-ecosystem/` for the home and conventions. _(Links land here as areas are published.)_

## Tooling

Config is committed so any clone behaves the same:

| Piece         | Where                                                          | What it does                                                                                            |
| ------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Agent skills  | `.opencode/skills/` + `.claude/skills/`                        | `new-vault-note` scaffolds a note from a template; `sync-catalogs` keeps INDEX + connection map in sync |
| Lint & checks | `.markdownlint-cli2.jsonc`, `.github/markdown-link-check.json` | markdownlint (gitignore-aware) + link check, local and CI                                               |
| Editor        | `.vscode/settings.json`                                        | markdown formatting, area folder icons, search excludes                                                 |

See [CONTRIBUTING](CONTRIBUTING.md#tooling) for the check commands.

## MCP servers

Two remote MCP servers are declared in `opencode.json` — no secrets in the repo; both authenticate with a Bearer token read per clone from `.secrets/` (gitignored) via `{file:.secrets/...}`.

| Server     | Endpoint                             | Token file                  |
| ---------- | ------------------------------------ | --------------------------- |
| `obsidian` | `http://127.0.0.1:27123/mcp/`        | `.secrets/obsidian-api-key` |
| `github`   | `https://api.githubcopilot.com/mcp/` | `.secrets/github-token`     |

Configure once per clone (full walkthrough in [MCP setup](CONTRIBUTING.md#mcp-setup-one-time-per-clone)):

1. Create the two token files:

   ```sh
   mkdir -p .secrets && chmod 700 .secrets
   printf '%s' '<obsidian-api-key>' > .secrets/obsidian-api-key
   printf '%s' '<github-token>'     > .secrets/github-token
   chmod 600 .secrets/*
   ```

   The GitHub token is a fine-grained PAT (`github_pat_`) with **Read** access to `Contents`, `Issues`, `Pull requests`, and `Metadata` on the `sca-node-template` repositories.
2. Obsidian — enable the _Local REST API with MCP_ plugin and turn on **Enable HTTP server** (plain HTTP on `127.0.0.1:27123`); keep Obsidian running.
3. Restart opencode — config and `{file:}` values are read at startup. Then `opencode mcp list` should show both servers `connected`.

## Getting started

1. Clone and open the folder as an **Obsidian vault** (the folder is the vault).
2. Configure MCP tokens once per clone (see [MCP servers](#mcp-servers)).
3. Author notes with the `new-vault-note` skill; keep catalogs in sync with `sync-catalogs`.
4. Run `npx --yes markdownlint-cli2 "**/*.md"` and the link check before pushing.

## Agent guides

- [AGENTS.md](AGENTS.md) — conventions and commands for AI agents working here.
- [CLAUDE.md](CLAUDE.md) — Claude Code specifics (skills, MCP, guardrails).
- [CONTRIBUTING.md](CONTRIBUTING.md) — contribution flow, definition of done, tooling, MCP setup.

## License

MIT — see [LICENSE](LICENSE).
