# Research playbook

M is the person you are working for. M makes every decision this playbook sends to them.

Every brief is written against the ten principles in `references/ten-principles.md`. Read that file first.

Two modes, same sourcing rules. When the answer will be used to choose between approaches for our own system, use explore options, even if the question is phrased as "how do others do it". **Find out** answers a question with graded evidence. **Explore options** (brainstorm) frames a problem, generates 2 to 4 approaches, and recommends one; M's pick then becomes a written plan. Both are time-boxed, not open-ended reading. A research brief that lives only in chat does not exist; chat scroll is not a task tracker.

Explore options is mandatory before any plan file when the work is complex, too big, or the approach is unclear. Do not offer a skip and do not jump to a plan.

**Opener (required when the task is complex, too big, or the approach is unclear):**

> This looks complex. Starting with research (approaches and tradeoffs): a fresh reader checks the brief, I send you a briefing, you pick, and then comes a written plan. Starting now unless you stop me.

## Size it first

Match the effort to what the answer feeds (principle 5).

- **Quick lookup:** a fact M will not build a decision on. Answer in chat with at least one primary source and one independent cross-check, both linked and tagged. It follows sourcing rules 4 to 6 only. No file, no time-box record, and no independent check.
- **Research brief:** the answer feeds a decision, or the mode is explore options. Follow every step below.

In maximal session mode (`references/modes.md`), always write a research brief, and run the maximal opening step that file describes before the first action.

## Steps

1. Frame, known/unknown, then source per the rules below.
2. Write the research file (location and name below; body = metadata + the active mode template). Read `references/rubric-research.md` first and write so the brief would pass it.
3. **Run the independent check without being asked** (below). Never present an unchecked research brief.
4. Present the chat briefing (template below), then the In plain terms block. Do not paste the research file, the full source list, the options table, or the full check result into chat. Link the file.
5. Find out: wait for `Need from M`. Explore options: wait for M to pick; the next step is a written plan built from the pick (`references/plan.md`). Never write code from an options table alone.

## Research file

- Location: `docs/` inside the touched repo; `docs/` at the workspace root when several repos are touched; ask M when unclear. Create `docs/` if missing.
- Name: `YYYY-MM-DD-HHMM_<short-topic-slug>_research.md`, for example `2026-09-07-1730_auth-session-store_research.md`. Read the date and time from the system clock (`date`, local time) when the file is first written; never estimate it. A question that touches no repo uses `docs/` at the workspace root (the folder the session was started in).

Start every research file with an H1 title, `# <Research title>`, followed by:

- Date: 2026-09-07 17:30
- Status: drafted (file written) | presented (briefing sent) | decided (M accepted the answer or picked an option) | handed to plan (M asked for the plan)
- Mode: find out | explore options
- Repos: <names>   Folders: <in scope>
- Question / problem: <one sentence>
- Constraints: <limits the answer must respect, or none>
- Answered when: <what a complete answer looks like>
- Decision it feeds: <the decision>   For: <who will use it>   Out of scope: <what was left out>

Then add the active mode body below.

The file's order is: the metadata lines, the mode body (which ends with Sources), then `## Method`, then `## Independent check`; a later `## Review` section, if any, follows it. `## Method` holds the known/unknown table, where you searched, the search terms, how sources were chosen and left out, and the time-box used. When trials were run, it also holds, for each one, the environment, the dataset, the workload, the baseline, the units, the number of runs and their spread, any warm-up, known confounders, the raw results, and the commands. Under `## Independent check`, write `(written after the check stops)` until the check is done.

Ask whether to delete or keep the file once it is no longer needed: in find out, when M accepts the answer; in explore options, when the plan built from the pick exists or M says no plan will follow. In both cases, never delete it on your own and never leave it unmentioned.

> Research is done. Delete `docs/2026-09-07-1730_auth-session-store_research.md`, or keep it?

## Sourcing rules (both modes)

