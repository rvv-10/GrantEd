# Repository structure, and why

This file explains every folder in the repo, when it gets content, and the reasoning behind the layout. It's written to be explainable in an interview.

## The full tree

```
GrantEd/
├── .github/
│   ├── pull_request_template.md   # Definition of Done + research re-check (now)
│   └── workflows/                 # ci.yml, source-check.yml (Week 5)
├── docs/
│   ├── architecture.md            # system overview (now)
│   ├── repository-structure.md    # this file (now)
│   ├── decisions/                 # ADRs (now)
│   ├── database.md                # (Day 5)
│   ├── fields.md                  # profile field registry, human-readable (Day 5)
│   ├── eligibility-engine.md      # (Day 6)
│   ├── privacy.md                 # (before deployment)
│   └── deployment.md              # runbook (Week 4)
├── research/
│   ├── README.md                  # rules for research data (now)
│   ├── sources.yaml, organisations.yaml, institutions.yaml   # (Day 3+)
│   ├── schemes/<scheme-id>/       # scheme.yaml + one file per year (Day 3+)
│   └── notes/                     # working notes, never loaded
├── backend/
│   ├── app/
│   │   ├── core/
│   │   ├── api/v1/
│   │   ├── eligibility/
│   │   ├── profile/
│   │   ├── schemes/
│   │   ├── sources/
│   │   ├── institutions/
│   │   ├── assessment/
│   │   └── review/
│   ├── alembic/
│   ├── scripts/
│   └── tests/
│       ├── unit/eligibility/
│       ├── integration/
│       └── data_quality/
├── frontend/                      # generated in Week 3
├── infrastructure/
│   ├── caddy/
│   └── scripts/
├── docker-compose.yml             # (Week 4)
├── .editorconfig
├── .gitignore
└── README.md
```

Git does not track empty folders. The `.gitkeep` files exist only so the structure is visible from the first commit. Delete each one once its folder has a real file.

---

## Top level: why these six folders

| Folder | Why it exists |
| --- | --- |
| `.github/` | GitHub reads this exact path for PR templates and Actions workflows. The PR template is how the Definition of Done and the research re-check get enforced in practice (ADR 0002). |
| `docs/` | Written reasoning lives next to the code it explains, versioned with it. Interviewers can read it, and so can you in three months. |
| `research/` | The scheme data is the product's foundation and its source of truth (ADR 0002). It is kept separate from `backend/` because it is data, not code. It has its own review rules, and nothing in it is executable. |
| `backend/` | The FastAPI application, its migrations, scripts and tests. |
| `frontend/` | The React app. It is a separate folder because it has its own toolchain (Node, npm, Vite) and its own build output. |
| `infrastructure/` | Deployment config that isn't application code: the reverse proxy and backup scripts. |

**Why one repository (a monorepo) and not separate frontend/backend repos?** One person, one product, one deployment. A single repo means a change that touches the API and the frontend type definitions lands in one PR, and CI tests them together. Separate repos would add version coordination with no benefit at this size.

---

## `backend/app/`: organised by module, not by layer

The blueprint's suggested layout groups code by **technical layer**: `models/`, `schemas/`, `services/`, `repositories/`. This repo groups it by **business module** instead (`schemes/`, `sources/` and so on). Each module holds its own layers:

```
schemes/
├── models.py       # SQLAlchemy tables
├── schemas.py      # Pydantic request/response shapes
├── repository.py   # database queries only
└── service.py      # business logic; calls repositories
```

**Why:** this is what "modular monolith" means in practice.

- Everything about schemes is in one place.
- Module boundaries are visible in the folder tree.
- If one module ever had to become its own service, its code is already together.

With layer folders, one feature is spread across four directories. Then nothing stops `services/schemes.py` from reaching into another module's tables.

| Module | Responsibility | Why it's separate |
| --- | --- | --- |
| `core/` | Settings (from environment variables), database engine/session, logging setup | Shared plumbing with no business logic. Every module depends on it, and it depends on none of them. Logging config here is where the "never log answers" rule is enforced (ADR 0001). |
| `api/v1/` | HTTP routes: parse, validate, call a service, return a schema | Keeps HTTP concerns (status codes, request parsing) out of business logic. That way services can be tested without HTTP. `v1/` exists so a breaking API change can become `v2/` without breaking the frontend. |
| `eligibility/` | Rule models (the `Group`/`Condition`/`Manual` tree), operators, three-valued logic, explanation building | **The most important boundary in the codebase.** It imports nothing from the rest of `app/`: no database, no HTTP, no clock. That makes it deterministic, fast to test exhaustively, and easy to explain. It's at the same level as the other modules, but it is a pure library, not a feature module. |
| `profile/` | Field registry (every answer the questionnaire can produce: name, type, allowed values) and the profile schema | Both the questionnaire and the rules depend on the same field definitions. If the registry lived inside `eligibility/`, the questionnaire would depend on the engine. If it lived in the frontend, CI couldn't check rules against it. One shared definition prevents a typo in a rule silently becoming UNKNOWN for everyone (R5). |
| `schemes/` | Schemes, versions, amount schedule, scheme relationships | The core catalogue. Versions and amounts live here, not in their own modules, because they never exist without a scheme. |
| `sources/` | Source register, change events | Sources are shared across schemes (one SUSI page can support many), and the change checker works on sources, not schemes. |
| `institutions/` | Providers and which schemes they participate in | Institutions have their own lifecycle and research checklist. Participation is checked per institution, never assumed. |
| `assessment/` | Takes a profile, loads published versions, runs the engine, groups and explains results | The use case that ties the others together. It is the only module that calls `eligibility/` with real data. It is stateless in v1.0 (ADR 0001). |
| `review/` | Read-only admin views: review tasks, change history, audit log | Kept apart from `schemes/` because admin concerns (who changed what, what needs re-checking) differ from the public catalogue. Keeping them apart also makes it obvious that no public route exposes unpublished data. |

