# Engineering Principles

## Database boundary

- PostgreSQL owns domain logic, invariants, and transactional behavior.
- Application code accesses PostgreSQL **only through database API functions**. It never queries application tables directly.
- Python knows the database API, not the underlying database representation.
- Python has access only to the API schema, not to the schema containing application tables.
- Enforce these boundaries with PostgreSQL privileges wherever possible; do not rely on developer discipline alone.

## API contracts

- Database function signatures and result columns are API contracts.
- Keep caller contracts narrow and explicit.
- Prefer database changes that preserve existing API contracts.
- New incompatible API versions get a new PostgreSQL API schema and a new set of functions rather than breaking existing callers.

## Python

- Python is a thin HTTP/authentication/serialization adapter.
- Do not duplicate domain logic in Python.
- No ORM. Database interaction consists of explicit PostgreSQL function calls.

## UI and network boundaries

**UI composition is local; data composition is remote. Do not conflate them.**

React component boundaries must not define network boundaries. A server-backed view transition or user action should normally require one coarse-grained HTTP round trip returning all state needed for the resulting view.

Avoid dependent request waterfalls.

```text
many React components
        ≠
many HTTP calls

one page / action
        ≈
one purpose-built backend call
        ↓
one database API function
        ↓
complete view model
