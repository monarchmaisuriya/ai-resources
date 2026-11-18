# Plan playbook

M is the person you are working for. M makes every decision this playbook sends to them.

A plan that lives only in chat does not exist; chat scroll is not a task tracker. The plan file is a lightweight design doc: who it is for, what is true today, the options, the decision, the design, the cost, and the parts.

Every plan is written against the ten principles in `references/ten-principles.md`. Read that file before writing.

**Opener.** Send it as soon as step 3 chooses a full plan, and wait for M's yes before writing the file. A light plan needs no opener: say in one line that you are writing a short plan, and start.

> This needs a full plan. Suggested cycle: plan -> a fresh reader checks the plan (no ask) -> I present a briefing to you -> implement part by part -> check each part. OK to start with the plan?

## Steps

1. **Reach full intent.** M's wording is a guess at the goal. Read the code and docs first, batch the questions, and restate the goal until M confirms it in their own words. If the work is complex or the approach is unclear, explore 2 to 4 options first, by explore options mode in `references/research.md` (read it in full first), and get M's pick before writing a plan.
2. **Verify the current state.** Read every code path in scope. Every "how it works today" claim in the plan is tagged `[verified: file:line]`; nothing is written from memory. When the code is unfamiliar, first map its pieces, the ways in, the actors and what each is trusted to do, the data that survives a restart, and where the auth checks live. Then, for each function on the changed path (the functions you will edit and their direct callers), note what it assumes and the line that makes it true, or `nothing found`; these notes, and the map, go under What is true today.
3. **Choose the size** (light or full, below), then write the plan file from the template. Read `references/rubric-plan.md` first and write so the plan would pass it. Write for a reader who never saw this conversation: every decision, constraint, and rejected option the plan relies on is in the file. Before writing a full plan, state the intended direction in three lines and get M's nod. That three-line direction is the only rough version M sees; the plan itself reaches M only after the check.
4. **Run the independent check without being asked** (below). Never present an unchecked plan.
5. **Present the chat briefing** (template below), then the In plain terms block.
   - Do not paste the plan file, the parts checkboxes, the full check result, or long diagram source or tables into chat.
   - Link the file.
   - Wait for explicit sign-off ("yes, exactly", "approved", "go", "implement", "ship it"); "looks good" or silence is not sign-off.
   - Then set Status to `approved`, set any decision record M has now decided to `accepted`, and follow "After sign-off" (below).
6. **Huge plans** (about 5 or more parts that do not depend on each other): in the briefing's `Questions for M`, offer to give each part to its own subagent, and wait for M's answer. Each dispatch names the part, the files it owns, the done condition, "fix the cause, not the symptom", what the subagent may not do without asking, and the absolute path of `references/plan.md`. Each subagent reads only that file's "After sign-off" section, follows its rules 2 to 5, and does not open `references/build.md`. The parent session does the sign-off check, the worktree offer, the write-scope rescan, and every plan-file update (the ticks, Status, and Changes made line in rule 3).

## Light plan or full plan

Size the plan to the cost of being wrong (principle 5).

- **Light plan:** the change is cheap, easy to undo, touches no public contract, no stored data, and no auth, and changes nothing for users or on a live system. Its Design section also says how to undo the change. Use six content sections only: Context and problem, Goals / Non-goals, What is true today, Design, Acceptance criteria, Parts. The three record sections (Independent check, Changes made, Open questions) are in every plan.
- **Full plan:** everything else. Use every section.

In maximal session mode (`references/modes.md`), always use the full plan, and run the maximal opening step that file describes before the first action.

State the size and the reason on the Status line. When unsure, use the full plan. In a light plan, `nothing found` assumptions are listed under What is true today, a decision M has not made goes under Open questions, decisions already made and options rejected go under Design, and the briefing leaves out the fields whose sections the plan does not have.

## Plan file

