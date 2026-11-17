---
name: research-record
description: Draft or update a GrantEd scheme research record as YAML in research/ from official sources, with every condition, amount and date traced to a source. Use when researching a scheme or a source change.
argument-hint: <scheme id, e.g. SCH-ROI-RTC> [year]
---

# /research-record — draft a scheme record from official sources

Scheme: $ARGUMENTS

Read `research/README.md` and ADR 0002 first. Follow these rules without exception:

- **Only tier-1 sources verify.** These are government departments, SUSI, HEA, Revenue, Student Finance NI, official institution pages and official guidance documents. Tier-2 sources (Citizens Information, for example) can explain a rule. Tier-3 sources (news, blogs, forums) can only help you discover one.
- **Never invent or infer a rule.** If the source is vague, write `unknown`. Then add an open question to `docs/progress.md` saying exactly what is unclear.
- **For every condition, amount and date,** record the source id and a locator (section heading or paragraph) for the passage. Mark whether it's an `official` condition or an `interpretation` (your paraphrase).
- **Never edit a published version file.** If one exists for that year, create a new version file and write a `change_summary`.
- **Amounts** always carry `currency`, `benefit_type`, `paid_to` and `effective_from`, with `effective_to` if known. A rate that changes mid-year is two rows, not one.
- **Tax reliefs:** record who may claim (student, parent or other fee payer). Never present a parent's relief as money paid to the student.
- **Discretionary decisions** (committees, access offices) are `manual` conditions, never guessed.

Steps:

1. List the sources you'll use, with tier and URL. Add any missing ones to `research/sources.yaml`.
2. Read each source. If you can't fetch a page, say so and ask me to open it. Don't fill the gap from memory.
3. Draft `research/schemes/<scheme-id-lowercase>/scheme.yaml` (if new) and the year file.
4. Set `verification_status: awaiting_verification`. Never set `verified` or `published`; only I do that, after a 24-hour re-check.
5. Summarise the record in a table: condition → value → source → official/interpretation.
6. List the unknowns and open questions you found.
7. Suggest a branch name (`research/<scheme-id>`) and a PR description that fills in the research section of the PR template.
