# rules plugin

Project-scoped coding rules via slash commands.

## How it works

Each `/rules:setup-<name>` command:

1. Analyzes your project (reads `CLAUDE.md`, detects stack markers: `package.json`, `*.csproj`, `pyproject.toml`, `go.mod`, `pom.xml`, `Cargo.toml`, `angular.json`, `nest-cli.json`, `next.config.*`).
2. Infers the correct `globs:` for the rule based on detected stack.
3. Writes `.claude/rules/<name>.md` with frontmatter + rule body.
4. Registers a one-line pointer under `## Agent docs` in `CLAUDE.md` (creating CLAUDE.md / section if missing).

Rules referencing skills (e.g. `setup-dotnet` → `dotnet-expert`) **prompt the user at install time**: *"Reference skill `X`? (y/n)"*. Rule body, when invoked by the agent, also asks the user before actually invoking the skill.

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
| `/rules:setup-typescript-bp` | TypeScript best practices (fixed globs `**/*.ts,tsx`) | — |
| `/rules:setup-dotnet8-bp` | ASP.NET Core 8 performance & reliability (fixed globs `**/*.cs`) | — |
| `/rules:setup-dotnet10-bp` | ASP.NET Core 10 performance & reliability (fixed globs `**/*.cs`) | — |
| `/rules:list` | Table of installed rules with globs + pointer status | — |
| `/rules:doctor` | Audit consistency: orphan rules, broken pointers, missing skills, dead globs | — |
| `/rules:uninstall <slug>` | Remove `.claude/rules/<slug>.md` and its CLAUDE.md pointer | — |

## Stack → globs

| Marker | Globs |
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
