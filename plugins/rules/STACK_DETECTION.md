# Stack Detection & Glob Inference

Shared routine for all `/rules:setup-*` commands. Read this file during command execution; do NOT inline it into command prompts.

## Detection order (first match wins)

Use Glob / Read against the project root. Check in this order (most specific first):

| # | Marker file(s) | Stack | Globs (JSON array) | Human summary |
|---|---|---|---|---|
| 1 | `angular.json` | Angular | `["src/app/**/*.ts"]` | `src/app/**/*.ts` |
| 2 | `nest-cli.json` | NestJS | `["src/**/*.ts"]` | `src/**/*.ts` |
| 3 | `next.config.js` / `.ts` / `.mjs` / `.cjs` | Next.js | `["src/**/*.{ts,tsx}", "app/**/*.{ts,tsx}"]` | Next.js ts/tsx |
| 4 | `vite.config.{js,ts}` + `tsconfig.json` | Vite + TS | `["src/**/*.{ts,tsx}"]` | `src/**/*.{ts,tsx}` |
| 5 | `package.json` (no specific marker above) | Generic JS/TS | `["src/**/*.{ts,tsx,js,jsx}"]` | JS/TS src |
| 6 | any `*.csproj` / `global.json` / `*.sln` | .NET | `["**/*.cs", "**/*.csproj"]` | `**/*.cs` |
| 7 | `pyproject.toml` / `setup.py` / `requirements.txt` | Python | `["**/*.py"]` | `**/*.py` |
| 8 | `go.mod` | Go | `["**/*.go"]` | `**/*.go` |
| 9 | `pom.xml` / `build.gradle` / `build.gradle.kts` | JVM (Java/Kotlin) | `["src/main/**/*.java", "src/main/**/*.kt"]` | JVM src/main |
| 10 | `Cargo.toml` | Rust | `["**/*.rs"]` | `**/*.rs` |
| 11 | `pubspec.yaml` | Dart/Flutter | `["lib/**/*.dart"]` | `lib/**/*.dart` |
| 12 | `Package.swift` | Swift | `["Sources/**/*.swift"]` | `Sources/**/*.swift` |
| 13 | `mix.exs` | Elixir | `["lib/**/*.ex", "lib/**/*.exs"]` | Elixir `lib/**` |
| — | none matched | Unknown | `["src/**/*"]` | all source |

## CLAUDE.md override

If `CLAUDE.md` exists and explicitly names the stack in its content (e.g. "This is an Angular workspace"), prefer that signal over marker files. Mention the override when reporting to the user.

## Standard command flow

Every `/rules:setup-*` command should follow this skeleton:

```
1. Locate marketplace:
   MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
   If empty -> stop with error.

2. Detect stack using the table above. Compute GLOBS (JSON array) and SUMMARY (human text).

3. Copy rule body:
   mkdir -p .claude/rules
   cp "$MARKETPLACE/plugins/rules/sources/rule_body/<slug>.md" .claude/rules/<slug>.md

4. Inject globs: Edit .claude/rules/<slug>.md to replace the literal token "__GLOBS__" with GLOBS.

5. (Only for skill-ref rules) Ask user:
   "Reference the `<skill-name>` skill in this rule? When the agent reads this rule and is about to do <activity>, the rule will suggest invoking `<skill-name>` and will ask you (the user) first before invoking. (y/n)"
   If y:
     cat "$MARKETPLACE/plugins/rules/sources/rule_references/<slug>.md" >> .claude/rules/<slug>.md

6. Register pointer in CLAUDE.md (see POINTER_REGISTRATION below).

7. Report: stack detected, globs used, skill-ref included?, file path written, CLAUDE.md action.
```

## POINTER_REGISTRATION

Read CLAUDE.md at project root.

- **Missing** → create with:

  ```markdown
  # Project

  ## Agent docs

  - `.claude/rules/<slug>.md` — <rule description> (auto-loads on <SUMMARY>)
  ```

- **Present with `## Agent docs` section** → Edit: append pointer line under the section. Skip if exact line already present.

- **Present without `## Agent docs`** → Edit: append new section at end:

  ```markdown

  ## Agent docs

  - `.claude/rules/<slug>.md` — <rule description> (auto-loads on <SUMMARY>)
  ```

`<rule description>` is a short human label (different from rule's full `description:` field). Examples:
- `solid.md` → "SOLID principles"
- `yagni.md` → "YAGNI"
- `clean-architecture-ddd.md` → "Clean Architecture / DDD"
- `dotnet.md` → ".NET coding guidance"
