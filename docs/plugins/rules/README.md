# rules plugin

Project-scoped coding rules via slash commands. Built against the official [Claude Code memory docs](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/): rules live in `.claude/rules/` and auto-load based on their `paths:` frontmatter — no `CLAUDE.md` pointer required.

## How it works

Each `/rules:setup-<name>` command:

1. Analyzes your project (reads `CLAUDE.md`, detects stack markers: `package.json`, `*.csproj`, `pyproject.toml`, `go.mod`, `pom.xml`, `Cargo.toml`, `angular.json`, `nest-cli.json`, `next.config.*`).
2. Infers the correct `paths:` list for the rule based on detected stack.
3. Writes `.claude/rules/<name>.md` with frontmatter (`description:` + `paths:` YAML list) and rule body.

Rules referencing skills (e.g. `setup-dotnet` → `dotnet-expert`) **prompt the user at install time**: *"Reference skill `X`? (y/n)"*. Rule body, when invoked by the agent, also asks the user before actually invoking the skill.

### Frontmatter format

All installed rules use the official frontmatter shape:

```yaml
---
description: Short human label (optional)
paths:
  - "src/app/**/*.ts"
  - "src/**/*.spec.ts"
---
```

- `paths:` is a YAML list (dashed items), not an inline JSON array.
- Rules without `paths:` load unconditionally every session — use sparingly.
- Path-scoped rules only load into context when Claude reads a matching file.

## Commands

| Command | What it installs | Skill reference |
|---|---|---|
| `/rules:setup-all` | Every rule below | Prompts per skill-ref |
| `/rules:setup-ddd` | Bundle: clean-architecture-ddd + separation-of-concerns + domain-naming | — |
| `/rules:setup-solid` | Single Responsibility, Open/Closed, Liskov, Interface Segregation, Dependency Inversion | — |
| `/rules:setup-yagni` | You Aren't Gonna Need It | — |
| `/rules:setup-dry` | Don't Repeat Yourself | — |
| `/rules:setup-kiss` | Keep It Simple, Stupid | — |
| `/rules:setup-design-patterns` | GoF design pattern guidance | `design-pattern-advisor` |
| `/rules:setup-dotnet` | .NET coding guidance | `dotnet-expert` |
| `/rules:setup-frontend` | Frontend design / UI code quality | `frontend-design` |
| `/rules:setup-api-design` | REST API design patterns | `api-design` |
| `/rules:setup-clean-architecture` | Clean Architecture / DDD dependency rule | — |
| `/rules:setup-cqs` | Command-Query Separation | — |
| `/rules:setup-early-return` | Early return / guard clauses | — |
| `/rules:setup-error-handling` | Error handling patterns | — |
| `/rules:setup-explicit-control-flow` | Explicit control flow | — |
| `/rules:setup-explicit-data-flow` | Explicit data flow | — |
| `/rules:setup-explicit-side-effects` | Explicit side effects | — |
| `/rules:setup-function-file-size` | Function & file size limits | — |
| `/rules:setup-functional-core` | Functional core, imperative shell | — |
| `/rules:setup-library-first` | Library-first approach | — |
| `/rules:setup-least-astonishment` | Principle of least astonishment | — |
| `/rules:setup-separation-of-concerns` | Separation of concerns | — |
| `/rules:setup-call-site-honesty` | Call-site honesty | — |
| `/rules:setup-domain-naming` | Domain-specific naming | — |
| `/rules:setup-typescript-bp` | TypeScript best practices (fixed paths `**/*.ts,tsx`) | — |
| `/rules:setup-dotnet8-bp` | ASP.NET Core 8 performance & reliability (fixed paths `**/*.cs`) | — |
| `/rules:setup-dotnet10-bp` | ASP.NET Core 10 performance & reliability (fixed paths `**/*.cs`) | — |
| `/rules:list` | Table of installed rules with their `paths:` and load mode | — |
| `/rules:doctor` | Audit consistency: frontmatter shape, missing skills, dead paths, legacy pointers | — |
| `/rules:uninstall <slug>` | Remove `.claude/rules/<slug>.md` (and any legacy `CLAUDE.md` pointer) | — |

## Stack → paths

| Marker | Paths |
|---|---|
| `angular.json` | `src/app/**/*.ts` |
| `nest-cli.json` | `src/**/*.ts` |
| `next.config.*` | `src/**/*.{ts,tsx}`, `app/**/*.{ts,tsx}` |
| `package.json` | `src/**/*.{ts,tsx,js,jsx}` |
| `*.csproj` / `global.json` | `**/*.cs` |
| `pyproject.toml` / `setup.py` | `**/*.py` |
| `go.mod` | `**/*.go` |
| `pom.xml` / `build.gradle` | `src/main/**/*.{java,kt}` |
| `Cargo.toml` | `**/*.rs` |
| fallback | `src/**/*` |

## Migration from legacy installs

Earlier versions of this plugin wrote frontmatter with `globs:` (not `paths:`) and registered pointer lines under `## Agent docs` in `CLAUDE.md`. Both are obsolete:

- `globs:` is ignored by Claude Code — rename the key to `paths:` and reshape the value as a YAML list (`- "pattern"`).
- `## Agent docs` pointers are redundant because `.claude/rules/*.md` is auto-discovered.

Run `/rules:doctor` to flag stale installs, then re-run the matching `/rules:setup-*` command to regenerate in the new format.
