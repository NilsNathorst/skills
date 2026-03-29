---
name: find-fixes
description: Proactively scan a codebase for small, safe, objectively good fixes — dead code, bad TypeScript types, code smells, deviations from project standards. Presents a numbered list for user review, then files selected items as GitHub issues via create-github-issue. Use when user wants to clean up a codebase, find quick wins, or run a code health scan.
---

# Find Fixes

Scan the codebase for small, safe, objectively good improvements. No refactors, no architectural changes — only concrete, low-risk fixes a developer would agree are unambiguously better.

## What to skip

Never flag files in: `node_modules/`, `dist/`, `build/`, `.next/`, `coverage/`, `*.d.ts`, auto-generated files (look for `// generated` or `// auto-generated` headers), and vendored code.

## Process

### 1. Understand the project

Read these files if they exist:
- `tsconfig.json` — strictness settings, paths, excluded files
- `.eslintrc*` / `eslint.config.*` / `biome.json` — what rules are already enforced
- `package.json` — scripts, dependencies, tech stack

Skim 5–10 source files across different parts of the codebase to get a feel for the conventions: naming, error handling patterns, import style, file structure.

### 2. Run the project's own tools first

If scripts exist in `package.json`, run them and collect output:

```bash
# TypeScript errors
npx tsc --noEmit 2>&1 | head -50

# Linting
npm run lint 2>&1 | head -50
# or: npx eslint src/ --max-warnings 0 2>&1 | head -50
# or: npx biome check . 2>&1 | head -50
```

Tool output is authoritative. Flag anything surfaced here as a finding — these are guaranteed real issues.

### 3. Scan for fixes by category

Work through each category. For each finding, verify it's real before adding it to your list.

Categories: **Dead code**, **TypeScript**, **Code smells**, **Standard deviations**.

See [REFERENCE.md](REFERENCE.md) for search patterns and grep commands per category.

### 4. Rejection criteria

**Do NOT flag:**

- Anything already caught and reported by the tools in step 2 (it's already tracked)
- Style preferences with no objective winner (`===` vs `==` is covered by eslint; don't re-flag)
- `any` where the type is genuinely complex or the fix requires understanding domain logic
- A pattern used consistently throughout the project, even if you'd personally do it differently
- Test files for most smell categories — tests have different conventions
- Anything that requires reading more than 2–3 files to understand the fix
- Anything where the fix might change runtime behavior (even slightly)

### 5. Prioritize and cap

Select the **best 3–7 findings**. Rank by:

1. **Certainty** — tool-reported issues first, then grep-confirmed issues
2. **Self-containedness** — fix lives in one file
3. **Obviousness** — a second developer would immediately agree it's wrong

### 6. Present the list

```
N. [Category] Short title
   File: path/to/file.ts (line X)
   Problem: one sentence
   Fix: one sentence — specific enough that no investigation is needed
```

Ask: "Which of these should I file as GitHub issues? (e.g. 1,2,3 or 'all' or 'none')"

### 7. File selected issues

For each selected fix, invoke the `create-github-issue` skill with the fix details.
