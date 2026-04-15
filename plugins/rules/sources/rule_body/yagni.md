---
description: YAGNI — You Aren't Gonna Need It. Implement only what is required now.
globs: __GLOBS__
---

# YAGNI — You Aren't Gonna Need It

Write code for current requirements only. Speculative generality, options that no one has asked for, and "just in case" abstractions add cost (maintenance, cognitive load, bug surface) without delivering value. Defer until a second real caller or a real requirement forces the abstraction.

## Rules

- **Do** implement the simplest thing that satisfies today's requirement.
- **Do** delete dead code, unused flags, and commented-out blocks on sight.
- **Do not** add configuration options, extension points, or parameters with a single call site.
- **Do not** build frameworks for problems you don't have yet.
- **Do not** over-parameterize functions anticipating variations nobody asked for.

## Signals you're violating YAGNI

- Generic type parameters used in one place.
- Strategy / factory patterns with a single concrete implementation.
- Feature flags with no owner and no flip date.
- `TODO: make this configurable` with no ticket.
- Abstractions whose interface mirrors its one implementation 1:1.

## When generality IS justified

- You have two real, different concrete cases in hand right now.
- A public API contract forces it.
- The abstraction is cheaper than the duplication it avoids, measured honestly.

Otherwise: duplicate, ship, and refactor when the third case arrives.
