---
name: handover
description: End-of-session handover for GrantEd — update docs/progress.md with what was built, its honest status, what I learned, open questions, debt and the next task. Use at the end of every working session.
---

# /handover — end the session properly

1. Look at what changed this session: `git status`, `git diff`, and `git log` since the last handover entry in `docs/progress.md`.
2. Update `docs/progress.md`:
   - Tick completed tasks in **Current week**, and add new ones that came up.
   - Add an entry under **Session log** (newest first) covering:
     - **Built:** files and features, each with an honest status: designed / implemented / tested / verified with official data / deployed
     - **Learned:** 2–4 concepts I should now be able to explain
     - **Open questions:** research or technical questions that are still unresolved
     - **Next task:** the single most useful thing to do next session
   - Add any bug to **Known bugs** and any shortcut to **Technical debt**.
   - Record any new architectural decision under **Decisions needing an ADR**.
3. Check the README status table still matches reality. Propose a change if not.
4. Show me the diff of `docs/progress.md`, and suggest a commit message. Don't commit unless I say so.
5. End with one question that checks I understood the most important thing from this session.