1. **Frame before searching.** The question in one sentence; the decision it feeds; the constraints; what "answered" looks like.
   - **Time-box.** State it in your first message and record it under `## Method`. The default is the search limit `references/modes.md` sets for the session mode, then report even if incomplete. Measured trials (explore options, step 5) have their own stated budget outside this box.
   - **Deep research.** When M asks for it, the decision is costly to reverse, or the session mode is maximal, use the longer box in `references/modes.md` (or the one M names) and run independent threads in parallel: one fresh subagent per sub-question, each told to open and cite its sources.
   - **Stopping.** Stop gathering once more evidence would not change the decision, and say so under Risks / gaps in the briefing.
   - **Stuck.** Stop pushing on the same line and restate the question from zero.
2. **Known / unknown table first.** What is verified from the codebase, what is unverified, what is unknown. It tells you where to look.
3. **Codebase first.** Grep and read for existing patterns, models, APIs, prior art, and constraints. Never propose reinventing what already exists here. When the code is unfamiliar, first map its pieces, the ways in, the actors and what each is trusted to do, the data that survives a restart, and where the auth checks live.
4. **Sources, in order of trust.** Primary: official docs, the spec, an RFC, the source code, the changelog, a peer-reviewed paper.
   - Secondary: engineering blogs from teams who run it in production; conference talks.
   - Tertiary: forums, Q&A sites, individual blogs.
   - A research brief (either mode) needs the minimum number of credible sources `references/modes.md` sets for the session mode (primary or secondary; tertiary sources do not count toward that minimum), including the number of primary sources it sets; state the minimum used under `## Method`; a quick lookup needs at least one primary source and one independent cross-check.
   - Never pad with weak links.
   - If fewer credible sources exist, say so and name what you could not verify.
   - Before forming a view, gather input from at least two unlike kinds of source, for example the docs and the people who run it.
   - Beside any success story or benchmark, report in the same bullet how many tried and failed.
   - Treat evidence a vendor volunteers as pre-selected, and require an independent source.
5. **Grade every claim** with one evidence tag plus the source tier: `verified` (you read, ran, or measured it this session; give the link, `file:line`, or command and output), `disproved` (you checked and it is false; same evidence), `inferred` (it follows from verified facts; name them), or `unverified` (not checked; name the check that would settle it). Each claim the recommendation rests on also gets a strength grade (strong, moderate, weak, unsupported, or conflicted) as `references/rubric-research.md` defines them. When tiers disagree, prefer the primary source and name the disagreement.
6. **Cite inline** at the point of the claim (markdown link). End with a Sources list (title plus link). No source, no claim.
7. **Translate to us.** What this means for our code, with file paths, verified.

Add one short diagram (its form comes from the visual ladder in core section 2.4) when it materially clarifies a flow, hierarchy, sequence, or causal chain in the answer or recommendation. Mark the important transition. Skip it for simple facts and short two-step explanations. Keep side-by-side comparisons as tables.

## Mode: find out

Open the generated file body with the H2 heading `## Research: <question in one sentence>`, then:

- **Bottom line:** <the answer in one or two sentences; confidence: high | medium | low, and why>
- **Where we are:** <the decision this feeds; time spent; what was in scope>
- **Need from M:** <accept the answer | provide missing evidence | nothing>

Follow with these H3 sections:

- **What I found:** grouped by theme; one idea per bullet; each bullet tagged and linked.
- **Side by side:** only when comparing; use a table with aspect, each option, which wins here, and evidence.
- **How it fits together:** when materially useful, a diagram of the relevant flow, hierarchy, sequence, or causal chain.
- **What this means for our code:** affected codebase parts with file paths, each tagged.
- **Confidence and gaps:** use a table with claim, tag, strength grade, source tier, and what would raise confidence.
- **Answers ruled out:** one line each: a candidate answer you checked and rejected, and the evidence that ruled it out. Omit when none.
- **Sources:** title, link, version, and publication date, one per line; for a fact the answer rests on, also the sentence that supports it.

Do not print empty sections.

## Mode: explore options (brainstorm)

