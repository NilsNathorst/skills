---
name: pr-review
description: Fetch a pull request diff and produce a structured code review grouped by severity, optionally posting it as a GitHub review. Use when user wants to review a PR, asks for a code review, or provides a PR number or URL.
---

# PR Review

Read a PR's diff and description, produce a structured review, and optionally post it to GitHub. No review is posted without user approval.

## Process

### 1. Identify the PR

If the user provided a PR number or URL, use it directly.

If not, try to detect the PR from the current branch:

```bash
gh pr view --json number,title,url,headRefName,baseRefName
```

If no open PR exists for the current branch, ask: "Which PR should I review? (number or URL)"

### 2. Fetch PR context

```bash
# PR metadata and description
gh pr view <number> --json title,body,author,baseRefName,headRefName,additions,deletions,changedFiles

# Full diff
gh pr diff <number>

# Existing reviews (to avoid duplicating feedback)
gh pr view <number> --comments --json comments,reviews \
  --jq '{comments: [.comments[] | {author: .author.login, body}], reviews: [.reviews[] | {author: .author.login, state, body}]}'
```

If the diff is very large (more than 1000 lines), focus on non-test source files first. Skim test files for coverage gaps but do not review test implementation line-by-line.

### 3. Read changed files in full

For each file in the diff, read the full file (not just the diff hunks) to understand surrounding context. A diff alone is not enough to judge correctness.

### 4. Review the changes

Evaluate every change against these criteria:

| Area | What to look for |
|---|---|
| **Correctness** | Logic errors, off-by-one, wrong variable, missing return, race conditions |
| **Edge cases** | Null/undefined inputs, empty arrays, boundary values, error paths |
| **Security** | Injection, auth bypass, secrets in code, unsafe deserialization, XSS |
| **Breaking changes** | Changed public API, renamed exports, removed fields, altered behavior |
| **Test coverage** | New logic without tests, changed behavior without updated tests |
| **Clarity** | Confusing naming, missing comments on non-obvious logic, dead code in diff |

Do NOT flag:
- Style issues already covered by linters (formatting, semicolons, quote style)
- Patterns consistent with the rest of the codebase, even if you would do it differently
- TODOs explicitly marked as follow-up work in the PR description

### 5. Present the review

Group findings by severity:

```
## PR Review: <PR title> (#<number>)

**Summary**: [1-2 sentence overall assessment]

### Blocking
Issues that must be fixed before merge.

1. **[Correctness]** `src/handler.ts:42` — Error response returns 200 instead of 400.
2. **[Security]** `src/auth.ts:15` — User input passed directly to SQL query.

### Suggestions
Improvements worth making but not blocking.

3. **[Edge case]** `src/utils.ts:78` — `parseDate` does not handle empty string.
4. **[Test coverage]** — No tests for the new `/api/reset` endpoint.

### Nits
Minor observations, take or leave.

5. **[Clarity]** `src/handler.ts:30` — `data` is vague; consider `validatedPayload`.
```

If there are no findings in a severity group, omit that group.

Ask: "Should I post this review to GitHub? (yes / yes with line comments / no)"

### 6. Post the review (if approved)

Determine the review action:

- **Blocking** items exist: post as `--request-changes`
- Only **suggestions** or **nits**: post as `--comment`
- No findings at all: post as `--approve`

```bash
gh pr review <number> --body "<review body>" --request-changes
```

For line-specific comments, use the GitHub API:

```bash
gh api repos/{owner}/{repo}/pulls/<number>/reviews --method POST \
  --field body="<summary>" \
  --field event="REQUEST_CHANGES" \
  --field 'comments=[{"path":"src/handler.ts","line":42,"body":"Error response returns 200 instead of 400."}]'
```

### 7. Confirm

Print the review URL or confirm the review was posted.
