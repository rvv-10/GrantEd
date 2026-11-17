# GrantEd: instructions for Claude Code

GrantEd is a free student financial-support discovery platform for the Republic of Ireland and Northern Ireland. A student answers short questions and gets explained, source-backed results. It's a portfolio project with a 6-week MVP (16 Nov – ~27 Dec 2026, about 12–15 hours a week). The developer must be able to explain every part of it in a software engineering interview.

@README.md
@docs/architecture.md
@docs/decisions/README.md
@docs/progress.md

The full product blueprint is `docs/blueprint.md`. It is long, so read the relevant part when a task needs product or scope detail. Don't load it all by default.

## How to work with me

I'm learning while building. Help me implement; don't implement for me.

For any feature, follow this cycle (or run `/feature`):

1. Restate the problem and the desired outcome.
2. Propose a design. Name the files you'll create or change **before** changing them.
3. Explain the important decisions and trade-offs.
4. Break the work into small steps. Do the **smallest complete vertical slice**, never a whole app.
5. Implement step by step, explaining the key parts of the code as you go.
6. Write and run tests.
7. Review for edge cases, errors, security and privacy.
8. Summarise what I should be able to explain in an interview, and ask me 1–2 short questions about it.

Other ways of working:

- Make a sensible assumption and write it down, rather than stopping. Ask **one** focused question only when an assumption would change the architecture or correctness.
- Don't add a dependency without saying why it's needed and what the alternative was.
- Never invent API behaviour, library options or configuration. If unsure, say so and check the current docs.
- Don't rewrite working code that wasn't part of the task.
- Be precise about status. "Designed" ≠ "implemented" ≠ "tested" ≠ "verified with official data" ≠ "deployed".
- At the end of a session, run `/handover`.

## Rules that must never be broken

**Eligibility**

- An LLM never decides eligibility. Only the deterministic engine in `backend/app/eligibility/` does.
- UNKNOWN is never treated as TRUE. A missing answer gives UNKNOWN.
- Never use `eval`, `exec` or any dynamic execution of rules. Rules are validated data (Pydantic models), with operators looked up from a fixed dictionary.
- `evaluate(profile, rule, as_of)` is pure: no database, HTTP, clock, randomness or logging of answers.
- Never invent or "infer" an eligibility criterion, amount or deadline. Every one must trace to a source in `research/sources.yaml`.

**Research data (ADR 0002)**

- `research/` is the source of truth. The database is a copy loaded by `backend/scripts/load_research.py`. Never edit the database by hand, and never write code that does.
- Never edit a published version file. A correction is a new version file.
- Unknown values are written as `unknown` and linked to an open question, never left blank or guessed.
- You may draft research records, but mark them `Awaiting verification`. Only I mark a record verified, after a 24-hour re-check against its sources.
- Tier-3 sources (news, forums, blogs) can help discover a scheme, never verify one.

**Privacy (ADR 0001)**

- v1.0 is guest-only and stateless. Never store assessment answers in the database, files, logs, error reports or analytics.
- Never collect PPS numbers, bank details, documents or diagnoses.
- Never send real student data to any external AI service.
- Never hardcode secrets. Configuration comes from environment variables. Never read or print `.env` files.

**Architecture**

- Modular monolith: one FastAPI app. No microservices.
- No Redis, RQ, Terraform, accounts or auth module in v1.0 (ADRs 0001 and 0003). If a task seems to need one, stop and say so.
- Dependency direction: `api/v1 → module services → eligibility (pure) / module repositories`. **`app/eligibility/` imports nothing from the rest of `app/`.**
- Routes hold no business logic. Services hold no SQL. Repositories hold no business rules.

## Project layout

See `docs/repository-structure.md`. Each backend module (`schemes/`, `sources/`, …) holds its own `models.py` (SQLAlchemy), `schemas.py` (Pydantic), `repository.py` and `service.py`.

## Commands

The tooling isn't set up yet; it arrives on Week 1, Day 6. Once it exists, run from `backend/`:

- Tests: `pytest`
- Lint and format: `ruff check .` and `ruff format .`

Update this section whenever a command is added.

## Conventions

- Python: type hints everywhere, 4-space indent, 100-character lines, `snake_case`. Tests are named `test_<behaviour>`.
- Ids: scheme `SCH-<JUR>-<SHORT>`, folder name in lowercase (`research/schemes/sch-roi-rtc/`). Sources `SRC-<ORG>-<SHORT>`, questions `Q-001`.
- Git: one feature per branch (`feat/…`, `fix/…`, `research/…`, `docs/…`), small commits in imperative mood, a PR into `main` using the template. Never push to `main` directly and never force-push.
- When an architectural decision changes, write a new ADR in `docs/decisions/`. Don't edit an accepted one.
