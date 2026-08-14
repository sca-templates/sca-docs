---
name: sync-catalogs
description: Keep vault catalogs in sync. Use after creating or editing a service, contract, infra, or package note, or when the user asks to update the INDEX, regenerate the connection map, or keep catalogs in sync.
---

# Sync catalogs

The vault keeps manual catalogs (no Dataview). One fact, one place: this skill
only updates tables and maps, it never introduces new facts.

## Steps

1. Update the area `INDEX.md` table with a row for the changed note (name, what
   it is, status). Areas: `01-services/INDEX.md`, `02-contracts/INDEX.md`,
   `04-infrastructure/INDEX.md`, `05-packages/INDEX.md`, `06-decisions/INDEX.md`.
2. Regenerate `03-connections-map/connection-map.md` if a contract or service
   note changed: mermaid graph + gRPC and Kafka matrices, derived from the
   contract and service notes.
3. Run `npx --yes markdownlint-cli2 '**/*.md'` and fix reported issues.
