---
name: fastapi-best-practices
description: Guides the design, implementation, and review of Python FastAPI services, including project layout, Pydantic models, dependency injection, async I/O, error handling, database sessions, security, and testing. Use when building a new FastAPI endpoint or service, refactoring an existing one, or reviewing FastAPI code.
license: MIT
---

# FastAPI Best Practices

Write FastAPI code that is typed, testable, and safe by default. When reviewing existing
code, follow the same rules and point to the exact location of each deviation.

## Project layout

Prefer a layout organized by responsibility:

```
app/
  main.py          # create_app(), router registration, middleware
  config.py        # settings loaded from environment (pydantic-settings or similar)
  db.py            # engine and session factory
  models.py        # ORM models
  schemas.py       # Pydantic request/response models
  api/             # routers, one module per resource
  services/        # business logic, no FastAPI imports
tests/
```

Keep business logic in plain functions or classes in `services/` so it can be tested
without HTTP. Routers translate HTTP to service calls and back.

## Request and response models

- Define explicit Pydantic models for every request body and response. Do not return ORM
  objects or raw dicts from public endpoints.
- Use separate models for input and output (`UserCreate`, `UserRead`) so internal fields
  such as password hashes can never leak.
- Declare `response_model` (or a return type annotation) on every route so the OpenAPI
  schema is accurate and output is filtered.
- Validate constraints in the schema (`Field(min_length=1, max_length=200)`, `EmailStr`,
  enums) instead of in handler code.

## Dependency injection

- Provide database sessions, the current user, and settings through `Depends`.
- Use a generator dependency (`yield`) for resources that must be closed, such as a
  database session, so cleanup runs even when the handler raises.
- Keep dependencies small and composable. Authorization checks (for example
  `require_role("admin")`) belong in dependencies, not copied into each handler.
- Override dependencies in tests with `app.dependency_overrides`.

## Async correctness

- Use `async def` only when every I/O call inside is awaited with an async library.
- Never call blocking I/O (synchronous database drivers, `requests`, `time.sleep`, large
  file reads) inside `async def`. It blocks the event loop for all requests. Either use a
  plain `def` endpoint (FastAPI runs it in a thread pool) or move the call to
  `run_in_threadpool`.
- Share HTTP clients (`httpx.AsyncClient`) across requests via application lifespan,
  rather than creating one per request.

## Errors

- Raise `HTTPException` with an accurate status code and a stable, non-sensitive
  `detail` message. Do not leak stack traces, SQL, or file paths to clients.
- Map domain exceptions to HTTP responses in one place with exception handlers.
- Use 400/422 for invalid input, 401 for missing authentication, 403 for insufficient
  permission, 404 for missing resources, 409 for conflicts (duplicates, version
  mismatch).

## Database

- One session per request, committed or rolled back in a single place.
- Avoid N+1 queries: load related objects explicitly when listing.
- Use migrations (for example Alembic) for schema changes; never rely on automatic table
  creation in production.
- Enforce uniqueness and foreign keys in the database, not only in Python.

## Security

- Load secrets from the environment; never commit them.
- Configure CORS with an explicit list of allowed origins. Do not combine
  `allow_origins=["*"]` with credentials.
- Enforce authorization on the server for every state-changing operation. Never trust a
  role or user id sent by the client without verification.
- Limit request body and upload sizes; validate uploaded file types and names.
- Hash passwords with a slow password hashing algorithm (argon2 or bcrypt).

## Testing

- Use `TestClient` (or `httpx.AsyncClient` with an ASGI transport) against an app built by
  a `create_app()` factory.
- Use an isolated database per test session (temporary SQLite file or a disposable
  database) and override the session dependency.
- Test the unhappy paths: validation errors, forbidden access, not-found, conflicts.

## Review output

When reviewing, report findings as:

```
[Severity] <title>
Location: <file>:<line>
Issue: <what is wrong and its effect>
Fix: <concrete change>
```

Severities: Blocker (security or data integrity), Major (likely bug or blocking I/O in
async code), Minor (structure or typing), Nit (style).
