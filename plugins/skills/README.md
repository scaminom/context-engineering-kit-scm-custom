# skills plugin

Per-project skill installer. Copies curated skill bundles into `<project>/.claude/skills/<name>/` so they activate only in projects where you want them.

## Why per-project

Skills installed globally load their `description:` into **every** session. If you only need `dotnet-expert` on .NET repos, installing it per-project keeps other sessions lean.

## Commands

| Command | Skill | Size |
|---|---|---|
| `/skills:setup-dotnet-expert` | `.NET 8+` expert (minimal APIs, CQRS, EF Core, Dapper, JWT, cloud-native) | SKILL.md + 8 references |
| `/skills:setup-design-pattern-advisor` | GoF pattern recommendations grounded in problem context | SKILL.md + pattern-catalog |
| `/skills:setup-frontend-design` | Distinctive, production-grade frontend UI | SKILL.md |
| `/skills:setup-api-design` | REST API design patterns | SKILL.md |

## How it works

Each command copies `plugins/skills/sources/<name>/` → `<project>/.claude/skills/<name>/`. The skill becomes available **only** in that project.

## Pairing with rules

Many `/rules:setup-*` commands reference a skill in their body. Installing the rule and the skill are independent — the rule just tells the agent *"consider invoking skill X, ask the user first"*. You decide whether to install the actual skill.
