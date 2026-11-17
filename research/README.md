# Research data

This folder is the **source of truth** for every scheme GrantEd shows (ADR 0002). The database is a copy loaded from here. Never edit the database by hand.

## Layout (files are added from Week 1, Day 3 onwards)

```
research/
├── sources.yaml          # register of official sources (SRC-… ids)
├── organisations.yaml    # providers: SUSI, Revenue, SFNI, universities…
├── institutions.yaml     # higher-education providers + research checklist
├── schemes/
│   └── sch-roi-rtc/      # one folder per scheme, named by scheme id
│       ├── scheme.yaml   # permanent identity: name, provider, category, benefit type
│       └── 2026.yaml     # one file per tax year or academic year (e.g. 2026-27.yaml)
└── notes/                # free-form working notes; never loaded into the app
```

## Rules

1. Every condition, amount and date cites a source id from `sources.yaml`.
2. Unknowns are written as `unknown` and linked to a question, never left blank.
3. A published version file is never edited. A correction is a new version file.
4. A record is "verified" only after a 24-hour re-check against its sources, recorded in the PR.
5. Tier-3 sources (news, forums) can help you discover a scheme, but never verify one.
