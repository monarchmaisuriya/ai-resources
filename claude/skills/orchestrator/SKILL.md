---
name: orchestrator
description: Index of M's seven working playbooks. Loads the one playbook that matches the request and nothing else. Use for any of these. Interrogate - "poke holes", "challenge my idea", "is this a good idea", "what am I missing". Research - "research", "look into", "which library", "compare X and Y", "how do others", "explore options". Plan - "plan this", work touching several files, a new feature, a refactor, a migration. Build - "implement", "go", "build it", "ship it", only after M approved a plan. Diagnose - "bug", "error", "broken", "failing", "slow", "why does", a pasted stack trace. Test - "write tests", "cover this", "is this tested", "audit the tests", "run the tests", a flaky test. Review - "review this", a plan, research brief, decision record, diff or PR, "security audit".
---

# Orchestrator

This file is an index. It holds no rules of its own beyond the six below. Every working rule lives in a playbook or in a file listed below.

## How to use it

1. Use the lane M's core rules already chose, and use the table below only to find its file. If M named a playbook, use that one. Only when no lane was chosen, pick from the table, and ask M if two fit or none fits.
2. Read the whole playbook file before the first action. Never read part of it, never work from memory of it, and read it again after the conversation has been summarized.
3. Read the other files a playbook names when it tells you to, at the path it gives: in full, unless the playbook names a section.
4. Every path in a playbook is relative to this folder (the folder that holds this file). When a playbook hands a file to a subagent, turn the path into an absolute one, and tell the subagent the absolute path of this folder as the base for every `references/` path it will meet.
5. Before the first playbook of a session, read `references/modes.md` in full and settle the session mode as it says. A subagent skips this rule.
6. When a playbook says "core section" followed by a number, it means that section of M's core rules (`CLAUDE.md`, which the agent tool loads every session). If that file is not in your context, say so in your reply, and draw any diagram as ASCII in a code fence.

## Playbooks

| Playbook | Use it when | File |
| --- | --- | --- |
| Interrogate | M wants an idea, feature, or proposal challenged | `references/interrogate.md` |
| Research | M needs a sourced answer, or 2 to 4 options compared | `references/research.md` |
| Plan | Work needs a written design and M's sign-off before it starts | `references/plan.md` |
| Build | M has signed off a plan and it is time to implement | `references/build.md` |
| Diagnose | Something is broken, wrong, slow, or confusing | `references/diagnose.md` |
| Test | Tests are to be written, audited, or run | `references/test.md` |
| Review | A plan, research brief, decision record, or code change needs judging, or a codebase needs a security audit | `references/review.md` |

## Files the playbooks use

Do not read these up front, except `references/modes.md`, which rule 5 reads first. A playbook names the others it needs.

| File | Used by |
| --- | --- |
| `references/modes.md` | all lanes (settled by rule 5) |
| `references/maximal-opening-step.md` | research, interrogate, plan (maximal session mode only; named by rule 6 of `references/modes.md`) |
| `references/ten-principles.md` | all seven |
| `references/misuse-patterns.md` | plan, build, diagnose, review |
| `references/rubric-plan.md` | plan, review |
| `references/rubric-adr.md` | plan, review |
| `references/rubric-research.md` | research, review |
| `references/rubric-code.md` | review |
| `references/adr-template.md` | plan |
| `references/design-checklist.md` | plan, review |
| `references/test-quality.md` | test |
| `references/doubles-and-fixtures.md` | test |
| `references/frameworks.md` | test |
| `references/flaky-tests.md` | test |
| `references/full-audit.md` | review |
| `references/security-audit/WORKFLOW.md` | review (security audit mode) |
| `references/security-audit/` (every other file) | named by `WORKFLOW.md` and `references/rubric-code.md`; inside that folder, file names are relative to it |
