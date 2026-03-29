---
name: dep-audit
description: Scan for outdated, deprecated, or vulnerable dependencies, triage by severity, and optionally update selected ones. Use when user wants to audit dependencies, check for vulnerabilities, update packages, or mentions "dependency health" or "outdated packages".
---

# Dependency Audit

Detect the package manager, run audit and outdated checks, and present a prioritized triage. No updates without user approval.

## Process

### 1. Detect the package manager

Check which lock files and manifests exist in the project root:

```bash
ls package-lock.json pnpm-lock.yaml yarn.lock Cargo.lock go.sum requirements.txt poetry.lock Pipfile.lock pyproject.toml 2>/dev/null
```

If multiple are present (e.g. a monorepo), handle each ecosystem separately and label the results.

### 2. Run audit and outdated commands

Run the appropriate commands for each detected ecosystem. Capture output — do not stop on non-zero exit codes (audit commands return non-zero when vulnerabilities exist). If a tool is not installed, note it and skip — do not install tools.

See [REFERENCE.md](REFERENCE.md) for ecosystem-specific commands.

### 3. Categorize findings

Sort every finding into exactly one category:

| Category | Criteria | Action |
|---|---|---|
| **CRITICAL** | Known vulnerability with a fix available | Update immediately |
| **WARNING** | Deprecated package, or vulnerability with no fix yet | Review — may need replacement |
| **MAJOR UPDATE** | Major version bump available (breaking changes likely) | Review changelog before updating |
| **SAFE UPDATE** | Minor or patch version bump available | Low risk, safe to batch-update |

### 4. Present the triage

```
CRITICAL (update immediately)
1. lodash 4.17.20 -> 4.17.21 — Prototype pollution (CVE-2021-23337)
2. express 4.17.1 -> 4.18.2 — Open redirect vulnerability

WARNING (review needed)
3. request 2.88.2 — DEPRECATED, no replacement in package.json
   Suggested replacement: undici or node-fetch

MAJOR UPDATE (breaking changes)
4. typescript 4.9.5 -> 5.4.2 — Major version bump
5. next 13.5.1 -> 14.1.0 — Major version bump

SAFE UPDATE (minor/patch)
6. zod 3.22.2 -> 3.22.4 — Patch
7. prettier 3.1.0 -> 3.2.1 — Minor
```

If no findings exist in a category, omit that category entirely.

Ask: "Which should I update? (e.g. 1,2,6,7 or 'all safe' or 'all' or 'none')"

### 5. Apply selected updates

Run the appropriate update command per ecosystem. See [REFERENCE.md](REFERENCE.md) for ecosystem-specific commands.

For major version bumps, check for a migration guide first and warn about known breaking changes before proceeding.

After all updates, run the project's test command if one exists and report the result.

### 6. Summary

Print what was updated, what was skipped, and suggest a commit message:

> Suggested commit: `update dependencies: patch lodash CVE-2021-23337, bump minor versions`