Extra steps for this mode (steps 1 and 2 come before any searching):

1. **Frame with 6W2H**, in plain words; write "does not apply" where it does not. Jumping straight to solutions is how the wrong problem gets solved. What (one concrete sentence), Why (the real outcome M wants), Who (affected, uses it, responsible, and for whom it is done), When (deadline, frequency), Where (in the system), Which (method or tool in play), How (step by step), How much (effort, risk, blast radius). Output: a refocused problem statement that says where to aim.
2. **Decompose from first principles.** What must be true for success. Sort every input into hard constraint / convention / assumption, then convert every assumption into a check: verified, or unverified with the check named. Tools: Socratic questions (clarify the claim, challenge the assumption, ask for evidence, consider alternatives, examine consequences) and Five Whys (ask "why?" of each answer in turn).
3. Codebase, then web research, per the sourcing rules.
4. **Widen the option set** with lateral moves: reframe (same goal, different mechanism), invert (what would make this worse), adjacent (how similar systems solve it), compete (hold several approaches at once). Generate options without judging them, and narrow only afterwards, with stated criteria; never mix the two phases. Add one deliberate constraint, such as "no new dependency", and see what option it forces.
5. **Offer 2 to 4 concrete approaches** (the session mode narrows this range: `references/modes.md`), each backed by evidence or a citation.
   - Include keeping things as they are when that is a real option. **Recommend one** with a one-line reason grounded in the research.
   - Every other approach raised along the way goes under Dropped approaches with the evidence that ruled it out; none silently disappears.
   - Include one option that delivers similar user impact for much less effort.
   - When the options are unfamiliar, run small measured trials of several before recommending one, with a fixed end to the trial phase.
6. **Stop for M's pick.** Once M picks, the next step is a written plan. Never write code from an options table alone.

Open the generated file body with the H2 heading `## Options: <refocused problem statement>`, then:

- **Bottom line:** <recommended approach and why, one or two sentences; confidence: high | medium | low, and why>
- **Where we are:** <framing done; N sources read; next step is the plan>
- **Need from M:** pick an option; while a blocking finding is open or contested, rule on that first.

Follow with these H3 sections:

- **The problem, framed:** 6W2H, one line each.
- **What must be true:** hard constraints tagged verified, conventions, and open checks tagged unverified with the check named.
- **Options:** one comparison table. Give each option a column. Use rows for how it works, codebase fit, what it gives up, what it costs (effort, money, risk), blast radius and whether it can be undone, failure conditions, evidence with its strength grade, and completeness x/10 (10 covers every edge case, 7 the happy path only, 3 a shortcut that defers real work).
- **Dropped approaches:** one line each: the approach, where it came from, and the evidence that ruled it out. Omit when none were dropped.
- **Flow of the recommended option:** a diagram when it materially clarifies how the option works.
- **Recommendation:** `RECOMMENDATION: <option> because <one reason>`.
- **Confidence and gaps:** how sure the recommendation is, what was not verified, and what would change it.
- **Sources:** title, link, version, and publication date, one per line; for a fact the answer rests on, also the sentence that supports it.

Do not print empty sections.

## Independent check

The session that wrote a brief knows things the brief does not say, so it cannot see the gaps a cold reader would hit.

