# Session modes

M is the person you are working for. M makes every decision this file sends to them.

A session mode is one setting, chosen once per session, that sets how much detail and how many check rounds the playbooks spend. It has two values: `minimal` (the default) and `maximal`. It never switches off a rule. It is always called "session mode", because some playbooks use the bare word "mode" for other things.

A round is one pass by a fresh reader or reviewer over a plan, a research brief, a review target, or a code diff.

## What each session mode sets

| Setting | Minimal (default) | Maximal |
| --- | --- | --- |
| Rules that apply | all | all |
| Size of the work (light or full plan; short or full interrogation pass; quick lookup or research brief) | chosen by the cost of a mistake, as each playbook says | always the heavier one: full plan, full pass, research brief |
| Opening step, before the first action in the Research, Interrogate, and Plan lanes | none | the four statements in rule 6 below, then M confirms the restated task |
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

1. Ask once per session, in the first reply that is not in the Answer lane: `Session mode: minimal (default) or maximal?` Start the work in `minimal` in that same reply; do not wait. If M's reply does not choose, the session mode stays `minimal`. If M answers `maximal` and the task is in the Research, Interrogate, or Plan lane, restart it from the maximal opening step; in any other lane, continue the task and follow the maximal column from the next step.
2. Name it in every `Where we are` line as `session mode: minimal` or `session mode: maximal`. After the conversation is summarized, read it from the most recent `Where we are` line; if none is found, ask again (a subagent never does: rule 5).
3. M may switch at any time. The switch applies from the next step. Work already finished in the old session mode is not redone unless M asks.
4. A session mode never switches off a rule. In minimal, a change that is costly, hard to undo, or touches users, stored data, auth, or a public contract still gets the full plan or the full interrogation pass; the size is still the full one; every other row follows the minimal column.
5. A fresh reader or any other subagent is not told the session mode in its prompt, never asks for it, and never settles it. It does one pass and reports. In maximal, a plan's Status line may read `full, because maximal session mode`; `references/rubric-plan.md` accepts that reason, so a full plan for a cheap change is not a finding.
6. Maximal opening step. Before the first action, state: (a) what a top professional in this field would do here that M did not ask for; (b) the rawest material they would start from (source code, logs, the specification, measurements) instead of a summary; (c) the specific methods and tools they would use; (d) what, other than the agent or M, would show the result is right (a test, a measurement, an independent source). Then restate the task to ask for exactly that, and wait for M to confirm or change it. The aim is a deeper task, not a bigger one. The opening step comes after any homework the playbook does before its first question, and before the playbook's own opener or first step.
7. A setting a playbook needs that this file does not list: stop and ask M. Never guess a value.
