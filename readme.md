# AI Resources

A collection of reusable instructions, skills, and working playbooks for AI-assisted software development, organized around Claude.

## What's included

- **[Coding agent protocol](claude/CLAUDE.md)** — working conventions for choosing an approach, grounding claims in evidence, communicating clearly, and verifying results.
- **[Orchestrator skill](claude/skills/orchestrator/SKILL.md)** — an index that routes requests to the relevant playbook rather than loading every reference at once.
- **[Reference library](claude/skills/orchestrator/references/)** — principles, design checklists, review rubrics, testing guidance, and a decision-record template.
- **[Security audit resources](claude/skills/orchestrator/references/security-audit/README.md)** — audit workflows, domain-specific references, report schemas, and validation scripts.
- **[Design playbook](claude/skills/orchestrator/references/design.md)**: interface design references copied unmodified from three open-source projects ([Hallmark](https://github.com/Nutlope/hallmark), MIT; [Impeccable](https://github.com/pbakaus/impeccable), Apache-2.0; [Design with Intent](https://github.com/ghaida/intent), CC0-1.0), with an [adaptations file](claude/skills/orchestrator/references/design/ADAPTATIONS.md) that says where the protocol's rules win. Each source folder carries its own licence file.
- **[Session modes](claude/skills/orchestrator/references/modes.md)**: a once-per-session choice between `minimal` (the default) and `maximal` that sets how much detail and how many check rounds the playbooks spend.

## Working playbooks

| Playbook | Purpose |
| --- | --- |
| [Interrogate](claude/skills/orchestrator/references/interrogate.md) | Challenge ideas and identify missing assumptions. |
| [Research](claude/skills/orchestrator/references/research.md) | Investigate questions and compare options using evidence. |
| [Plan](claude/skills/orchestrator/references/plan.md) | Define a design and obtain approval before implementation. |
| [Build](claude/skills/orchestrator/references/build.md) | Implement an approved plan and verify the result. |
| [Diagnose](claude/skills/orchestrator/references/diagnose.md) | Trace failures to their causes before fixing them. |
| [Test](claude/skills/orchestrator/references/test.md) | Write, audit, and run meaningful tests. |
| [Review](claude/skills/orchestrator/references/review.md) | Evaluate plans, research, decisions, and code changes. |
| [Design](claude/skills/orchestrator/references/design.md) | Route interface design work to the copied design sources. |

## How to use these resources

1. Read the [coding agent protocol](claude/CLAUDE.md) and adapt its personal preferences and approval rules to your workflow.
2. Use the [orchestrator index](claude/skills/orchestrator/SKILL.md) to select the playbook that matches your task.
3. Read that playbook and the references it requests before starting work.
4. Keep the orchestrator directory structure intact so relative reference paths continue to resolve.

This repository is a resource library, not a standalone application. Review instructions before incorporating them into an agent's configuration, and preserve any license notices included with individual resources.
