# Stack Detection & Path Inference

Shared routine for all `/rules:setup-*` commands. Read this file during command execution; do NOT inline it into command prompts.

Rules auto-load from `.claude/rules/*.md` — no `CLAUDE.md` pointer needed. See [official docs](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/) for the `paths:` frontmatter contract.

## Detection order (first match wins)

Use Glob / Read against the project root. Check in this order (most specific first):

| # | Marker file(s) | Stack | Paths (list) | Human summary |
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

2. Detect stack using the table above. Get PATHS_ARRAY (e.g. ["src/app/**/*.ts"]) and SUMMARY (human text).

3. Copy rule body:
   mkdir -p .claude/rules
   cp "$MARKETPLACE/plugins/rules/sources/rule_body/<slug>.md" .claude/rules/<slug>.md

4. Inject paths as YAML list: Edit .claude/rules/<slug>.md to replace the literal token "__PATHS__" with a YAML list built from PATHS_ARRAY. Format EXACTLY:

   For PATHS_ARRAY = ["src/app/**/*.ts"] emit:
       - "src/app/**/*.ts"

   For PATHS_ARRAY = ["src/**/*.{ts,tsx}", "app/**/*.{ts,tsx}"] emit:
       - "src/**/*.{ts,tsx}"
       - "app/**/*.{ts,tsx}"

   Indentation: 2 spaces before `-`. Each entry on its own line. Quote each pattern with double quotes.

5. (Only for skill-ref rules) Ask user:
   "Reference the `<skill-name>` skill in this rule? When the agent reads this rule and is about to do <activity>, the rule will suggest invoking `<skill-name>` and will ask you (the user) first before invoking. (y/n)"
   If y:
     cat "$MARKETPLACE/plugins/rules/sources/rule_references/<slug>.md" >> .claude/rules/<slug>.md

6. Report: stack detected, paths used, skill-ref included?, file path written.
```

## Why no CLAUDE.md pointer

Per the official [Claude Code memory docs](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/), any `.md` file inside `.claude/rules/` is auto-discovered and loaded into the session — unconditionally if no `paths:` frontmatter, or conditionally when files matching `paths:` are opened. A pointer line under `## Agent docs` in `CLAUDE.md` is redundant and wastes context tokens. Do not register pointers.
