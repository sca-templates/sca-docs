# sca-docs

Claude Code memory for this repository.

## Agent guide

See @AGENTS.md — it is the single source for conventions (English only, one fact one place, vault layout, naming, frontmatter, tag taxonomy, links) and the check commands. Do not duplicate that content here.

## Claude Code specifics

- Skills for this vault live in `.claude/skills/` (mirrored from `.opencode/skills/`). Keep both in sync when editing a skill.
- MCP servers (`obsidian`, `github`) are declared in `opencode.json` for opencode; both authenticate with tokens read from the repo-local `.secrets/` dir via `{file:.secrets/...}` interpolation. To use them from Claude Code, add them yourself (see CONTRIBUTING → MCP setup for the exact commands and URLs).
- The `obsidian` server is the plugin's built-in MCP endpoint (`http://127.0.0.1:27123/mcp/`): it needs the Local REST API community plugin enabled in Obsidian, the "Enable HTTP server" toggle on, Obsidian running, and the key present in `.secrets/obsidian-api-key`.
- `github` MCP uses the token in `.secrets/github-token`.

## Repo guardrails

- This vault is markdown and YAML only — no code, no build steps.
- Never commit secrets: `.env` and the repo-local `.secrets/` dir hold credentials, referenced from config via `{file:}` — never written to the repo.
- The README is the repo entry point; keep it in English and up to date.
