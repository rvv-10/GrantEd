# ADR 0001: Guest-only, stateless assessment for v1.0

- **Status:** Accepted
- **Date:** 2026-11-17
- **Supersedes:** none
- **Revisit when:** v1.0 is deployed and the privacy documentation for stored data is ready (planned for v1.1)

## Context

The original blueprint has user accounts in the MVP: registration, login, saved schemes and application tracking.

GrantEd's assessment asks about household income, residency, dependants and, optionally, disability. Some of these answers are personal data under GDPR and UK GDPR. A disability answer can be special category data (Article 9).

The MVP has about 80–95 hours of development time in total (42 days at 12–15 hours a week). Accounts bring a lot of extra work:

- password hashing and reset flows
- email delivery
- session security
- account and data deletion
- retention rules
- a privacy notice that covers stored data

None of that work is needed to prove the product's core idea. The core idea is that a student answers questions and gets explained, sourced recommendations.

## Decision

v1.0 has no user accounts.

- The browser holds the student's answers in memory only.
- The frontend sends the answers to a stateless endpoint, `POST /api/v1/eligibility/evaluate`. It returns results and stores nothing.
- The API never writes assessment answers to the database, to logs or to error reports.
- Refreshing the page loses the answers. The results page offers "Edit answers" instead of saved results.
- The only authenticated area is the read-only admin view, protected by HTTP basic auth at the reverse proxy (see ADR 0002).

Saved schemes, application tracking and stored assessments move to v1.1.

## Alternatives considered

1. **Full accounts in v1.0, as the blueprint has it.** Rejected. It costs roughly two weeks of the six, and it needs privacy and retention work before launch.
2. **Anonymous stored assessments with a shareable link.** Rejected for v1.0. It still stores sensitive answers, so most of the GDPR work remains. And it adds little over keeping the answers in the browser.
3. **Keeping answers in browser `localStorage` so they survive a refresh.** Deferred. It is low-cost, but on a shared computer it leaves sensitive answers behind. If added later, make it opt-in and easy to clear.

## Consequences

**Positive**

- About a third of the roadmap's scope is removed.
- No stored personal data in v1.0. No account deletion, retention schedule or breach scope for stored answers.
- The evaluate endpoint is a pure function of the request. It is easy to test, cache-safe and simple to explain.
- The `users`, `assessments`, `assessment_answers`, `recommendations`, `saved_schemes` and `application_tracking` tables are not built yet. That gives fewer migrations and less code.

**Negative**

- Students cannot save results or come back later.
- No usage data about which schemes students match. Any analytics must be aggregate and must not contain answers.
- "Authentication" is a smaller interview talking point in v1.0. You can explain the deferral instead.

**What still applies (this decision reduces GDPR obligations; it does not remove them)**

- Processing answers in memory is still processing personal data. v1.0 still needs a privacy notice explaining what is asked, why, and that nothing is stored.
- The disability question can still involve special category data while it is processed. Keep it opt-in, never ask for a diagnosis, and seek data-protection advice before public launch.
- Request logging must exclude request bodies on the evaluate route. Write a test that proves this.

## Follow-up

- [ ] Add a test that the evaluate endpoint performs no database writes.
- [ ] Add a test that application logs contain no answer values.
- [ ] Write `docs/privacy.md` for the stateless model before deployment.
