---
description: Remove an installed rule — deletes .claude/rules/<slug>.md
argument-hint: <slug>  (e.g. solid, dotnet, clean-architecture-ddd)
---

# Uninstall Rule

Remove a rule from the project. Takes one argument: the rule slug (without `.md` extension).

## Step 1 — Validate argument

If no argument was provided, tell the user: *"Usage: `/rules:uninstall <slug>`. Run `/rules:list` to see installed rules."* and stop.

Let `SLUG` = the argument.

## Step 2 — Locate rule file

Check whether `.claude/rules/<SLUG>.md` exists.

- If it does NOT exist, tell the user: *"`.claude/rules/<SLUG>.md` not found. Nothing to uninstall."* and stop.

## Step 3 — Confirm

Ask: *"Remove `.claude/rules/<SLUG>.md`? (y/n)"*. Stop on `n`.

## Step 4 — Delete rule file

```bash
rm .claude/rules/<SLUG>.md
```

## Step 5 — Legacy pointer cleanup (best-effort)

Read `CLAUDE.md` if it exists. If a legacy pointer line under `## Agent docs` references `` `.claude/rules/<SLUG>.md` ``, use the Edit tool to delete that line. Leave the `## Agent docs` heading alone even if it becomes empty (user may keep it for other docs).

Pointer lines are no longer required — rules auto-load from disk — so this step just cleans up old installs.

## Step 6 — Report

- Rule file: deleted / not found.
- CLAUDE.md legacy pointer: removed / not found / file missing.
- Suggest `/rules:list` to confirm.
