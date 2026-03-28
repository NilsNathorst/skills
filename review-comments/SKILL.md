---
name: review-comments
description: Find the PR for the current branch, read all review comments, triage which need action, and implement the ones the user approves. Use when user wants to action PR feedback, respond to review comments, or mentions "review comments" or "PR comments".
---

# Review Comments

Find the open PR for the current branch, triage its review comments, and implement the approved ones.

## Process

### 1. Determine the repo and branch

**Single repo**: run `git branch --show-current` in the working directory.

**Multi-repo scenario**: If Claude Code is running in a parent directory containing multiple repos (e.g. `frontend/` and `backend/`), infer the active repo from the conversation — which repo's files have been discussed or edited? If uncertain, ask:

> "I'm going to look at the PR for `<branch-name>` in `<repo-name>` — is that right?"

### 2. Find the PR

```bash
gh pr view --json number,title,url,headRefName
```

If no open PR exists for the branch, tell the user and stop.

### 3. Fetch all review comments

```bash
# Review thread comments (inline code comments)
gh api repos/:owner/:repo/pulls/<PR-number>/comments --paginate \
  --jq '.[] | {id, path, line, body, user: .user.login, resolved: (.original_commit_id != .commit_id)}'

# General PR comments (top-level conversation)
gh pr view <PR-number> --comments --json comments \
  --jq '.comments[] | {author: .author.login, body, createdAt}'
```

### 4. Triage each comment

For each comment, read it and assess:

| Status | Meaning |
|---|---|
| **Needs fix** | A clear request to change code — wrong logic, missing case, bad type, style violation flagged by reviewer |
| **Already addressed** | The code has already been changed to address this (check the current state of the file) |
| **Question / discussion** | The reviewer is asking a question or discussing approach — no code change implied |
| **Out of scope** | Reviewer is noting something unrelated to this PR, or flagging a pre-existing issue |

To assess "already addressed", read the current file at the commented line and judge whether the concern still applies.

### 5. Present the list

Show all comments grouped by status. For **Needs fix** items, be specific about what the fix is:

```
NEEDS FIX
1. [reviewer] path/to/file.ts:42
   Comment: "This should handle the null case"
   Fix: Add a null check before accessing `.value`

2. [reviewer] path/to/file.ts:87
   Comment: "Wrong return type, should be `string | null` not `string`"
   Fix: Update return type annotation

ALREADY ADDRESSED
3. [reviewer] path/to/other.ts:12
   Comment: "Rename this to be more descriptive"
   → Already renamed in current code

QUESTION / NO ACTION
4. [reviewer] (general comment)
   "Why did you choose this approach over X?"
   → Discussion, no code change needed
```

Ask: "Which should I implement? (e.g. 1,2 or 'all' or 'none')"

### 6. Implement selected fixes

Work through each selected fix one at a time. Make only the change described — do not fix unrelated things you notice along the way.

After all changes are made, suggest a commit message:

> Suggested commit: `address PR review comments (null check, return type fix)`