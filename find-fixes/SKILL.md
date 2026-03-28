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

Work through each category using the search strategies below. For each finding, verify it's real before adding it to your list.

---

#### Dead code

**Unused exports** — find exports then check if anything imports them:
```bash
# Find all named exports
grep -rn "^export " src/ --include="*.ts" --include="*.tsx"

# For each symbol found, check if it's imported anywhere
grep -rn "symbolName" src/ --include="*.ts" --include="*.tsx"
```
A symbol is dead if the only match is its own definition.

**Commented-out code** — look for code-shaped comments:
```bash
grep -rn "^[[:space:]]*//" src/ --include="*.ts" --include="*.tsx" | grep -E "(const|let|var|function|return|if|import)"
```

**Unreachable branches:**
```bash
grep -rn "if (false\|if(false" src/ --include="*.ts" --include="*.tsx"
```

---

#### TypeScript

**`any` usage:**
```bash
grep -rn ": any\b\|as any\b\|<any>" src/ --include="*.ts" --include="*.tsx"
```
Only flag `any` where the correct type is obvious from surrounding context. Skip if it's genuinely hard to type.

**Suppression comments:**
```bash
grep -rn "@ts-ignore\|@ts-expect-error" src/ --include="*.ts" --include="*.tsx"
```
Check each one — if the error it suppresses no longer exists (i.e. removing the comment causes no error), it's dead.

**Type assertions that should be narrowing:**
```bash
grep -rn " as [A-Z]\w*" src/ --include="*.ts" --include="*.tsx"
```
Flag assertions where a type guard or proper narrowing is clearly the right fix.

---

#### Code smells

**Magic values used more than once:**
```bash
# Magic numbers
grep -rn "[^a-zA-Z][0-9]\{2,\}[^0-9]" src/ --include="*.ts" --include="*.tsx" | grep -v "test\|spec\|\.d\.ts"

# Magic strings (look for repeated string literals)
grep -roh '"[a-zA-Z][a-zA-Z_-]\{4,\}"' src/ --include="*.ts" --include="*.tsx" | sort | uniq -d
```

**Copy-pasted blocks** — look for identical multi-line patterns by reading files that are structurally similar to each other (same directory, same naming pattern). If two files have near-identical sections of 10+ lines, flag it.

---

#### Standard deviations

This category requires establishing the convention first.

For each of these, read enough files to determine the project standard, then search for outliers:

- **Error handling**: Is it `try/catch`, `Result` types, or `.catch()`? Search for the minority pattern.
- **Async style**: Is it `async/await` or `.then()`? Search for `.then(` if async/await dominates.
- **Import style**: Named imports, default imports, or barrel imports? Search for the outlier pattern.
- **Naming**: camelCase functions, PascalCase types — search for violations:
  ```bash
  # Functions that start with uppercase (should be PascalCase components or classes only)
  grep -rn "^export function [A-Z]" src/ --include="*.ts"

  # Types/interfaces that start with lowercase
  grep -rn "^export type [a-z]\|^export interface [a-z]" src/ --include="*.ts"
  ```

---

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
