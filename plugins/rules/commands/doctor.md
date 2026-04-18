---
description: Audit .claude/rules/ for consistency — frontmatter shape, missing skills, dead paths
argument-hint: No arguments
---

# Rules Doctor

Audit rule installation in this project. Read-only — does not modify files.

Rules auto-load from `.claude/rules/*.md` (per [official Claude Code memory docs](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/)) — no `CLAUDE.md` pointer is required or tracked.

## Check 1 — Frontmatter shape

For each `.claude/rules/**/*.md`, Read the first ~15 lines and confirm frontmatter:

- Opens/closes with `---`.
- If `paths:` is present, it MUST be a YAML list (keys indented under, starting with `-`), NOT an inline JSON array like `["foo", "bar"]` and NOT a scalar.
- Flag legacy `globs:` key — it is ignored by Claude Code and should be renamed to `paths:`.

For each violator: suggest *"Fix frontmatter to match `paths:` YAML list format (see `STACK_DETECTION.md`)."*

## Check 2 — Missing skill references

Rules whose body contains a `## Skill reference` section mentioning skill name(s) that are NOT installed in `.claude/skills/`.

For each rule with skill-ref:
- Grep the rule body for backtick-quoted skill names (e.g. `` `dotnet-expert` ``) within the Skill reference section.
- For each referenced skill, check if `.claude/skills/<skill>/SKILL.md` exists.
- If missing, note it.

For each: suggest *"Install with `/skills:setup-<skill>` (if the skill command exists in this marketplace) or note that the referenced skill is unavailable in this project."*

## Check 3 — Dead paths

Rules whose `paths:` patterns match zero files in the project.

For each rule:
- Parse `paths:` from frontmatter.
- For each pattern, run Glob. Count matches.
- If total matches across all patterns is zero, flag it.

For each: suggest *"The rule's paths don't match any files. Re-run `/rules:setup-<slug>` to re-detect the stack, or edit the `paths:` list manually."*

## Check 4 — Legacy `## Agent docs` pointer section (informational)

Previous versions of this plugin registered pointer lines under `## Agent docs` in `CLAUDE.md`. This is no longer needed — rules auto-load from disk. If `CLAUDE.md` contains such a section, suggest: *"The `## Agent docs` section in CLAUDE.md is legacy and can be removed to save context tokens. Claude Code auto-loads `.claude/rules/*.md` without pointers."*

## Report

Output a sectioned report:

```
# Rules Doctor Report

## Summary
- Total rules: N
- Issues found: N (frontmatter: N, missing skills: N, dead paths: N, legacy pointers: N)
- Status: OK / ATTENTION NEEDED

## Issues

### Frontmatter issues (N)
- `slug` — path, suggested fix

### Missing skill references (N)
- ...

### Dead paths (N)
- ...

### Legacy `## Agent docs` pointer section
- present / absent (with fix suggestion)
```

If `Issues found = 0`, report a single-line *"All good. N rules, all consistent."*
