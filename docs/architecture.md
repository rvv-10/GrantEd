# Architecture

GrantEd is a **modular monolith**: one FastAPI application, deployed as one container, with internal modules that have clear boundaries. There are no microservices.

## Two paths through the system

**Runtime path (students)**

```
Browser (React) → Caddy (HTTPS) → FastAPI → eligibility engine
                                       ↓
                                  PostgreSQL (published scheme data only)
```

Answers stay in the browser and in the request. The API never stores them (ADR 0001).

**Research path (scheme data)**

```
Source checker (scheduled GitHub Action) → opens issue
  → you edit research/*.yaml → pull request + CI validation → merge
  → load_research.py seeds PostgreSQL
```

Nothing reaches the database without a merged PR (ADR 0002).

## Dependency rule inside the backend

```
api/v1  →  module services  →  eligibility (pure)  
                            →  module repositories → database
```

- `app/eligibility/` imports nothing else from `app/`. No database, no HTTP, no clock.
- Routes contain no business logic. They validate input with Pydantic, call a service and return a schema.
- Repositories contain queries only.

## Eligibility engine in one paragraph

A rule is a validated tree of `Group` (all / any / not), `Condition` (field, operator, value, source) and `Manual` (a provider decision) nodes. Evaluation uses Kleene three-valued logic: a missing answer gives UNKNOWN, and UNKNOWN never becomes TRUE. `evaluate(profile, rule, as_of)` is a pure function. The same inputs always give the same result, with an explanation for every condition.

_Full details: `docs/eligibility-engine.md` (Day 6) and `docs/database.md` (Day 5)._
