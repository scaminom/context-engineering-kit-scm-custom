---
description: Audit .claude/rules/ and CLAUDE.md for consistency — orphans, broken pointers, missing skills, dead globs
argument-hint: No arguments
---

# Rules Doctor

Audit rule installation in this project. Report issues with concrete fix suggestions. Read-only — does not modify files.

## Check 1 — Orphan rules

Rule files in `.claude/rules/` without a pointer under `## Agent docs` in `CLAUDE.md`.

Use Glob on `.claude/rules/*.md` and parse `CLAUDE.md` for pointer lines. Diff the sets.

For each orphan: suggest *"Add pointer line manually, or re-run `/rules:setup-<slug>` to regenerate."*

## Check 2 — Broken pointers

Pointer lines in CLAUDE.md (under `## Agent docs`) referencing `.claude/rules/<slug>.md` files that don't exist.

Parse pointer lines with a regex like `\`.claude/rules/([^`]+)\``, then check each file's existence with Glob.

For each broken pointer: suggest *"Remove the line from CLAUDE.md or run `/rules:setup-<slug>` to reinstall."*

## Check 3 — Missing skill references

Rules whose body contains a `## Skill reference` section mentioning skill name(s) that are NOT installed in `.claude/skills/`.

For each rule with skill-ref:
- Grep the rule body for backtick-quoted skill names (e.g. `` `dotnet-expert` ``) within the Skill reference section.
- For each referenced skill, check if `.claude/skills/<skill>/SKILL.md` exists.
- If missing, note it.

For each: suggest *"Install with `/skills:setup-<skill>` (if the skill command exists in this marketplace) or note that the referenced skill is unavailable in this project."*

## Check 4 — Dead globs

Rules whose `globs:` patterns match zero files in the project.

For each rule:
- Parse `globs:` from frontmatter.
- For each pattern, run Glob. Count matches.
- If total matches across all patterns is zero, flag it.

For each: suggest *"The rule's globs don't match any files. Re-run `/rules:setup-<slug>` to re-detect the stack, or edit the globs manually."*

## Report

Output a sectioned report:

```
# Rules Doctor Report

## Summary
- Total rules: N
- Issues found: N (orphans: N, broken: N, missing skills: N, dead globs: N)
- Status: OK / ATTENTION NEEDED

## Issues

### Orphan rules (N)
- `slug1` — path, suggested fix
- ...

### Broken pointers (N)
- ...

### Missing skill references (N)
- ...

### Dead globs (N)
- ...
```

If `Issues found = 0`, report a single-line *"All good. N rules, all consistent."*
