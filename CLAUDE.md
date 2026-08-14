# sca-docs

Claude Code memory for this repository.

## Agent guide

See @AGENTS.md — it is the single source for conventions (English only, one fact
one place, vault layout, naming, frontmatter, tag taxonomy, links) and the check
commands. Do not duplicate that content here.

## Claude Code specifics

- Skills for this vault live in `.claude/skills/` (mirrored from
  `.opencode/skills/`). Keep both in sync when editing a skill.
- MCP servers (`obsidian`, `github`) are declared in `opencode.json` for
  opencode. To use them from Claude Code, add them yourself, e.g.:

  ```sh
  claude mcp add obsidian -- uvx mcp-obsidian -e OBSIDIAN_API_KEY=$OBSIDIAN_API_KEY
  ```

- The `obsidian` MCP server needs the Local REST API community plugin enabled in
  Obsidian, the `OBSIDIAN_API_KEY` env var exported, and Obsidian running.
  Without it, the server starts but every tool call fails.
- `github` MCP uses `{env:GITHUB_TOKEN}`; export that token to enable it.

## Repo guardrails

- This vault is markdown and YAML only — no code, no build steps.
- Never commit secrets: `.env` is gitignored and API keys come from the
  environment (`{env:VAR}` interpolation), never from the repo.
- The README is written last (per project decision); keep it in English.
