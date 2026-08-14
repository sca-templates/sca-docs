# Agent guide — sca-docs

Instructions for AI agents and assistants working in this repository.

## Language

Write all new content, commit messages, and PR descriptions in **English**. The vault is the single home for ecosystem topology; repo docs keep the depth.

## Scope

This repo is the Obsidian vault of the `sca` ecosystem. It describes topology and links to repo docs; it **never copies** repo content (one fact, one place).

## Conventions to honor

- **Vault layout** — `00-ecosystem/`, `01-services/`, `02-contracts/`, `03-connections-map/`, `04-infrastructure/`, `05-packages/`, `06-decisions/`, `99-glossary/`, `_templates/`, `_config/`.
- **Naming** — folders `NN-area/`, files kebab-case English, ADRs `adr-NNN-kebab-title.md`, contracts `grpc-<domain>.md` / `evt-<topic>.md`.
- **Frontmatter** — required on vault notes (`title`, `type`, `status`, `repo`, `tags`). Use only the closed tag taxonomy: `type/*`, `domain/*`, `stack/*`, `connectivity/*`, `status/*`.
- **Links** — wikilinks `[[…]]` inside the vault; relative markdown links `../<repo>/path.md` toward other repos (never absolute paths).
- **Keep catalogs in sync** — update `INDEX.md` tables and regenerate `03-connections-map/connection-map.md` whenever a contract or service note changes.

## Commands

```sh
# Lint all markdown
npx --yes markdownlint-cli2 "**/*.md"

# Check links in a single file
npx --yes markdown-link-check -c .github/markdown-link-check.json <file>
```

Do not run `codegraph init` (indexing is the user's decision). This repository is not a code project; changes are markdown and YAML only.

## MCP servers

`opencode.json` defines two remote MCP servers (no secrets in the repo): `obsidian` (the Local REST API plugin's endpoint on `127.0.0.1:27123`) and `github`. Both authenticate with tokens read per clone from the repo-local `.secrets/obsidian-api-key` and `.secrets/github-token`. If a server's tools are missing in a session, check those files and that Obsidian is running with the plugin enabled (see CONTRIBUTING → MCP setup).
