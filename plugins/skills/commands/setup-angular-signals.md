---
description: Install the angular-signals skill into this project's .claude/skills/ directory
argument-hint: No arguments
---

# Install angular-signals skill (per-project)

Copy the bundled `angular-signals` skill into `<project>/.claude/skills/angular-signals/` so it activates only in this project.

**Purpose**: Angular 16+ reactive state with fine-grained signals and zone-less change detection

## Step 1 — Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

If empty, tell the user the marketplace is not installed and stop.

## Step 2 — Check for existing install

If `<project>/.claude/skills/angular-signals/` already exists, ask: *"Already installed. Overwrite? (y/n)"* — stop on `n`.

## Step 3 — Copy

```bash
mkdir -p .claude/skills
cp -r "$MARKETPLACE/plugins/skills/sources/angular-signals" .claude/skills/
```

## Step 4 — Verify

```bash
ls -la .claude/skills/angular-signals/
head -5 .claude/skills/angular-signals/SKILL.md
```

## Step 5 — Report

- Source path used.
- Target path written.
- Files installed (count + any `references/` subdir).
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is discovered. Once active, the agent can invoke it in this project."*