- Location: `docs/` inside the touched repo; `docs/` at the workspace root when several repos are touched; ask M when unclear. Create `docs/` if missing.
- Name: `YYYY-MM-DD-HHMM_<short-topic-slug>_plan.md`, for example `2026-09-04-1430_add-auth_plan.md`. Read the date and time from the system clock (`date`); never estimate it.

Start every plan with an H1 title, `# <Plan title>`, followed by:

- Date: 2026-09-04 14:30
- Status: planning | approved | in progress (part 2 of 5) | blocked (waiting on M) | done; light | full, because <reason>
- Repos: <names>   Folders: <in scope>   Files: <paths expected to change>
- Subagents: none | one per part (list which)

Then add these H2 sections in order:

- **Context and problem:** who this is for, what they are trying to do, and what they use today; what M sees today, in plain words; why it matters. State the function the solution must perform before naming any form it takes. State the constraints the solution must respect.
- **Goals / Non-goals:** observable outcomes and explicit exclusions. One success metric, one guardrail metric (a number that must not get worse), and the date the result will be checked after launch. For a change with no users and no live system, write `metrics: n/a` and the reason. In non-goals, name the tempting near-misses (work you would enjoy and could defend) and forbid part-time progress on them.
- **What is true today:** current behavior with every claim tagged by verified file evidence. Add a diagram (its form comes from the visual ladder in core section 2.4) when a flow, hierarchy, sequence, or causal chain has several meaningful steps; otherwise use one concise statement.
- **Options considered:** a table with one column per option and rows for how it works, why it fits our code, what it gives up, what it costs (effort, money, risk), failure conditions, evidence, and completeness x/10 (10 covers every edge case, 7 the happy path only, 3 a shortcut that defers real work). Follow it with `RECOMMENDATION: <option> because <one reason>`. Before proposing a rewrite or a new component, list what the existing one already handles and what it quietly depends on; rebuild only when the existing form cannot meet the required function, otherwise improve it. If good options are not obvious, rewrite the problem statement instead of adding options.
- **Decision:** what we will do, the structural reason (why this fixes the cause and not a symptom), consequences, and rejected alternatives, each with the evidence that ruled it out.
  - Tag each decision with who made it: `[decided: M, chat YYYY-MM-DD]` or `[proposed: agent]`. A proposed decision that is costly to reverse is also listed under Open questions.
  - A decision that is costly to reverse (a data model, a public contract, a vendor, a framework) also gets its own decision record: copy `references/adr-template.md` to `docs/adr/NNNN-short-title.md`, numbered in order with four digits starting at `0001`, in the repo that owns the decision, and link it here. Read `references/rubric-adr.md` first and write the record so it would pass it. A decision M has not yet made is recorded with status `proposed`.
  - Mark every assumption as an assumption, and carry it into every conclusion that depends on it.
- **Cost and scope:** effort, and the time budget it must fit; what gets cut first if time runs out; risk (what could go wrong, how likely, how bad); blast radius (everything that reads, calls, or depends on what changes); and whether the change can be undone. Base the effort estimate on how similar past changes actually went (from git history or earlier plans), then adjust for this one; with no such record, label the estimate an assumption.
- **Design:** target behavior, data changes, contracts touched, and migrations.
  - When a diagram materially clarifies the target flow, hierarchy, sequence, or causal chain, include it and mark changed nodes.
  - Then work through `references/design-checklist.md` and add each item that applies; write `n/a` with a reason for each one that does not (a light plan uses item 1 only).
  - When the design adds a config value, an auth or token flow, an outbound fetch, a dependency, a Kubernetes manifest, an IAM permission, or an LLM tool, also read the matching section of `references/misuse-patterns.md`.
  - When the change touches a user interface, read `references/design.md` in full, then `references/design/ADAPTATIONS.md` and the one source file it names for the task. Name the files you used in the Design section.
  - Say how it behaves on empty and invalid input, retries, timeouts, partial failure, concurrent use, and recovery, or why each does not apply.
