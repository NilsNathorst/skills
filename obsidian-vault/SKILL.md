---
name: obsidian-vault
description: Search, create, and manage notes in the Obsidian vault with wikilinks and index notes. Use when user wants to find, create, or organize notes in Obsidian.
---

# Obsidian Vault

## Vault location

Before doing anything, resolve the vault path:

1. Check if `~/.claude/skills/obsidian-vault/.vault-path` exists. If it does, read the path from it.
2. If the file does not exist, ask the user: "Where is your Obsidian vault? (absolute path)"
3. Save their answer to `~/.claude/skills/obsidian-vault/.vault-path` (just the path, nothing else).

Use this path as `<VAULT_PATH>` in all commands below.

## Naming conventions

- **Index notes**: aggregate related topics (e.g., `Skills Index.md`, `RAG Index.md`)
- **Title case** for all note names
- No folders for organization - use links and index notes instead

## Linking

- Use Obsidian `[[wikilinks]]` syntax: `[[Note Title]]`
- Notes link to dependencies/related notes at the bottom
- Index notes are just lists of `[[wikilinks]]`

## Workflows

### Search for notes

```bash
# Search by filename
find "<VAULT_PATH>" -name "*.md" | grep -i "keyword"

# Search by content
grep -rl "keyword" "<VAULT_PATH>" --include="*.md"
```

Or use Grep/Glob tools directly on the vault path.

### Create a new note

1. Use **Title Case** for filename
2. Write content as a unit of learning (per vault rules)
3. Add `[[wikilinks]]` to related notes at the bottom
4. If part of a numbered sequence, use the hierarchical numbering scheme

### Find related notes

Search for `[[Note Title]]` across the vault to find backlinks:

```bash
grep -rl "\\[\\[Note Title\\]\\]" "<VAULT_PATH>"
```

### Find index notes

```bash
find "<VAULT_PATH>" -name "*Index*"
```
