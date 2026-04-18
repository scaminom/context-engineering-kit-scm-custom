---
description: KISS — Keep It Simple, Stupid. Prefer the simplest design that solves the problem.
paths:
__PATHS__
---

# KISS — Keep It Simple, Stupid

Simplicity is a property of the reader, not the writer. Code is simple when the next person (including future-you) can understand it fully, in isolation, within seconds. Complexity compounds — each clever trick costs every future reader.

## Rules

- **Do** pick the most boring solution that works.
- **Do** prefer straight-line code over cleverness.
- **Do** use language features the whole team already knows.
- **Do not** reach for a pattern, abstraction, or framework before trying plain code.
- **Do not** optimize for a single character saved at the cost of a minute of comprehension.

## Complexity signals

- A function that needs a comment to explain its overall purpose.
- A file the team avoids editing because "nobody really gets it."
- A call chain with more than 3 hops of indirection to do one thing.
- A class hierarchy deeper than 2 levels.
- Generic parameters, callbacks, or metaprogramming with one call site.

## Simplicity tactics

- Fewer moving parts > more moving parts.
- Named functions > inline lambdas when the lambda has a meaning.
- Early returns > nested conditionals.
- Data transformations > stateful mutation.
- Standard library > framework > hand-rolled, in that preference order.

## Measuring it

If explaining the code to a colleague takes longer than reading it, the code is too complex — not your colleague.
