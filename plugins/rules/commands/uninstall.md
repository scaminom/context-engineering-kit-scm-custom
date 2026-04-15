---
description: Remove an installed rule — deletes .claude/rules/<slug>.md and its pointer in CLAUDE.md
argument-hint: <slug>  (e.g. solid, dotnet, clean-architecture-ddd)
---

# Uninstall Rule

Remove a rule from the project. Takes one argument: the rule slug (without `.md` extension).

## Step 1 — Validate argument

If no argument was provided, tell the user: *"Usage: `/rules:uninstall <slug>`. Run `/rules:list` to see installed rules."* and stop.

Let `SLUG` = the argument.

## Step 2 — Locate rule file

Check whether `.claude/rules/<SLUG>.md` exists.

- If it does NOT exist, tell the user: *"`.claude/rules/<SLUG>.md` not found. Nothing to uninstall."* and stop (don't touch CLAUDE.md either — the pointer might be stale; run `/rules:doctor` to clean it up).

## Step 3 — Confirm

Ask: *"Remove `.claude/rules/<SLUG>.md` and its pointer in CLAUDE.md? (y/n)"*. Stop on `n`.

## Step 4 — Delete rule file

```bash
rm .claude/rules/<SLUG>.md
```

## Step 5 — Remove pointer from CLAUDE.md

Read `CLAUDE.md`. Locate any line under `## Agent docs` that references `` `.claude/rules/<SLUG>.md` `` (exact substring match).

- If found, use the Edit tool to delete that entire line.
- If the `## Agent docs` section becomes empty after removal, leave the heading alone (don't delete headings — preserves user edits).
- If no matching line exists, note it in the report but proceed (the file was already gone from the pointer list).

## Step 6 — Report

- Rule file: deleted / not found.
- CLAUDE.md pointer: removed / not found / file missing.
- Suggest `/rules:list` to confirm.
