---
title: {{service}}
type: infra
status: active
repo: {{repo}}
tags:
  - type/infra
  - domain/infrastructure
  - stack/{{stack}}
---

# {{service}}

> {{one line — what it is in the stack}}

## Role in the stack

{{what it does, dependencies, who uses it}}

## Access

| Endpoint | Address |
|---|---|

{{credentials source: Vault path / local .env}}

## Pointers

- Repo README: {{relative link `../<repo>/README.md`}}
- Related notes: {{wikilinks to services/contracts}}

## Status

{{planned | active | deprecated}}
