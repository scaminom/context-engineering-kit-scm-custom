---
description: Install ALL rules (20 total). Asks once per skill-ref rule whether to embed the skill reference.
argument-hint: No arguments
---

# Setup All Rules

Install every rule shipped by this plugin. Follows the **Standard command flow** from `$MARKETPLACE/plugins/rules/STACK_DETECTION.md`, with stack detected ONCE up front and reused for every rule.

## Step 0 — Confirm

Tell the user: *"This will install 20 rules into `.claude/rules/`. It will also prompt 3 times (once per skill-reference rule). Proceed? (y/n)"* — stop on `n`.

## Step 1 — Detect stack once

Apply the STACK_DETECTION table. Save `PATHS_ARRAY` and `SUMMARY`. Do not re-prompt for subsequent rules.

## Step 2 — Install each rule

Run the Standard command flow (copy source → inject paths → append skill-ref if applicable) for each row:

### Standalone rules (skill-ref: none)

| slug | rule description |
|---|---|
| `solid` | SOLID principles |
| `yagni` | YAGNI |
| `dry` | DRY principle |
| `kiss` | KISS principle |
| `clean-architecture-ddd` | Clean Architecture / DDD |
| `command-query-separation` | Command-Query Separation (CQS) |
| `early-return-pattern` | Early return pattern |
| `error-handling` | Typed error handling |
| `explicit-control-flow` | Explicit control flow |
| `explicit-data-flow` | Explicit data flow |
| `explicit-side-effects` | Explicit side effects |
| `function-file-size-limits` | Function and file size limits |
| `functional-core-imperative-shell` | Functional core, imperative shell |
| `library-first-approach` | Library-first approach |
| `principle-of-least-astonishment` | Principle of least astonishment |
| `separation-of-concerns` | Separation of concerns |
| `call-site-honesty` | Call-site honesty for logging |
| `domain-specific-naming` | Domain-specific naming |

### Rules with skill reference (ask once each)

For each, prompt verbatim the skill question from the Standard command flow:

| slug | rule description | skill-name | activity |
|---|---|---|---|
| `design-patterns` | Design pattern guidance | `design-pattern-advisor` | face a recurring design problem |
| `frontend` | Frontend UI quality | `frontend-design` | design or build non-trivial UI |
| `api-design` | REST API design | `api-design` | design new API surfaces |

### Rules that require a specific stack

- `dotnet` (slug `dotnet`, description `.NET coding guidance`, skill-ref `dotnet-expert`, paths forced to `["**/*.cs", "**/*.csproj"]`): install ONLY if `*.csproj` / `global.json` / `*.sln` exists. Otherwise skip and mention at report.

## Step 3 — Report

Show:
- Stack detected + paths used.
- Rules written (count).
- Rules skipped + reason (e.g. `dotnet` skipped: no .NET markers).
- Skill references embedded (list).
- Follow-up tip: *"Install referenced skills per-project with `/skills:setup-*` as needed. Rules auto-load from `.claude/rules/` — no `CLAUDE.md` changes required."*
