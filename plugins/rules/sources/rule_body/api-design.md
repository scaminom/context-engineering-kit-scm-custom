---
description: REST API design — resource naming, status codes, pagination, filtering, errors, versioning.
paths:
__PATHS__
---

# REST API Design

Design APIs that are predictable, consistent, and hard to misuse. Surface decisions should match HTTP conventions so clients built against any REST API can understand yours without reading documentation first.

## Resource naming

- **Do** use plural nouns: `/orders`, `/users/{id}/invoices`.
- **Do** keep URLs lowercase and hyphenated: `/password-resets`.
- **Do not** put verbs in paths — the HTTP method is the verb.
- **Do not** expose database columns or internal IDs when opaque IDs or slugs work.

## HTTP methods & status codes

| Action | Method | Success | Notable failure |
|---|---|---|---|
| List | GET /orders | 200 | — |
| Read | GET /orders/{id} | 200 | 404 |
| Create | POST /orders | 201 + Location header | 400, 409, 422 |
| Full update | PUT /orders/{id} | 200 or 204 | 404, 409 |
| Partial update | PATCH /orders/{id} | 200 or 204 | 404, 409, 422 |
| Delete | DELETE /orders/{id} | 204 | 404 |

- **Do** use 401 for missing/invalid auth, 403 for authenticated-but-forbidden.
- **Do** use 429 for rate limiting with `Retry-After` header.
- **Do** use 422 for semantically invalid (validation) vs 400 for malformed request.

## Pagination

- **Do** offer cursor-based pagination for collections that can grow unbounded.
- **Do** document page size default and max; enforce max server-side.
- **Do** return total count only when computing it is cheap.
- **Do not** return a raw array at the root — wrap: `{ "data": [...], "next_cursor": "..." }`.

## Filtering & sorting

- **Do** expose filters as flat query params: `?status=open&assignee=me`.
- **Do** support sort with explicit order: `?sort=-created_at,name`.
- **Do not** build a query DSL in query strings — if you need that, it's GraphQL territory.

## Error shape (pick one and stick to it)

```json
{
  "error": {
    "code": "invalid_customer_id",
    "message": "Customer not found",
    "details": [{ "field": "customer_id", "issue": "unknown" }]
  }
}
```

Or [RFC 7807 Problem Details](https://www.rfc-editor.org/rfc/rfc7807). Consistency matters more than which format.

## Versioning

- **Do** version at the URL prefix (`/v1/`) when breaking changes are likely.
- **Do** treat additive changes (new fields, new endpoints) as non-breaking and ship without a version bump.
- **Do not** version individual endpoints — version the contract.

## Rate limiting

- Expose `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.
- Document per-route vs per-account limits explicitly.
