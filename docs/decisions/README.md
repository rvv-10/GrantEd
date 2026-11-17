# Architecture Decision Records

An ADR records one significant decision: the context, what was decided, the alternatives, and the consequences. ADRs are never rewritten after acceptance. A changed decision gets a new ADR that supersedes the old one.

| ADR | Decision | Status | Date |
| --- | --- | --- | --- |
| [0001](0001-guest-only-stateless-v1.md) | Guest-only, stateless assessment for v1.0 | Accepted | 2026-11-17 |
| [0002](0002-research-records-yaml-in-git.md) | Research records as YAML in Git, approved by PR | Accepted | 2026-11-17 |
| [0003](0003-defer-redis-rq-and-terraform.md) | Defer Redis/RQ and Terraform to v1.1 | Accepted | 2026-11-17 |

## Template

Copy this into `NNNN-short-title.md`:

```markdown
# ADR NNNN: Title

- **Status:** Proposed | Accepted | Superseded by ADR NNNN
- **Date:** YYYY-MM-DD
- **Supersedes:** none
- **Revisit when:** the condition that would reopen this

## Context
What problem or force makes a decision necessary?

## Decision
What we will do, stated plainly.

## Alternatives considered
Each option, and why it was not chosen.

## Consequences
Positive, negative, and what still applies.

## Follow-up
- [ ] Tasks this decision creates.
```
