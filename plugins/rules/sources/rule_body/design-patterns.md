---
description: Design pattern guidance — recognize recurring problems, apply GoF patterns pragmatically.
globs: __GLOBS__
---

# Design Patterns

Patterns are vocabulary for recurring design problems. Use them to communicate intent, not as goals in themselves. A pattern without a matching problem is dead code; a problem without a pattern name is still solvable with plain OO / functional design.

## Rules

- **Do** recognize the problem first, then match a pattern.
- **Do** prefer the simplest pattern that resolves the tension (composition over inheritance, strategy over inheritance, factory over singleton when possible).
- **Do** name classes / modules after the pattern when it clarifies intent (`OrderStrategy`, `UserRepository`).
- **Do not** apply a pattern because you studied it — apply it because the problem exists in your code.
- **Do not** build pattern infrastructure (registries, factories, observers) before the first real need.

## Common problem → pattern map

| Problem | Pattern |
|---|---|
| Need to swap algorithm at runtime | Strategy |
| Multiple implementations behind one interface | Strategy / Factory |
| Need to add behavior without modifying a class | Decorator / Visitor |
| Object creation depends on runtime conditions | Factory Method / Abstract Factory |
| Notify N listeners of state changes | Observer / Pub-Sub |
| Separate construction from representation | Builder |
| Provide a unified interface over a subsystem | Facade |
| One shared resource, need single access point | Singleton (use sparingly) |
| Traverse a tree / structure uniformly | Visitor / Composite |
| Defer a request for queuing, undo, logging | Command |

## Anti-patterns

- **Pattern over-engineering** — using Observer for a call that has exactly one listener.
- **Singleton abuse** — hiding global mutable state behind `getInstance()`.
- **God factory** — one factory that builds 20 unrelated things.
- **Strategy with one concrete strategy** — that's a function, not a pattern.