- **Risks and mitigations:** a table with risk, likelihood, impact, mitigation, and how the failure would be noticed. Every `nothing found` assumption on the changed path becomes a row.
- **Acceptance criteria:** one Given/When/Then statement per behavior.
- **Rollout and rollback:** flags (with the condition for removing each), ordering, the condition for entering and leaving each stage, abort conditions, the last known safe state, how to undo, and cleanup afterwards.
- **Parts:** open with a file table: path, create / change / delete, why. Include docs, schema, config, and test files. Then H3 per part, checkbox tasks, and one observable done condition per task. Each part is small enough to review alone (about a day of work or less) and carries its own tests and doc changes. A part is ready when an engineer with this file and the code it names can implement it without making a design choice.
- **Independent check:** the result of the check (below).
- **Changes made:** one line per shipped part.
- **Open questions:** each unresolved decision or unknown, with who resolves it and how; or `None`.

For a version, framework, or API migration, add a parity table under Design. Give it one row per behavior or contract and these columns: item, today, after, status (`same`, `renamed`, `changed`, `removed`, `new`, or `unknown`), why it matters, and evidence. Flag every changed, removed, or unknown row to M in the briefing's `Questions for M`: what it is, why it matters, and a recommendation. Resolve each unknown or get M's acceptance before implementation.

Use diagrams to explain relationships, not to decorate the plan. Skip them for simple facts and short two-step changes. Keep side-by-side comparisons as tables.

**Keep it live.** After each part, update the file as "After sign-off" rule 3 says. The file is the source of truth for progress. When the work finishes, ask M whether to delete or keep it; never delete it on your own and never leave it unmentioned.

> Work is done. Delete `docs/2026-09-04-1430_add-auth_plan.md`, or keep it?

## Independent check

The session that wrote a plan knows things the plan does not say, so it cannot see the gaps a cold reader would hit.

1. **Fresh reader.** Launch one fresh `general-purpose` subagent. Give it only file paths, written as absolute paths: the plan, any decision record it links, this skill's `references/rubric-plan.md`, `references/rubric-adr.md` (for the decision record), `references/ten-principles.md`, `references/design-checklist.md`, and `references/misuse-patterns.md`, and the code, schema, test, and doc files the plan names; when the plan touches a user interface, also `references/design.md` and `references/design/ADAPTATIONS.md`. Never give it the conversation, M's request, or your reasoning. Its prompt carries the instructions in step 2 word for word, including the three severity definitions. If no subagent is available, tell M the check is not independent, run it yourself only with M's OK, and mark it `partial`.
2. **What it returns.** In its own words, what the plan proposes and how the current system works; then a verdict (pass, violation, or n/a, with evidence) for each rubric criterion; then one finding per violation with a severity: `blocking` (can cause wrong behavior, data loss, or a decision on false claims), `must-verify` (plausible and important; one check settles it), or `note` (usable now; a bounded improvement).
3. **Compare.** You, the author, compare the reader's account of what the plan proposes with M's request. A difference is a blocking finding.
4. **Answer every finding.** First try to refute each blocking finding: re-read every location it cites and look for the control that would prevent it. If it survives, it stands. Then give each finding exactly one of: `fix` (valid; name the edit), `contest` (wrong, with proof the reader could have read; M decides), or `decline` (notes only). Never contest a finding that says the reader could not follow something: if a cold reader could not, M cannot either.
5. **Re-check.** If round 1 had any blocking or must-verify finding answered `fix`, and the round limit in `references/modes.md` allows another round, apply the fixes, then launch a new fresh reader with the same inputs and nothing from the first round. The plan body never names a round or a reader; it cites `file:line`, a command and its output, or a doc link.
6. **Stop** when a round has no blocking or must-verify finding with a `fix` response, or at the round limit for the session mode in `references/modes.md` (the first check is round 1).
   - Whenever the check stops, apply the note-level `fix` responses.
   - At the limit, handle blocking and must-verify fixes, and the question to M, as `references/modes.md` says for the session mode.
