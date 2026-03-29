# Review Criteria

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

# Output Template

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
