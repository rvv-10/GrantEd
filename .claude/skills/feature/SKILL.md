---
name: feature
description: Build one GrantEd feature as a small, explained, tested vertical slice using the project's 9-step learning cycle. Use when starting any new feature or backend/frontend change.
argument-hint: <feature or issue to build>
---

# /feature — build one feature the GrantEd way

Feature requested: $ARGUMENTS

Work through these steps **in order**. Pause where marked ⏸ and wait for my reply before continuing.

1. **Problem.** Restate what we're building and what "done" looks like, in 2–4 sentences. If an issue number was given, read it first.
2. **Scope check.** Check that this belongs in v1.0. Look at `docs/progress.md` and the ADRs in `docs/decisions/`. If it needs accounts, Redis/RQ or Terraform, stop and say so.
3. **Design.** Propose the smallest complete vertical slice. List every file you'll create or change, and which module each belongs to. Show how it respects the dependency rule (`eligibility/` imports nothing else from `app/`). ⏸
4. **Trade-offs.** For each important decision, give the alternative and why it wasn't chosen. Name any new dependency and justify it.
5. **Plan.** Break the work into 3–6 small steps, each one testable.
6. **Implement, one step at a time.** After each step, explain the 2–3 most important parts of the code, not every line.
7. **Test.** Write the tests, run them, and show the result. If a test fails, explain why before fixing it. Never hide a failure.
8. **Review.** Check edge cases (missing values, UNKNOWN, boundaries), error handling, input validation, security, and that no assessment answer is logged or stored.
9. **Learn.** Summarise in 4–6 bullets what I should be able to explain in an interview. Then ask me 1–2 short questions about how the code works. ⏸

Finish by adding the result to `docs/progress.md` with an honest status (implemented / tested — not "done" unless tested).
