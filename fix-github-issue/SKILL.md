---
name: fix-github-issue
description: Read a GitHub issue, implement the fix, and open a pull request. Use when user wants to action a GitHub issue, implement a filed fix, or mentions "fix issue" or provides a GitHub issue URL or number.
---

# Fix GitHub Issue

Read a GitHub issue and implement it cleanly. Minimize questions — the issue should contain everything needed.

## Process

### 1. Get the issue

If the user provided an issue number or URL, fetch it:

```bash
gh issue view <number> --json title,body
```

If not, ask: "Which issue number should I fix?"

### 2. Understand the fix

Read the issue in full. The **Instructions for Claude** and **Fix** sections tell you exactly what to change. If either is missing or ambiguous, explore the codebase to fill in the gaps — do NOT ask the user unless truly stuck.

### 3. Implement

- Make only the change described. Do not fix unrelated things you notice along the way.
- Keep the diff as small as possible.
- Run the project's lint and type-check commands if they exist (`package.json` scripts are the source of truth).
- Run existing tests. If they fail, fix the failure only if it's directly caused by your change.

### 4. Open a PR

```bash
gh pr create --title "<short title>" --body "$(cat <<'EOF'
## What

[One sentence: what was changed]

## Why

[One sentence: why, referencing the issue]

Closes #<issue-number>
EOF
)"
```

### 5. Confirm

Print the PR URL.
