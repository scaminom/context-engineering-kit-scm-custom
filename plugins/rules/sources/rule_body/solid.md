---
description: SOLID principles for object-oriented and modular design
globs: __GLOBS__
---

# SOLID Principles

Five principles for designing maintainable, extensible object-oriented / modular code. Apply them pragmatically — the goal is reducing coupling and isolating change, not ceremony.

## S — Single Responsibility

A class, module, or function should have one reason to change. Split responsibilities that belong to different stakeholders (business logic, persistence, presentation, etc.).

- **Do** give each unit one clear purpose reflected in its name.
- **Do not** bundle unrelated behaviors for convenience.

## O — Open/Closed

Code should be open for extension, closed for modification. Add new behavior by adding new code, not by editing stable code.

- **Do** use polymorphism, composition, or plug-in points for variability.
- **Do not** accumulate `if`/`switch` ladders on type tags for behavioral variations.

## L — Liskov Substitution

Subtypes must be usable in place of their base type without breaking callers.

- **Do** preserve preconditions, postconditions, and invariants of the parent.
- **Do not** throw for methods the parent contract says are supported.
- **Do not** narrow return types or widen accepted inputs in ways callers can't anticipate.

## I — Interface Segregation

Clients should not depend on methods they don't use. Many small, focused interfaces beat one fat interface.

- **Do** split interfaces by client need, not by implementor convenience.
- **Do not** force implementors to stub methods they don't support.

## D — Dependency Inversion

High-level modules should not depend on low-level modules; both depend on abstractions. Abstractions should not depend on details.

- **Do** inject collaborators through interfaces / abstract types defined by the consumer.
- **Do** wire concrete implementations at composition time (DI container, factory, main).
- **Do not** instantiate infrastructure (DB client, HTTP client, file system) inside domain / use-case code.

## Anti-Patterns

| Symptom | Principle violated | Fix |
|---|---|---|
| Class has "and" in its description | SRP | Split by responsibility |
| New feature requires editing a switch statement in 5 places | OCP | Replace with polymorphism or strategy |
| `throw new NotSupportedException` in override | LSP | Rethink hierarchy or interface |
| Interface with 20 methods, implementors stub half | ISP | Split into role interfaces |
| `new SqlClient()` inside business logic | DIP | Inject a repository abstraction |
