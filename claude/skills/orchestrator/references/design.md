# Design playbook

M is the person you are working for. M makes every decision this playbook sends to them.

This playbook is about interface design: what a person sees and does on a screen. System design (contracts, reliability, threat models) is `references/design-checklist.md`.

It holds no design rules of its own. The rules live in three source folders copied unmodified from other projects, under `references/design/`, and in one file of ours, `references/design/ADAPTATIONS.md`, that says where M's rules win.

## When it runs

- **As the lane:** M asks for interface design work itself: a design direction, a critique or audit of an interface, an accessibility audit, a user flow, interface copy. The output is findings or a design direction, in the reply shape of core section 2.
- **Inside another lane:** the plan, build, and review playbooks send you here when their work touches a user interface. Read this file, then return to that playbook's own steps.
- **For visuals made for M:** before the first rendered HTML visual of a session (core section 2.4).

Design work that changes code is never done from this playbook alone. It chains: Design -> Plan -> Build, with the sign-off and checks those playbooks require.

## Steps

1. Read `references/design/ADAPTATIONS.md` in full before any source file, every session. It overrides the source text wherever the two conflict.
2. Pick one row from the first table below and read that entry file in full. Read the further files it names only when it tells you to, at the path it gives.
3. Every path in this file is relative to the skill folder, like every playbook path. A link inside a source file resolves from the folder of the file that holds it. A path in a source file that points into an original skill's folder that is not here (for example the `references/...` paths in `references/design/intent/wireframe.md` and `references/design/intent/present.md`) is a dead link.
4. Apply what the source file says inside the lane that is running. When this playbook is the lane, stop for M before anything is built: present the findings or the direction, and the next step is a plan.
5. When a subagent is handed a source file, give it the absolute path of that file and of `references/design/ADAPTATIONS.md`.

## Which source for which task

| The work | Entry file | What it routes to |
| --- | --- | --- |
| A new marketing or landing page; a full redesign of one only when M names Hallmark or asks for a new visual system | `references/design/hallmark/WORKFLOW.md` | page anatomy, layout, type and colour rules, imagery, about 90 numbered checks in `references/design/hallmark/references/gates.md` |
| Building, refining, critiquing, auditing, or polishing any interface, and every other change to an existing page: app screens, dashboards, components, docs pages, landing pages | `references/design/impeccable/WORKFLOW.md` | one file per task under `references/design/impeccable/reference/` (shape, critique, audit, polish, typeset, layout, colorize, animate, clarify, harden, adapt, optimize, and more) |
| UX reasoning: the problem, research, flows, navigation, copy, screen states, accessibility, ethics, measurement, handoff | `references/design/intent/intent.md` | one file per discipline under `references/design/intent/`, and eight reference files under `references/design/intent/references/` |

## Direct lookups

Use these without the entry file when the need is narrow. `references/design/ADAPTATIONS.md` still comes first. Every path in this table is under `references/design/`.

| Need | File under `references/design/` |
| --- | --- |
| The quality floor to read just before an interface edit, and before making an HTML visual for M (on a Hallmark run, the Floor in `hallmark/WORKFLOW.md` governs instead) | `impeccable/reference/craft-floor.md` |
| What changes when the visitor is deciding, doing a task, or reading | `impeccable/reference/mode-persuade.md`, `mode-operate.md`, `mode-read.md` |
| Every state a screen needs (empty, loading, error, and the rest) and edge cases | `intent/fortify.md` |
| An accessibility audit | `intent/include.md`, then `intent/references/accessibility-foundations.md` |
| Deceptive and manipulative patterns to refuse, with severities | the anti-pattern catalog in `intent/intent.md`, then `intent/references/ethical-design.md` |
| A heuristic review of an existing interface | `intent/evaluate.md`, or `impeccable/reference/critique.md` with `critique-rubric.md` |
| Interface copy: labels, errors, empty states | `intent/articulate.md`, `impeccable/reference/clarify.md` |
| States and focus rules for controls and forms | `hallmark/references/interaction-and-states.md` |
| Responsive rules and dark mode | `hallmark/references/responsive.md`, `hallmark/references/dark-mode.md` |
| A project's design system file (`DESIGN.md`) and product file (`PRODUCT.md`) | `impeccable/reference/document.md`, `impeccable/reference/init.md` |
| Native iOS or Android screens | `impeccable/reference/ios.md`, `android.md`, `audit.native.md`, `adapt.native.md` |

## Sources and licences

| Folder | Copied from | Pinned at | Licence |
| --- | --- | --- | --- |
| `references/design/hallmark/` | [Nutlope/hallmark](https://github.com/Nutlope/hallmark) | commit `04977e1` (2026-11-11), skill version 2.0.0 | MIT, `LICENSE` in that folder |
| `references/design/impeccable/` | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | commit `d41d986` (2026-11-16), skill version 4.9.2 | Apache-2.0, `LICENSE` and `NOTICE.md` in that folder |
| `references/design/intent/` | [ghaida/intent](https://github.com/ghaida/intent) | commit `1bc00bb` (2026-11-18), v1.7.5 | CC0-1.0, `LICENSE` in that folder |

Two Impeccable files, `reference/ios.md` and `reference/android.md`, derive from a fourth project, ehmo's `platform-design-skills` (MIT), as `references/design/impeccable/NOTICE.md` records.

The copied files are unmodified. `references/design/ADAPTATIONS.md` lists what was renamed and what was left out.