### Deliberately absent from `app/`

| Not created | Why |
| --- | --- |
| `auth/`, `users/` | No accounts in v1.0 (ADR 0001). Admin protection is basic auth at Caddy, not app code. |
| `workers/` | No Redis/RQ in v1.0. The source checker is a script run by GitHub Actions (ADR 0003). |
| `notifications/` | Nothing to notify without accounts. Arrives with deadline reminders in v1.1. |
| `analytics/` | No stored answers, so there's nothing to analyse yet. Any future analytics must be aggregate and must not contain answers. |
| `recommendations/` (as a module) | Covered by `assessment/`. Results are computed per request, not stored. |

Creating empty folders for features you aren't building makes the project look bigger than it is. It also invites code to grow in the wrong place. Add each module when its ADR says so.

---

## The rest of `backend/`

| Folder | Why |
| --- | --- |
| `alembic/` | Alembic's default migration folder name. Every schema change is a reviewed, versioned migration, never a manual `ALTER TABLE`. Created on Day 9 with `alembic init`. Delete the `.gitkeep` first, because `alembic init` expects an empty or missing folder. |
| `scripts/` | Command-line entry points that aren't part of the web app: `validate_research.py` (CI), `load_research.py` (seed the database from YAML), `check_sources.py` (scheduled checker). They are scripts, not API routes, because they run outside a request. They import from `app/` so validation uses the same Pydantic models as the API. |
| `tests/unit/eligibility/` | Truth tables, operators and rule validation: the code that most needs exhaustive tests. It gets its own folder because these tests need no database and run in milliseconds. |
| `tests/integration/` | API + real PostgreSQL. It checks that routes, services, repositories and the database work together. |
| `tests/data_quality/` | Checks over `research/*.yaml`, as listed in Part 13.5 of the blueprint: every published version has a tier-1 source, ids are unique, no amount periods overlap. These are kept separate because they test the **data**, not the code, and they fail when research is wrong. |

---

## `research/`: why one folder per scheme, one file per year

- **A folder per scheme** keeps a scheme's identity (`scheme.yaml`) and all of its years together, so a PR that adds 2027 sits next to 2026.
- **A file per year** follows the blueprint rule never to overwrite old rules (Section 6.6). Adding a year is a new file, and an old year's file never changes once published. CI enforces that by rejecting edits to published version files.
- **The shared registers** (`sources.yaml`, `organisations.yaml`, `institutions.yaml`) sit at the top level because many schemes refer to them. Duplicating a SUSI source inside every scheme folder would let copies drift apart.
- **Naming:** folder names are the scheme id in lowercase (`SCH-ROI-RTC` → `sch-roi-rtc`). File names use the year (`2026.yaml` for a tax year, `2026-27.yaml` for an academic year). Ids never change once used.
- **`notes/`** is for working notes and questions. The loader never reads it, so nothing there can reach a student by accident.

---

## `infrastructure/` and `docker-compose.yml`

- `caddy/` holds the Caddyfile. Caddy handles HTTPS certificates automatically, proxies `/api` to FastAPI, serves the built frontend, and puts basic auth on `/admin` (R9, ADR 0001).
- `scripts/` holds `backup.sh` and `restore.sh`. A backup is only real once a restore has been tested (Week 4, Day 26).
- `docker-compose.yml` is at the **repo root**, not in `infrastructure/`, because `docker compose` looks for it in the current directory. It also describes the whole app (API, database, Caddy), not just infrastructure.
- There is no `terraform/` folder until v1.1 (ADR 0003).

---

## Config files at the root

| File | Why |
| --- | --- |
| `.gitignore` | Stops secrets (`.env`), dependencies (`node_modules/`, `.venv/`), build output, database dumps and Terraform state from ever being committed. The Terraform lines are there from day one so a v1.1 state file can't slip in. |
| `.editorconfig` | Consistent whitespace across editors: 4 spaces for Python, 2 for everything else, LF line endings. It keeps diffs clean, and clean diffs matter when PR review *is* the verification step. |
| `README.md` | What GrantEd is, the v1.0 scope, and an honest status table. The table follows Rule 9 of the blueprint: designed ≠ implemented ≠ verified. |
| `CLAUDE.md` | Instructions Claude Code loads at the start of every session: how to work with me, the rules that must never break, and the project conventions. It imports the README, architecture, ADR index and progress log. |
| `.claude/settings.json` | Shared Claude Code permissions. Tests, lint and read-only git run without asking. Commits, pushes and installs ask first. `.env` reads and force-pushes are denied. |
| `.claude/skills/` | Project commands: `/feature` (the 9-step build cycle), `/research-record` (draft a sourced scheme record) and `/handover` (end-of-session update). |
| `docs/progress.md` | Task list, open questions, bugs, technical debt and the session log (Part 19, Rule 8 of the blueprint). |
| `docs/blueprint.md` | The full product blueprint, for reference. Not imported into CLAUDE.md because of its size. |

---

## Questions to check your understanding

1. Why can `assessment/` import from `eligibility/`, but `eligibility/` must never import from `assessment/`?
2. Where would you add a new field such as `has_dependants`, and what breaks in CI if a rule uses it before you do?
3. A SUSI threshold changes for 2027/28. Which files change, which must not change, and how does the database find out?
4. Why are `tests/data_quality/` tests separate from `tests/unit/`, and when would one fail while the other passes?
