# Progress

Updated by `/handover` at the end of each session. Newest entries first.

## Current week: Week 1 (16–22 Nov 2026), research, design and test setup

- [x] Decide R1–R3 (17 Nov): guest-only v1.0, YAML research by PR, defer Redis/RQ and Terraform
- [x] Create GitHub repo (17 Nov)
- [ ] Commit scaffold: ADRs 0001–0003, README, PR template, folder structure
- [ ] Commit Claude Code setup: CLAUDE.md, .claude/settings.json, skills, this file
- [ ] Protect `main` (require PRs) and create a GitHub Project board with Week 1 issues
- [ ] Day 2: open every tier-1 source; fill `publication_date` and `last_checked`; move the 15 schemes to "Researching"; answer Q-006
- [ ] Day 3: Rent Tax Credit research record (tax year 2026, plus 2027 if published); answer Q-001
- [ ] Day 4: choose one ROI and one NI university and complete their checklists; finish the SFNI Student Contribution Loan record; answer Q-003
- [ ] Day 5: check the ER diagram against both records; write `docs/database.md` and `docs/fields.md`
- [ ] Day 6: write `docs/eligibility-engine.md`; set up `backend/pyproject.toml`, pytest, ruff; write `and3`/`or3`/`not3` with table-driven tests
- [ ] Day 7: 24-hour re-check of both records; handover; Week 2 issues

**Week 1 is done when:** ADRs are committed, both records are verified or have logged blockers, `database.md` and `eligibility-engine.md` exist, and `pytest` passes.

## Status by component

| Component | Status |
| --- | --- |
| Research database | Designed (template sheet exists; no YAML yet) |
| Data model | Designed (ER diagram v0) |
| Eligibility engine | Designed |
| API | Designed |
| Frontend | Not started (Week 3) |
| Source checker | Designed (ADR 0003) |
| Deployment | Designed (Week 4) |

## Open research questions

| Id | Scheme | Question | Blocking |
| --- | --- | --- | --- |
| Q-001 | SCH-ROI-RTC | Can a parent claim the Rent Tax Credit for a student child's rent, and under which conditions? | Yes |
| Q-002 | SCH-ROI-SUSI-MAINT | How will SUSI publish the Budget 2027 +4.5% maintenance increase from January 2027? | No |
| Q-003 | SCH-NI-SCL | What is the maximum Student Contribution Loan for 2026/27, and in which currency? | No |
| Q-004 | SCH-ROI-TFR | What is the ineligible amount for tax years 2026 and 2027? | No |
| Q-005 | Questionnaire | Which income-band edges line up with the SUSI 2026/27 thresholds? | Yes |
| Q-006 | Institutions | Which official registers define approved institutions for SUSI, and for SFNI study in ROI? | No |

## Known bugs

_None yet._

## Technical debt

_None yet._

## Decisions needing an ADR

_None yet._

## Session log

### 2026-11-17
- **Built:** repo created; ADRs 0001–0003, folder scaffold and Claude Code setup prepared (designed, not yet committed)
- **Learned:** why a guest-only, stateless v1.0 shrinks scope and GDPR exposure; how PR review can act as a verification workflow
- **Open questions:** Q-001 to Q-006 above
- **Next task:** commit the scaffold and setup, protect `main`, then Day 2 source review
