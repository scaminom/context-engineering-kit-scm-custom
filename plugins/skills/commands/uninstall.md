---
description: Remove an installed skill — deletes .claude/skills/<name>/ directory
argument-hint: <name>  (e.g. dotnet-expert, design-pattern-advisor)
---

# Uninstall Skill

Remove a per-project skill. Takes one argument: the skill name (directory name under `.claude/skills/`).

## Step 1 — Validate argument

If no argument provided, tell the user: *"Usage: `/skills:uninstall <name>`. Run `/skills:list` to see installed skills."* and stop.

Let `NAME` = the argument.

## Step 2 — Locate skill directory

Check whether `.claude/skills/<NAME>/` exists (as a directory with at least `SKILL.md` inside).

- If not found, report *"`.claude/skills/<NAME>/` not found. Nothing to uninstall."* and stop.

## Step 3 — Confirm

Ask: *"Remove `.claude/skills/<NAME>/` and all its files (including any `references/` subdir)? (y/n)"*. Stop on `n`.

## Step 4 — Delete

```bash
rm -rf .claude/skills/<NAME>
```

## Step 5 — Report

- Skill directory: deleted / not found.
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is deregistered from this session."*
- Suggest `/skills:list` to confirm.
