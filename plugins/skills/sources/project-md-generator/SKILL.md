---
name: project-md-generator
description: Generate a high-quality CLAUDE.md file for a project. Use this skill whenever the user wants to create, rewrite, or audit a CLAUDE.md (or AGENTS.md) for their project. Triggers on phrases like "create a CLAUDE.md", "write my CLAUDE.md", "onboard Claude to my project", "set up Claude for my codebase", "generate project instructions for Claude", or any time the user wants Claude to understand their project structure. Also use when the user asks how to write good instructions for Claude Code or wants to improve an existing CLAUDE.md.
---

# CLAUDE.md Generator

Generate a lean, high-signal CLAUDE.md that actually improves Claude's performance — not one that bloats the context window.

## Core principles (internalize these before writing a single line)

**LLMs are stateless.** CLAUDE.md is the only context that loads in every session. Everything in it competes for attention every time — including instructions irrelevant to the current task.

**Frontier models can reliably follow ~150–200 instructions total.** Claude Code's system prompt already consumes ~50. Budget accordingly: fewer instructions = better adherence.

**Instruction decay is uniform.** Adding more instructions doesn't just push later ones out — it degrades compliance across *all* of them. Less is genuinely more.

**Claude is an in-context learner.** If the codebase follows patterns consistently, Claude will pick them up from reading files. Don't document conventions that are already visible in the code.

---

## Step 1: Gather project context

Before writing anything, ask the user for (or explore the codebase to find):

1. **Stack**: language, frameworks, runtime (e.g., Node 20, Bun, Python 3.12)
2. **Project type**: monorepo / single app / library / CLI / API
3. **Build & run commands**: how to build, start, test, typecheck
4. **Test strategy**: unit / integration / e2e? which framework?
5. **Package manager**: npm / pnpm / yarn / bun / uv / poetry
6. **Critical gotchas**: anything that has burned the user or will surprise Claude (e.g., "never run migrations directly", "always use the custom fetch wrapper")
7. **Agent docs that exist**: any existing markdown docs Claude should know about

If the user has a codebase open or has shared files, read the root `package.json`, `pyproject.toml`, `Makefile`, or equivalent to extract commands directly — don't ask for things you can discover.

---

## Step 2: Decide what goes in CLAUDE.md vs. elsewhere

Apply this filter to every candidate line:

| Content | Goes in CLAUDE.md? | Alternative |
|---|---|---|
| Build/test/lint commands | ✅ Yes | — |
| Project structure overview | ✅ Yes (brief) | — |
| WHY the project exists | ✅ Yes (1–2 sentences) | — |
| Critical safety rules ("never X") | ✅ Yes | — |
| Pointers to agent_docs/ files | ✅ Yes | — |
| Code style preferences | ❌ No | Let linter enforce it |
| Full API docs / schema | ❌ No | agent_docs/api.md |
| Test writing guide | ❌ No | agent_docs/testing.md |
| DB migration instructions | ❌ No | agent_docs/database.md |
| Formatting rules | ❌ No | Hook + linter |

**Target: under 60 lines for small projects, under 150 for large monorepos.**

---

## Step 3: Structure the CLAUDE.md

Use this template as a base. Omit sections that don't apply — empty sections waste tokens.

```markdown
# [Project Name]

[1–2 sentences: what is this and why does it exist]

## Stack
- [runtime + version]
- [framework]
- [database, if relevant]
- [key libraries worth knowing]

## Project structure
[Only for monorepos or non-obvious layouts. 4–8 lines max.]
- `apps/web` — Next.js frontend
- `apps/api` — Express API
- `packages/db` — Prisma schema and migrations

## Commands
```bash
# Install
[install command]

# Dev
[dev command]

# Test
[test command]

# Typecheck / lint
[check command]

# Build
[build command]
```

## Important constraints
[Only add lines that Claude would NOT discover by reading the code]
- [e.g., "Always use `bun` not `node` to run scripts"]
- [e.g., "Never run `prisma migrate` directly — use `bun run db:migrate`"]

## Agent docs
Claude: before starting work, check if any of these are relevant and read them.
- `agent_docs/architecture.md` — [what's in it]
- `agent_docs/testing.md` — [what's in it]
- [add as needed]
```

---

## Step 4: Progressive disclosure setup

If the project has any of these, recommend creating the corresponding agent_docs file and add a pointer in CLAUDE.md:

- Non-trivial architecture → `agent_docs/architecture.md`
- Complex test setup → `agent_docs/testing.md`
- Database / migrations → `agent_docs/database.md`
- External API integrations → `agent_docs/integrations.md`
- Deployment / infra → `agent_docs/deployment.md`

Each agent_docs file should:
- Start with a one-line summary of what it covers
- Use `file:line` references to actual source files instead of copying code snippets
- Stay under 100 lines

---

## Step 5: What NOT to include (anti-patterns)

Remove any line that:
- Documents something already enforced by a linter or formatter
- Would only apply to one specific task type (e.g., "when writing a migration...")
- Duplicates what's in a rules file (see `claude-rules-generator` skill)
- Is a reminder to be careful about something Claude already knows (e.g., "always write clean code")
- Is a code snippet that will go stale

---

## Step 6: Hook recommendations

Suggest these to the user as high-leverage additions that keep CLAUDE.md smaller:

**Stop hook (linter/formatter)**: runs your formatter after every Claude stop event so Claude never needs style instructions.
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{ "type": "command", "command": "bun run lint:fix && bun run format" }]
    }]
  }
}
```

**Slash command for code review**: instead of putting style rules in CLAUDE.md, create `.claude/commands/review.md`:
```markdown
Review the changes in `git diff --staged`. Check for:
- Type safety issues
- Missing error handling
- Test coverage gaps
```

---

## Output

Deliver:
1. The complete `CLAUDE.md` content, ready to copy-paste
2. A list of `agent_docs/` files to create (with a one-line description of each)
3. Any hook or slash command recommendations

After delivering, ask: "Does this look right? Is there anything critical I missed or anything here that wouldn't apply to every session?"
