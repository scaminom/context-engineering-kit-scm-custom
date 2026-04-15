---
description: .NET / ASP.NET Core coding guidance
globs: __GLOBS__
---

# .NET Coding Guidance

Pragmatic defaults for .NET 8+ work in this project. Apply alongside framework-specific rules (e.g. ASP.NET Core best practices) when present.

## Core rules

- **Target .NET 8+ and C# 12** — use records, primary constructors, collection expressions, pattern matching.
- **Enable nullable reference types** — `<Nullable>enable</Nullable>` in every project.
- **Async end-to-end** — never block with `.Result` / `.Wait()`; never use `async void` (except event handlers).
- **Propagate `CancellationToken`** through every async chain.
- **DI via constructor injection** — no service locator, no `new` of infrastructure inside domain / application code.
- **DTOs are records** — immutable, `init`-only properties, explicit `required` where applicable.
- **EF Core read paths use `AsNoTracking()`**.
- **Result pattern** for business failures; reserve exceptions for exceptional conditions.
- **Guard clauses** at public boundaries — `ArgumentNullException.ThrowIfNull(x)`.

## Anti-patterns

| Anti-pattern | Fix |
|---|---|
| `async void` (non-event) | Return `Task` |
| `.Result` / `.Wait()` | `await` |
| `catch (Exception) { }` | Handle or rethrow with context |
| `new HttpClient()` | `IHttpClientFactory` |
| Exposing EF entities in API responses | DTO records |
| Hardcoded config | `IOptions<T>` + config binding |
| `dynamic` in business logic | Generics or explicit types |
