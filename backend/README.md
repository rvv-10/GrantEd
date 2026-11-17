# Backend

FastAPI modular monolith. Python tooling (`pyproject.toml`, pytest, ruff) is set up on Week 1, Day 6.

```
backend/
├── app/
│   ├── core/          # settings, database session, logging config (no business logic)
│   ├── api/v1/        # HTTP routes only: validate → call service → return schema
│   ├── eligibility/   # PURE engine: rule models, operators, 3-valued logic, explanations
│   ├── profile/       # field registry + the student profile schema
│   ├── schemes/       # schemes, versions, amounts, relationships
│   ├── sources/       # source register, change events
│   ├── institutions/  # providers and scheme participation
│   ├── assessment/    # stateless evaluation: profile → grouped, explained results
│   └── review/        # read-only admin: review tasks, change history, audit log
├── alembic/           # database migrations (Week 2)
├── scripts/           # validate_research.py, load_research.py, check_sources.py
└── tests/
    ├── unit/eligibility/   # truth tables, operators, rule validation
    ├── integration/        # API + PostgreSQL
    └── data_quality/       # checks over research/*.yaml
```

The rule that matters most: **`app/eligibility/` never imports from the rest of `app/`.**
