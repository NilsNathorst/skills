# skills

A personal collection of Claude Code skills by [NilsNathorst](https://github.com/NilsNathorst). Several skills are based on [mattpocock/skills](https://github.com/mattpocock/skills).

## Install

Install all skills:

```bash
npx skills add NilsNathorst/skills
```

Install a single skill:

```bash
npx skills add NilsNathorst/skills --skill grill-me
```

## Update

Check for updates and pull the latest versions:

```bash
npx skills check     # see what's changed
npx skills update    # update all skills
```

## Skills

| Skill | Description |
| --- | --- |
| [grill-me](./grill-me/SKILL.md) | Relentlessly interviews you about a plan or design, walking down the decision tree one question at a time. |
| [ubiquitous-language](./ubiquitous-language/SKILL.md) | Extracts a DDD-style ubiquitous language glossary from the conversation, flags ambiguities, and saves to `UBIQUITOUS_LANGUAGE.md`. |
| [write-a-skill](./write-a-skill/SKILL.md) | Guides you through creating a new agent skill with proper structure and progressive disclosure. |
| [git-guardrails-claude-code](./git-guardrails-claude-code/SKILL.md) | Sets up a PreToolUse hook that blocks destructive git operations (push, hard reset, force clean, etc.). |
| [find-fixes](./find-fixes/SKILL.md) | Scans the codebase for small, safe fixes (dead code, bad TS types, code smells, standard deviations), presents a numbered list, and files selected items as GitHub issues. |
| [create-github-issue](./create-github-issue/SKILL.md) | Files a well-structured GitHub issue with explicit Claude instructions. Used by `find-fixes` and callable standalone. |
| [fix-github-issue](./fix-github-issue/SKILL.md) | Reads a GitHub issue, implements the fix, and opens a PR. |
| [review-comments](./review-comments/SKILL.md) | Finds the PR for the current branch, triages review comments (needs fix / already addressed / discussion), implements approved ones, and suggests a commit message. |
| [dep-audit](./dep-audit/SKILL.md) | Scans for outdated, deprecated, or vulnerable dependencies, triages by severity, and optionally updates selected ones. |
| [pr-review](./pr-review/SKILL.md) | Fetches a PR diff and produces a structured code review grouped by severity (blocking / suggestion / nit), optionally posting it to GitHub. |
| [explain-codebase](./explain-codebase/SKILL.md) | Generates a structured walkthrough of a codebase or subsystem covering purpose, architecture, key files, data flow, and conventions. |
| [obsidian-vault](./obsidian-vault/SKILL.md) | Searches, creates, and manages notes in an Obsidian vault with wikilinks and index notes. Asks for vault path on first use. |
