---
name: create-github-issue
description: Create a well-structured GitHub issue with a clear problem description and explicit instructions for Claude to implement the fix. Use when you have a fix or task ready to file as a GitHub issue.
---

# Create GitHub Issue

File a GitHub issue with enough detail that Claude (or a developer) can implement it without further investigation.

## Process

### 1. Gather the details

You need:
- **Title**: short, specific (e.g. "Remove unused `formatDate` export in utils.ts")
- **Problem**: what is wrong or suboptimal, and where
- **Fix**: the concrete change to make — specific enough that no investigation is needed
- **Category**: (Dead code / TypeScript / Code smell / Standard deviation / Bug / Feature)

If called from another skill (e.g. `find-fixes`), these details are already provided. If called directly by the user, ask for them.

### 2. Create the issue

Use `gh issue create` with this template:

```
## Problem

[Clear description of what is wrong and where. Include file path and line number if known.]

## Fix

[Concrete description of the change to make. Specific enough to implement without re-investigating.]

## Instructions for Claude

1. Find the code described above
2. Apply the fix exactly as described
3. Make sure existing tests still pass
4. Open a PR with a short title and a one-line description of what was changed and why

## Acceptance Criteria

- [ ] Fix is applied
- [ ] No unrelated code is changed
- [ ] Existing tests pass
- [ ] PR is open
```

Run:
```bash
gh issue create --title "<title>" --body "<body>" --label "<category>"
```

### 3. Confirm

Print the issue URL after creation.
