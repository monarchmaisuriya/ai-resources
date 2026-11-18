---
name: design
description: Interface design references for M - visual craft, UX reasoning, screen states, accessibility, and patterns to refuse. Use when work touches a user interface - "design this page", "landing page", "redesign", "make this look better", "polish", "critique the UI", "audit the UI", "typography", "layout", "colour", "dark mode", "responsive", "empty state", "error state", "accessibility", "WCAG", "dark patterns", "user flow", "wireframe", "microcopy" - or when a plan, build, or review playbook says to load the design skill. Not for system or architecture design (that is the orchestrator's design checklist).
---

# Design

This file is an index. It holds no design rules of its own. The rules live in three source folders copied from other projects, and in one file of ours that says where M's rules win.

This skill is about interface design: what a person sees and does on a screen. System design (contracts, reliability, threat models) is `references/design-checklist.md` in the `orchestrator` skill.

## How to use it

1. Read `ADAPTATIONS.md` in full before any source file, every session. It overrides the source text wherever the two conflict.
2. Pick one row from the table below and read that entry file in full. Read the further files it names only when it tells you to, at the path it gives.
3. Every path below is relative to this folder (the folder that holds this file). A link inside a source file resolves from the folder of the file that holds it. A path in a source file that points into an original skill's folder that is not here (for example the `references/...` paths in `references/intent/wireframe.md` and `references/intent/present.md`) is a dead link.
4. This skill adds knowledge, never a process. The lane M's core rules chose still runs the work: plan, sign-off, build, review. Use the source files inside those steps.
5. When a subagent is handed a file from this skill, give it the absolute path of that file and of `ADAPTATIONS.md`.

## Which source for which task

| The work | Entry file | What it routes to |
| --- | --- | --- |
| A new marketing or landing page; a full redesign of one only when M names Hallmark or asks for a new visual system | `references/hallmark/WORKFLOW.md` | page anatomy, layout, type and colour rules, imagery, about 90 numbered checks in `references/hallmark/references/gates.md` |
| Building, refining, critiquing, auditing, or polishing any interface, and every other change to an existing page: app screens, dashboards, components, docs pages, landing pages | `references/impeccable/WORKFLOW.md` | one file per task under `references/impeccable/reference/` (shape, critique, audit, polish, typeset, layout, colorize, animate, clarify, harden, adapt, optimize, and more) |
| UX reasoning: the problem, research, flows, navigation, copy, screen states, accessibility, ethics, measurement, handoff | `references/intent/intent.md` | one file per discipline under `references/intent/`, and eight reference files under `references/intent/references/` |

## Direct lookups

Use these without the entry file when the need is narrow. `ADAPTATIONS.md` still comes first.

| Need | File |
| --- | --- |
| The quality floor to read just before an interface edit, and before making an HTML visual for M (on a Hallmark run, the Floor in `references/hallmark/WORKFLOW.md` governs instead) | `references/impeccable/reference/craft-floor.md` |
| What changes when the visitor is deciding, doing a task, or reading | `references/impeccable/reference/mode-persuade.md`, `mode-operate.md`, `mode-read.md` |
| Every state a screen needs (empty, loading, error, and the rest) and edge cases | `references/intent/fortify.md` |
| An accessibility audit | `references/intent/include.md`, then `references/intent/references/accessibility-foundations.md` |
| Deceptive and manipulative patterns to refuse, with severities | the anti-pattern catalog in `references/intent/intent.md`, then `references/intent/references/ethical-design.md` |
| A heuristic review of an existing interface | `references/intent/evaluate.md`, or `references/impeccable/reference/critique.md` with `critique-rubric.md` |
| Interface copy: labels, errors, empty states | `references/intent/articulate.md`, `references/impeccable/reference/clarify.md` |
| States and focus rules for controls and forms | `references/hallmark/references/interaction-and-states.md` |
| Responsive rules and dark mode | `references/hallmark/references/responsive.md`, `references/hallmark/references/dark-mode.md` |
| A project's design system file (`DESIGN.md`) and product file (`PRODUCT.md`) | `references/impeccable/reference/document.md`, `references/impeccable/reference/init.md` |
| Native iOS or Android screens | `references/impeccable/reference/ios.md`, `android.md`, `audit.native.md`, `adapt.native.md` |

## Sources and licences

| Folder | Copied from | Pinned at | Licence |
| --- | --- | --- | --- |
| `references/hallmark/` | [Nutlope/hallmark](https://github.com/Nutlope/hallmark) | commit `04977e1` (2026-11-11), skill version 2.0.0 | MIT, `references/hallmark/LICENSE` |
| `references/impeccable/` | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | commit `d41d986` (2026-11-16), skill version 4.9.2 | Apache-2.0, `references/impeccable/LICENSE` and `NOTICE.md` |
| `references/intent/` | [ghaida/intent](https://github.com/ghaida/intent) | commit `1bc00bb` (2026-11-18), v1.7.5 | CC0-1.0, `references/intent/LICENSE` |

Two Impeccable files, `reference/ios.md` and `reference/android.md`, derive from a fourth project, ehmo's `platform-design-skills` (MIT), as `references/impeccable/NOTICE.md` records.

The copied files are unmodified. `ADAPTATIONS.md` lists what was renamed and what was left out.
