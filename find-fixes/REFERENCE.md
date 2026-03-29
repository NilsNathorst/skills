# Search Patterns by Category

## Dead code

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

## TypeScript

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

## Code smells

**Magic values used more than once:**
```bash
# Magic numbers
grep -rn "[^a-zA-Z][0-9]\{2,\}[^0-9]" src/ --include="*.ts" --include="*.tsx" | grep -v "test\|spec\|\.d\.ts"

# Magic strings (look for repeated string literals)
grep -roh '"[a-zA-Z][a-zA-Z_-]\{4,\}"' src/ --include="*.ts" --include="*.tsx" | sort | uniq -d
```

**Copy-pasted blocks** — look for identical multi-line patterns by reading files that are structurally similar to each other (same directory, same naming pattern). If two files have near-identical sections of 10+ lines, flag it.

## Standard deviations

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
