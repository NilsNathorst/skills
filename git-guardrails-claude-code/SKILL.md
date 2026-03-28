---
name: git-guardrails-claude-code
description: Set up a PreToolUse hook that blocks destructive git operations (push, hard reset, force clean, branch deletion, working directory restore). Use when user wants to prevent Claude from running dangerous git commands or wants to set up git guardrails.
---

# Git Guardrails for Claude Code

This setup guide enables a safety mechanism that prevents Claude from executing destructive git operations.

## Core Functionality

The PreToolUse hook intercepts bash commands and blocks dangerous git actions including pushes, hard resets, forced deletions, and file checkouts. When blocked, Claude receives a message indicating it lacks authority to execute those commands.

## Installation Process

**Step 1: Scope Decision**
First, ask whether the user prefers project-level (`.claude/settings.json`) or system-wide (`~/.claude/settings.json`) installation.

**Step 2: Script Placement**
Copy the blocking script from `scripts/block-dangerous-git.sh` to either `.claude/hooks/` (project) or `~/.claude/hooks/` (global), then make it executable.

**Step 3: Configuration**
Add the PreToolUse hook to the appropriate settings file, specifying the script path. If settings already exist, merge the new hook configuration rather than replacing existing content.

**Step 4: Customization**
Offer to modify the blocked command patterns based on user preferences.

**Step 5: Testing**
Verify functionality by piping a test git push command through the script—it should exit with code 2 and display a blocked message.

## Protected Operations

The guardrails prevent `git push` variants, `--hard` resets, forced file cleaning, branch deletion, and working directory restoration without user confirmation.