7. **Record.** After the rounds stop and M has answered any question from step 6, write the result under `## Independent check`, starting with `independent` or `partial`. Use `present as is` when no blocking finding is open, `present after fixing N blocking` when N blocking findings are open and each has a proposed edit, or `do not implement until fixed` when any open blocking finding has no agreed fix; add `, N must-verify open` when that applies. Then the verdict per criterion; each finding with the response it got (`fix`, `contest`, or `decline`); every finding still open; and every contest waiting for M. A blocking or must-verify fix applied in the last allowed round is recorded as `Applied, not re-checked`, and the result line adds `, N applied not re-checked`. A later `## Review` section, if any, follows this one.

## After sign-off

Implementation follows `references/build.md`: read it in full before the first edit. The rules below are the short set every implementer keeps to, and the set a per-part subagent is given. A per-part subagent reads only this section.

The plan file now drives the work. Whoever implements it follows these rules; a different session or a subagent is given this file's absolute path so it can read them:

1. **One part at a time, in the order the plan gives.** The one exception: when M accepted the offer in step 6, independent parts run at the same time, each in its own subagent, and rules 2 to 5 still apply to each. Before starting a part, re-read the files it names; if they no longer match what the plan says is true today, stop, tell M, and update the plan before going on. A change to the Design or the Decision needs M's sign-off again.
2. **A part is done only when every done condition is observed**: run the command, read the output, and show it. Print one line per proving command: `VERIFY: ran <exact command>: PASS | FAIL | DID NOT RUN (exit <code>; <runner summary line>)`. A part that changes behavior ships with a test that failed before the change and passes after it.
3. **Pause after each part** for M to look, unless M said to run all parts. Then tick its boxes, update Status, and add one line under Changes made.
4. **Make no design choice the plan did not make.** A choice the plan left open goes back to M as a question, and the answer is written into the plan's Decision section, tagged `[decided: M, ...]`.
5. **Stop on any failure.** Tell M the exact error, what you think caused it, and what you propose, then wait.
6. **When the last part is done**, close by the final-ship list in the Done report section of `references/build.md`. Set Status to `done` only after any fresh-reader fixes have landed.

## Presenting the plan

Chat gets a briefing, not the plan file. The file is the source of truth; link it. Aim for one screen; `Questions for M` and migration flags are never cut to fit. Compress, but never leave out a decision, a risk that could change the decision, or a blocking finding. Then the In plain terms block. Always.

Open with the H2 heading `## Briefing: <plan title>`, then:

- **Bottom line:** <decision in 1-2 sentences; confidence: high | medium | low, and why>
- **Where we are:** checked plan at `<path>`; status: planning; waiting on sign-off
- **Need from M:** sign off to start Part 1, or request changes.

Next, a `Questions for M` block: every open question, numbered and standalone, with lettered options and a recommendation, so M can answer without opening the file. With none, write `Questions for M: none`.

Follow with short labeled paragraphs for:

- **Problem:** <who has it and what M sees today>
- **Today:** <current mechanism in one or two sentences>
- **Decision:** <what we will do> because <one structural reason>
- **Rejected:** <short; omit when no alternative was rejected>
- **What changes:** <outcome language; key contracts or files named>
- **Cost:** <effort, main risk, blast radius, and whether it can be undone>
- **Success:** <the metric and the date it is checked; omit for a light plan with `metrics: n/a`>
- **Parts:** 1 <name> -> 2 <name> -> 3 <name>
- **Risks:** <only what could change the decision>
- **Check:** <N blocking, N must-verify>; independent or partial; one line if anything blocks.
- **Open fixes:** one line per fix left open at the round limit, with its proposed edit; omit when none.
- **Applied, not re-checked:** one line per blocking or must-verify fix applied in the last allowed round; omit when none.
- **Contests:** one line per finding you contested, with your proof, for M to decide; omit when none.

End with the H2 heading `## In plain terms` and these fields:

- **The problem:** <concrete scenario, no unexplained jargon>
- **What changes:** <outcome language, not file paths>
- **Why:** <the structural reason this approach solves it>
- **What M gets:** <observable outcome after ship>

Do not repeat `Need from M` in the closing block.
