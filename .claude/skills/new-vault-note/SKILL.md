---
name: new-vault-note
description: Scaffold a new vault note from the matching template. Use when the user asks to create a note for a service, gRPC contract, Kafka event, infrastructure component, @sca/* package, ADR, glossary term, MOC, or Home (e.g. "create a note for sca-auth", "nueva nota de contrato").
---

# New vault note

Follow the vault conventions in AGENTS.md and the closed taxonomy in
`_config/tagging-and-naming.md`.

## Steps

1. Determine the note type: `home`, `moc`, `service`, `contract-grpc`,
   `contract-event`, `infra`, `package`, `adr`, or `glossary`.
2. Copy the matching template from `_templates/template-<type>.md`.
3. Place the note in the right area, with the right name:
   - service → `01-services/<sca-name>.md`
   - gRPC contract → `02-contracts/grpc/grpc-<domain>.md`
   - Kafka event → `02-contracts/events/evt-<topic>.md`
   - infra → `04-infrastructure/<service>.md`
   - package → `05-packages/<name>.md`
   - ADR → `06-decisions/adr-NNN-kebab-title.md`
   - glossary → `99-glossary/<term>.md`
   - home / MOC → `00-ecosystem/<name>.md`
4. Fill frontmatter (`title`, `type`, `status`, `repo`, `tags`) using only the
   closed taxonomy; resolve `repo` in `_config/repo-locations.md`.
5. Links: wikilinks `[[…]]` inside the vault; relative markdown links
   `../<repo>/path.md` toward repo docs; never absolute paths.
6. Run `npx --yes markdownlint-cli2 '**/*.md'` and fix reported issues.
7. If the note is a service, contract, infra, or package, run the
   `sync-catalogs` skill to update the area `INDEX.md` and the connection map.
