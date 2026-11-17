# ADR 0002: Research records as YAML in Git, approved by pull request

- **Status:** Accepted
- **Date:** 2026-11-17
- **Supersedes:** none
- **Revisit when:** someone other than the project owner needs to edit scheme data, or the catalogue grows past what is comfortable to edit as files

## Context

GrantEd is only as trustworthy as its scheme data. The blueprint requires:

- every published fact to have an official source
- no unverified change ever published
- previous versions kept
- an audit trail of who approved what and when

It also plans a web admin interface for adding, editing, reviewing and publishing schemes.

Building that interface well means forms, validation feedback, review queues, roles, approval actions and audit logging. That is one to two weeks of work. Git and GitHub already provide history, review, approval and an audit log.

## Decision

The source of truth for scheme data is a set of YAML files in `research/`, reviewed and approved through pull requests.

- Each scheme lives in its own folder, `research/schemes/<scheme-id>/`. Inside it, `scheme.yaml` holds the permanent identity and there is one file per academic or tax year, for example `2026-27.yaml`.
- The shared registers are `research/sources.yaml`, `research/organisations.yaml` and `research/institutions.yaml`.
- The backend's Pydantic models validate every file in CI. That means the same models the API uses, so there is one definition of a valid record. CI also checks:
  - that every condition field exists in the profile field registry
  - that every published version has at least one tier-1 source
  - that no two amount periods overlap
  - that an already-published version file has not been edited (a correction must be a new version file)
- **A merged pull request is the verification step.** The PR description records which sources were re-checked and when.
- After a merge, a seed script (`backend/scripts/load_research.py`) loads approved records into PostgreSQL. It is idempotent: running it twice changes nothing.
- The web admin in v1.0 is read-only: the catalogue, version history, change events and review tasks. It sits behind HTTP basic auth at the reverse proxy.
- The research spreadsheet ("GrantEd — Research Database Template") is for discovery and triage only. A record moves from the sheet into YAML once it reaches "Awaiting verification".

## Alternatives considered

1. **Web admin editor (blueprint plan).** Deferred to v1.1+. It is the right tool for non-technical editors, but there are none yet.
2. **The spreadsheet as the source of truth.** Rejected. It has no review step, weak validation and no link between a change and its approval.
3. **A headless CMS.** Rejected. It is a new hosted dependency with a cost, and it duplicates the validation logic outside the codebase.
4. **Editing records directly in PostgreSQL.** Rejected. There is no review step, and history depends on audit tables you would have to build first.

## Consequences

**Positive**

- History, diffs, review and audit come free from Git.
- One validation model shared by CI and the API.
- Records are reviewable as plain text in a PR, so a mistaken threshold is visible in the diff.
- It fits the rule that a detected change never auto-publishes. The source checker can only open an issue (see ADR 0003). A human must open and merge a PR.

**Negative**

- The project owner is the only reviewer, so approving your own PR is weaker than independent review. Mitigation: a 24-hour cooling-off re-check against the source before merging. Record it in the PR template.
- Editing YAML by hand is error-prone. CI validation is what catches the mistakes, so it must exist before the first real record is merged.
- Non-technical contributors cannot edit data. That is acceptable for v1.0.
- The database is a derived copy. It must never be edited by hand, or it drifts from Git.

## Follow-up

- [ ] Define the YAML shape for `scheme.yaml` and version files in `docs/database.md` (Day 5).
- [ ] Write `backend/scripts/validate_research.py` and run it in CI (Week 2 / Week 5).
- [ ] Add the "sources re-checked" section to the PR template (done in this commit).
