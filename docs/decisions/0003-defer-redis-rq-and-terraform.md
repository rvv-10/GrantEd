# ADR 0003: Defer Redis/RQ and Terraform to v1.1

- **Status:** Accepted
- **Date:** 2026-11-17
- **Supersedes:** none
- **Revisit when:** source checks outgrow a scheduled job, or the manual deployment has been repeated enough to be worth codifying

## Context

The blueprint includes two pieces of infrastructure:

- **Redis with RQ** for background jobs, mainly checking official source pages for changes
- **Terraform** to define the AWS infrastructure as code

Both are valuable skills, but neither is needed for v1.0 to work.

- After ADR 0001, the only background work is a periodic source check. With about 15 schemes and about 20 sources, it runs in seconds. It has no user waiting on it, so it doesn't need a job queue.
- Terraform codifies infrastructure you understand. Writing it before you have deployed by hand once means learning AWS and Terraform at the same time, in the same week.

## Decision

**No Redis or RQ in v1.0.** The source checker is a plain Python script, `backend/scripts/check_sources.py`, run by a scheduled GitHub Actions workflow (`.github/workflows/source-check.yml`). The script:

- fetches each URL in `research/sources.yaml`, honouring `robots.txt` and spacing requests out
- compares `ETag` / `Last-Modified` headers where available, otherwise a hash of the normalised page content
- opens or updates a GitHub issue labelled `source-changed` when a difference is found
- **never** edits scheme data. A human updates the YAML and opens a PR (ADR 0002).

**No Terraform in v1.0.** In Week 4 the AWS setup is created by hand from a written runbook (`docs/deployment.md`). Terraform then codifies that known-good setup in v1.1.

## Alternatives considered

1. **Redis + RQ, as in the blueprint.** Deferred. It adds two containers (Redis and a worker), plus failure modes and monitoring, for a job that runs in seconds.
2. **Cron on the EC2 instance.** A reasonable fallback. It was rejected as the default because GitHub Actions keeps job history and logs, can open issues directly, and keeps the checker off the production server.
3. **Terraform from the start.** Deferred, for the learning-sequence reason above.

## Consequences

**Positive**

- Two fewer moving parts in production, and less to monitor and secure.
- Week 5 time goes to the review workflow and security instead.
- The checker is still a background job that never blocks a user request. That matches the blueprint's intent.

**Negative**

- GitHub's scheduled workflows can start late at busy times, so checks are approximate, not exact. That's fine for checks that run once or twice a day.
- GitHub can automatically disable scheduled workflows in public repositories after a long period with no repository activity. Check that the workflow is still enabled. The issue it opens also acts as a heartbeat.
- You can't claim Redis or Terraform as v1.0 skills. In interviews, explain why they were deferred and when you would add them.

**When to add them**

- **Redis/RQ:** when there is user-triggered asynchronous work (for example, emailed deadline reminders in v1.1), or when checks take long enough to need retries and concurrency.
- **Terraform:** in v1.1, once `docs/deployment.md` has been followed successfully at least once.

## Follow-up

- [ ] Write `backend/scripts/check_sources.py` (Week 5).
- [ ] Add `.github/workflows/source-check.yml` with a daily schedule and manual `workflow_dispatch` (Week 5).
- [ ] Write `docs/deployment.md` while deploying in Week 4.
