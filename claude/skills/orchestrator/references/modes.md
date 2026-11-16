# Session modes

M is the person you are working for. M makes every decision this file sends to them.

A session mode is one setting, chosen once per session, that sets how much detail and how many check rounds the playbooks spend. It has two values: `minimal` (the default) and `maximal`. It never switches off a rule. It is always called "session mode", because some playbooks use the bare word "mode" for other things.

A round is one pass by a fresh reader or reviewer over a plan, a research brief, a review target, or a code diff.

## What each session mode sets

| Setting | Minimal (default) | Maximal |
| --- | --- | --- |
| Rules that apply | all | all |
| Size of the work (light or full plan; short or full interrogation pass; quick lookup or research brief) | chosen by the cost of a mistake, as each playbook says | always the heavier one: full plan, full pass, research brief |
| Opening step, before the first action in the Research, Interrogate, and Plan lanes | none | the seven steps in `references/maximal-opening-step.md` (rule 6), then M confirms the proposed assignment |
| Round limit for a plan check, a research brief check, and a review | 1 | 2; when round 2 leaves a blocking or must-verify fix open, M is asked whether to run one more; never more than 4 in total |
| Blocking or must-verify fix found in the last allowed round of those three | applied by the author with no question to M, only where the playbook lets the author apply its own `fix` responses (never in the cases the Review playbook lists under "Who reviews": work of unknown authorship, an accepted decision record, work no agent wrote, where the finding is reported and M decides); listed in the briefing or review brief as `Applied, not re-checked` | left open with its proposed edit; M is asked: present as it stands, run one more round (while under 4; it applies the open fixes first, only where the playbook lets the author apply its own `fix` responses), or redraft |
| Fresh reader on the final build diff (runs only on M's yes, in both session modes) | 1 pass; each blocking or must-verify fix is proven by its test; no second reader | a second pass after a blocking fix; if that pass also finds a blocking issue, M is asked whether to run one more; never more than 4 in total |
| A re-check after M rules on a contested finding | not run; the fix is recorded as `Applied, not re-checked` in the artifact's check or review section | run without asking M while fewer than 4 rounds have run, and it counts toward the limit of 4; at 4 it is not run and the fix is recorded as `Applied, not re-checked` |
| Research search limit | 20 minutes; the 60-minute setting still applies when M asks or the decision is costly to reverse | 60 minutes with parallel subagents, always |
| Research sources for a brief | at least 5 credible, one primary | at least 8 credible, two primary; the brief's `## Method` section states this minimum |
| Explore options: approaches offered | 2 to 3 | 3 to 4; fewer, with the reason stated, when fewer real ones exist (never an invented option) |
| Interrogate closing rounds | riskiest assumption only; the brief and the final message write `not covered: minimal session mode` under `Premortem checks` | premortem and riskiest assumption |
| Diagnose and Test playbooks | unchanged | unchanged |

## Rules

### 1. Session mode selection

Ask M exactly once per session, in the first reply that is not in the Answer lane:

`Session mode: minimal (default) or maximal?`

- Begin working in minimal session mode immediately in that same reply. Never delay the task while waiting for M's answer.
- If M does not explicitly select a session mode, the session remains in minimal.
- If M selects maximal:
  - In the Research, Interrogate, or Plan lane, restart the task from the maximal opening step (rule 6).
  - In every other lane, continue the current task without restarting and apply the maximal column from the next step onward.
- Do not ask the selection question again during the same session, except as rule 2 says after a conversation summary.

### 2. Session mode persistence and identification

Every `Where we are` line MUST explicitly identify the active session mode using exactly one of these formats:

- `session mode: minimal`
- `session mode: maximal`

After a conversation summary, determine the active session mode exclusively from the most recent `Where we are` line.

- If such a line exists, resume using its recorded session mode.
- If no such line exists, ask M to select a session mode using the question in rule 1.
- Exception: fresh readers and subagents must never ask for or establish the session mode. Rule 5 governs them.

### 3. Switching session modes

M may switch between minimal and maximal session mode at any point in the session.

- A requested switch takes effect from the next step onward, not retroactively.
- Preserve all work completed under the previous session mode.
- Never repeat, expand, or redo completed work solely because the session mode changed.
- Redo previously completed work only when M explicitly requests it.
- A switch to maximal in the middle of a task does not run the maximal opening step for that task unless M asks. Rule 1's restart, on M's first answer, is the only automatic restart.

### 4. Mandatory rules and minimal-mode exceptions

Session mode controls the depth of execution; it NEVER disables mandatory safeguards, requirements, or quality standards.

In particular, minimal session mode does not permit a light plan or a short interrogation pass for a change that the playbook sizes as full. The sizing test has one home: `references/plan.md` for plans and `references/interrogate.md` for interrogation (a change that is costly, hard to undo, or touches users, a live system, stored data, auth, or a public contract).

For any such change in minimal session mode:

- The plan or interrogation pass is the full one, with its full prescribed sections.
- Every other row of the table still follows the minimal column (round limit, last-round fixes, re-check, sources, closing rounds).

In short: minimal session mode reduces unnecessary work, not necessary rigor.

### 5. Fresh readers and subagents

A fresh reader or any other subagent operates independently of the session mode.

Its instructions MUST NOT:

- Disclose or communicate the active session mode.
- Ask M to choose a session mode.
- Attempt to establish the session mode (reading a plan's `Status` line that names it is not establishing it).
- Change or override the session mode.

Instead, the subagent must:

1. Perform one independent pass over its assigned task.
2. Apply the requirements relevant to that task.
3. Report its findings and results.

Maximal-mode planning exception: when the main agent is operating in maximal session mode, a plan's `Status` line MAY state `full, because maximal session mode`. The `references/rubric-plan.md` rubric explicitly accepts this justification. Consequently, a full plan for an otherwise inexpensive change MUST NOT be reported as a finding or violation when maximal session mode is the reason for its scope.

### 6. Maximal opening step

In maximal session mode, before the first action of a task in the Research, Interrogate, or Plan lane, read `references/maximal-opening-step.md` in full and run every step of it, in the place its "Placement and precedence" part gives. It ends with a stop for M's confirmation. It runs once per task, as that file says. In minimal session mode, do not read that file.

### 7. Missing playbook settings

This rule covers only a value that depends on the session mode: one a playbook sends you to this file for, but the table above does not list. Every other decision is settled by the playbook that owns it.

For such a missing value:

- Stop at the point where that setting is required.
- Ask M to provide or confirm its value.
- Do not invent, assume, infer, or silently substitute a default.
- Resume only after M provides the missing information.

Governing principle: never guess a required setting.
