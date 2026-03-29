---
name: explain-codebase
description: Generate a structured walkthrough of a codebase or subsystem for developer onboarding, covering purpose, architecture, key files, data flow, and conventions. Use when user wants to understand a codebase, onboard to a project, asks "how does this work", or mentions "explain the codebase".
---

# Explain Codebase

Generate a structured walkthrough of a repo or subsystem suitable for onboarding a new developer.

## Process

### 1. Determine scope

If the user specified a focus area (e.g. "the auth system", "src/api/"), scope exploration to that area.

If no focus area was given, explore the entire repo at a high level.

Start exploring immediately — do not ask clarifying questions. If the scope turns out to be too broad, narrow to the most important subsystems and note what was excluded.

### 2. Read project metadata

```bash
# Manifest and config files
cat package.json pyproject.toml Cargo.toml go.mod 2>/dev/null

# Project documentation
cat README.md CONTRIBUTING.md ARCHITECTURE.md 2>/dev/null | head -200

# Directory structure (top 3 levels, excluding noise)
find . -maxdepth 3 -type f \
  -not -path '*/node_modules/*' \
  -not -path '*/.git/*' \
  -not -path '*/dist/*' \
  -not -path '*/build/*' \
  -not -path '*/__pycache__/*' \
  -not -path '*/target/*' \
  -not -path '*/vendor/*' \
  | head -100
```

### 3. Identify entry points

Find where execution starts:

- **Web apps**: main server file, route definitions, middleware chain
- **CLIs**: bin scripts, main command handler
- **Libraries**: public exports, index files
- **APIs**: route files, controller layer

Read each entry point file in full.

### 4. Trace key abstractions

From the entry points, follow imports to identify:

- **Core domain types** — the main data structures and entities
- **Key boundaries** — where does the system talk to the outside world (DB, APIs, filesystem)?
- **Processing pipeline** — how does a request/input flow through the system?
- **Configuration** — how is the system configured (env vars, config files, feature flags)?

Read 10-20 of the most important source files. Prioritize files that are imported by many others.

### 5. Identify conventions

Note patterns a new developer needs to follow:

- File and directory naming conventions
- Error handling approach
- Testing patterns (where tests live, framework, how to run)
- State management approach
- Authentication/authorization pattern (if applicable)

### 6. Generate the walkthrough

Present the walkthrough using the template in [REFERENCE.md](REFERENCE.md). Sections: Purpose, Architecture Overview, Key Files, Data Flow, Conventions, Where to Start.

### 7. Deliver output

Print the walkthrough to the conversation.

If the user asked to save it, write to the requested path (default: `CODEBASE_GUIDE.md`). Do not save to a file unless asked.