1. **Fresh reader.** Launch one fresh `general-purpose` subagent. Give it only file paths, written as absolute paths: the brief, this skill's `references/rubric-research.md` and `references/ten-principles.md`, and any code files the brief names. Never give it the conversation, M's request, or your reasoning. Its prompt carries the instructions in step 2 word for word, including the three severity definitions. It may open the links the brief cites. If no subagent is available, tell M the check is not independent, run it yourself only with M's OK, and mark it `partial`.
2. **What it returns.** In its own words, the question the brief answers and its conclusion; then a verdict (pass, violation, or n/a, with evidence) for each rubric criterion; then one finding per violation with a severity: `blocking` (the conclusion rests on a claim that is unsupported, contradicted, or stated more confidently than the evidence allows), `must-verify` (plausible and important; one check settles it), or `note` (usable now; a bounded improvement).
3. **Compare.** You, the author, compare the reader's account with M's question. A difference is a blocking finding.
4. **Answer every finding.** First try to refute each blocking finding: open the source it disputes and check the exact claim. If it survives, it stands. Then give each finding exactly one of: `fix` (valid; name the edit), `contest` (wrong, with proof the reader could have read; M decides), or `decline` (notes only). Never contest a comprehension gap: if the reader could not follow it, M cannot either.
5. **Re-check.** If round 1 had any blocking or must-verify finding answered `fix`, and the round limit in `references/modes.md` allows another round, apply the fixes, then launch a new fresh reader with the same inputs and nothing from the first round. The brief's body never names a round or a reader; it cites its sources.
6. **Stop** when a round has no blocking or must-verify finding with a `fix` response, or at the round limit for the session mode in `references/modes.md` (the first check is round 1).
   - Whenever the check stops, apply the note-level `fix` responses.
   - At the limit, handle blocking and must-verify fixes, and the question to M, as `references/modes.md` says for the session mode.
7. **Record.** After the rounds stop and M has answered any question from step 6, write the result under `## Independent check`. A finding is open when it is unfixed, whether its response was `fix` or `contest`. A blocking or must-verify fix applied in the last allowed round is recorded as `Applied, not re-checked`, and the result line adds `, N applied not re-checked`.
   - `present as is`: no blocking finding is open.
   - `present after fixing N blocking`: N blocking findings are open and each has a proposed edit.
   - `do not use for a decision until fixed`: an open blocking finding has no proposed edit, or is contested and waiting for M.
   - Add `, N must-verify open` when that applies.
   - Then list the verdict per criterion, each finding with the response it got (`fix`, `contest`, or `decline`), every finding still open, and every contest waiting for M.
8. **After M rules on a contest.** If M sides with the reader, apply the fix, run one more fresh reader only if `references/modes.md` allows it for the session mode, and record again. If M sides with you, mark the finding settled and record again.

## Presenting research

Chat gets a briefing, not the research file. The file is the source of truth; link it. Hard cap: about one screen, so each labeled paragraph below is one or two lines. Compress, do not omit a recommendation, gap, or open finding. Then the In plain terms block. Always.

Open with the H2 heading `## Briefing: <title>`, then:

- **Bottom line:** <answer or recommended option, 1-2 sentences; confidence: high | medium | low, and why>
- **Where we are:** checked research at `<path>`; mode: find out | explore options; waiting on <decision | pick | nothing>
- **Need from M:** <accept the answer | pick an option | provide missing evidence | nothing>

Next, a `Questions for M` block: every open question, numbered and standalone, with lettered options and a recommendation, so M can answer without opening the file. With none, write `Questions for M: none`.

Follow with short labeled paragraphs for:

- **Problem / question:** <one concrete sentence>
- **Today / found:** <current mechanism, or the 3-5 findings that matter>
- **Decision / recommendation:** <what and why>
- **Options:** explore options only: one line per option with its name, what it gives up, and what it costs, so M can pick without opening the file.
- **Rejected / also looked at:** <short; omit when nothing was rejected>
- **What this means:** <outcome for our code; key files or contracts>
- **Risks / gaps:** <only what could change the call>
- **Check:** <N blocking, N must-verify>; independent or partial; one line if anything blocks.
- **Open fixes:** one line per fix left open at the round limit, with its proposed edit; omit when none.
- **Applied, not re-checked:** one line per blocking or must-verify fix applied in the last allowed round; omit when none.
- **Contests:** one line per finding you contested, with your proof, for M to decide; omit when none.

End with the H2 heading `## In plain terms` and these fields:

- **The problem:** <concrete scenario, no unexplained jargon>
- **What changes / next:** <outcome language, not file paths>
- **Why:** <the structural reason this answer or option holds>
- **What M gets:** <the decision this unblocks>

Do not repeat `Need from M` in the closing block.
