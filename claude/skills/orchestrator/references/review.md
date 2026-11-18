# Standardized Review

Review each artifact with the rubric designed for it. Plans, research, ADRs, and code require different evidence. They share a workflow and report shape, not a forced checklist.

M is the person you are working for. M makes every decision this playbook sends to them.

Find problems that could change implementation, operation, or a decision. Do not report formatting, prose preference, or naming-taste nits. Every recommendation fixes the owning layer (the part of the system allowed to decide that fact) or the source of truth. Never patch a downstream symptom.

## When it runs

| Artifact | When this review runs |
| --- | --- |
| Plan | When M asks for a review. A plan may already carry the result of an earlier check; review it anyway when asked. |
| Research | When M asks for a review. |
| ADR | Mandatory before presenting a standalone ADR draft; a decision record written inside a plan is covered by that plan's independent check. Also on-demand when M asks to review an existing ADR. |
| Code | On demand, when M asks, for example after code ships. |
| Security audit | On-demand only, for a codebase rather than a diff. See Security audit mode. |

On-demand always works for any of them when M asks.

Write-time: authors write so the artifact would pass its rubric. Do not wait for review to care about it.

## Security audit mode

For a security audit, pen test, or vulnerability hunt of a codebase, follow `references/security-audit/WORKFLOW.md`, a port of Cloudflare's security-audit skill. Read its "Adaptations for this skill" section first; it maps agents, gates the full run on M's yes, and maps verdicts into the review brief.

- **Guidance mode** (a security question, one suspected bug, triage of a finding, a security review of a diff): use only the parts that fit. For a diff, run the code rubric in `references/rubric-code.md`; its Security pass already carries the upstream candidate gate and severity anchors, and step 7 of the shared workflow carries the refutation.
- **Full audit mode** (explicit audit or pen test of a codebase, a comprehensive review, or report artifacts requested): run all six upstream phases in order, write the upstream artifacts, and run both validators.
- If the ask could be either, ask one question before creating files.

Outside a security audit, to understand unfamiliar code before reviewing it, build the map in `references/full-audit.md`. A security audit does not use that file.

## Shared workflow

1. **Detect the artifact.**
   - `*_plan.md`, task breakdown, or implementation sequence -> plan.
   - `*_research.md`, research question, investigation, comparison, options brief, or recommendation based on sources -> research.
   - `*_rca.md` or another incident write-up -> research, read with the incident row of its rubric.
   - `*_brief.md` from an interrogation -> no rubric fits: judge it against the ten principles only, and say so under Scope and limits.
   - Architecture Decision Record, decision proposal, or durable architecture choice -> ADR.
   - Diff, PR, commit, file, module, or implementation -> code.
   - Security audit, pen test, or vulnerability hunt of a codebase -> Security audit mode.
   - If an artifact mixes types, review each part with its matching rubric. If the type is still unclear, ask once.
2. **State the intended outcome.** Write one sentence describing what this artifact must enable. Derive it from M's request and the artifact. Do not invent intent.
3. **Set the scope.** Name what was reviewed and what was not. Read the complete artifact. Then inspect the evidence it relies on:
   - plan: current code, contracts, callers, docs, and stated requirements;
   - research: cited sources, source dates, search method, and underlying data where available;
   - ADR: context, constraints, related ADRs, current architecture, and affected contracts;
   - code: diff plus affected callers, tests, schemas, configuration, and public contracts.
4. **Separate fact from inference.** Mark direct observations as `[verified]`. Mark conclusions as `[inferred]`. Mark a claim you checked and found false as `[disproved]`. Mark anything that needs evidence as `[unverified]`. Never turn an unverified premise into a confident finding.
5. **Apply the matching rubric.** Give every applicable criterion a pass, violation, or n/a verdict. Cite evidence for passes and violations. Explain every n/a.
6. **Trace second-order effects.** Follow what reads, writes, calls, deploys, operates, supersedes, or makes a decision from the reviewed artifact.
7. **Try to refute every blocking and must-verify candidate.** The agent that checks a finding is never the agent that found it.
   - **Name the preventing control.** Reconstruct the strongest control on the path that should stop it (validation, auth middleware or decorator, database constraint, framework escaping, type system, deploy config in the repo). Read it and every path through it.
   - **Independent refuter.** Launch one fresh `general-purpose` subagent per candidate, in parallel in one message. Give it only the candidate's claim, its cited `file:line`s, and the artifact or diff; never your reasoning or another refuter's outcome. Prompt it: "You did not write this finding. Try to refute it from the source. Re-read every cited location, reconstruct the strongest control on the path, and check that the stated impact is the one the evidence shows. Return exactly one of: `stands` with the evidence; `downgraded` with the exact fact outside the source that decides it and who can observe it; `ruled out` with the preventing `file:line`."
   - **Apply the outcome.** `stands` -> keep the severity. `downgraded` -> must-verify, naming the missing fact and a safe owner check. `ruled out` -> drop the finding and add one line to Ruled out (or a note when only a second defensive layer is missing). If you disagree with the refuter, report both outcomes with their evidence; never silently pick one.
   - **Material changes get a fresh check.** If the outcome strengthens a finding or changes its root cause, path, or impact, give the corrected finding to another fresh refuter before reporting it.
   - **Consolidate by root cause.** One root cause reached by several paths is one finding listing every path. Unrelated causes stay separate.
   - A finding that could not get an independent refuter (subagents unavailable) is reported with `[unverified: no independent refutation]`, and the review is marked partial.
8. **Create flag cards for violations.** Each card contains:
   - severity;
   - criterion;
   - what is wrong and where;
   - evidence;
   - consequence;
   - structural fix at the owning layer;
   - alternatives and recommendation when a decision is required;
   - what is needed from M.
9. **Report without modifying.** A review diagnoses. It does not silently edit the artifact. Fixes require M's explicit instruction or the workflow that requested the review. Exception: the author applies its own `fix` responses during the rounds described in "Who reviews, and what happens to findings". A fix to code needs a test that failed before the fix and passes after it, with the exact command and its result shown, before it is reported as fixed.

## Who reviews, and what happens to findings

The session that wrote an artifact knows things the artifact does not say, so it cannot see the gaps a cold reader would hit.

- **Who reviews:** an artifact an agent wrote (plan, research brief, ADR, or code) is reviewed by one fresh `general-purpose` subagent.
  - It gets only file paths: the artifact, this playbook (`references/review.md`) with the rubric and principle files it names, and the files the artifact links to; for a plan or code, the repo path; for a plan, also `references/design-checklist.md` and `references/misuse-patterns.md`; for code, also the exact diff command and the plan or description that says what the change is for (if none exists, the author writes one to a file first). Tell it the absolute path of the folder that holds `references/`, as the base for every `references/` path it meets, and tell it to skip any `## Independent check` or `## Review` section already in the artifact.
  - It never gets the conversation, M's request, or the author's reasoning.
  - The reviewer runs steps 3 to 6 and 8 and judges the artifact against the goal the artifact itself states, and returns its verdict and evidence for each criterion, its draft finding cards, what it reviewed and did not, and one sentence saying what the artifact is for as it reads it.
  - Its step 8 cards are drafts: the author then runs step 7 on every blocking and must-verify draft and applies its outcome (`stands`, `downgraded`, or `ruled out`) to the card.
  - The author session (the session running this review, whoever wrote the artifact) does steps 1, 2, and 7, the author response, and the brief; it compares the reviewer's reading of the goal with M's request, and a mismatch is a blocking finding.
  - For an artifact no agent wrote (M's own document, someone else's PR), the review may run in this session, and there is no author response.
  - When you cannot tell who wrote it, use a fresh reviewer as for agent-written work, but apply `fix` edits only when M asks.
  - An accepted decision record is never edited: report the findings, and a fix means a new record that supersedes it.
- **No subagent available:** tell M the review is not independent, run it here only with M's OK, and mark it `partial: author-session review`.
- **Author response:** after refutation, the author gives every finding exactly one response.
  - `fix`: the finding is valid; name the edit at the owning layer.
  - `contest`: the finding is wrong, with proof the reviewer could have read (`file:line`, a section, a linked file).
  - `decline`: allowed for notes only, never for a blocking or must-verify finding; recorded, not fixed.
  - M decides every contest; a contest waits in the brief under Decisions needed and does not keep the rounds going.
  - If a later reviewer raises a contested finding again, add that round to the same item instead of opening a new one.
  - Never contest a comprehension gap: if the reviewer could not follow it, a cold M cannot either.
- **Re-check:** when the round limit in `references/modes.md` allows another round, the fixes are applied and a new fresh reviewer reviews the updated artifact.
  - It gets nothing from the earlier round.
  - Text in the artifact outside its review section cites primary sources only (`file:line`, command and output, doc link) and never names a round or a reviewer.
  - After the rounds stop, and not before, the brief goes at the end of a plan, research brief, or decision record under an H2 heading `## Review`, so no reviewer ever reads an earlier brief; for code, and for a decision record that is already accepted, the brief goes to M in chat.
  - Apart from the author's own `fix` edits, this is the only edit a review makes.
- **Stop:** when a round has no blocking or must-verify finding with a `fix` response, or at the round limit for the session mode in `references/modes.md` (the first review is round 1).
  - Notes answered `fix` are applied in every round, including the last.
  - At the limit, handle blocking and must-verify fixes, and the question to M, as `references/modes.md` says for the session mode.

## The ten principles

Every artifact is written and reviewed against the ten principles in `references/ten-principles.md`. Read that file before applying a rubric. Each rubric is the detailed form of the ten for one artifact type.

## Review discipline

Use these rules for every artifact:

- **Review the claim, not the confidence of its author.** Verify important statements against code, data, primary sources, or durable decisions.
- **Start broad, then go deep.** First understand the whole artifact and its system boundary. Then inspect the highest-risk paths in detail.
- **Use risk to allocate attention.** Spend the most effort on irreversible decisions, auth, data integrity, public contracts, migrations, concurrency, external dependencies, and operational recovery.
- **Maintain competing explanations.** When diagnosing a defect or evaluating research, test plausible alternatives instead of gathering support for the first theory.
- **Distinguish absence from proof.** “I found no reference” does not mean “unused.” “No test failed” does not mean “correct.” State what was actually checked.
- **Do not demand perfection.** The test is whether the artifact leaves the system better than before, not whether it is perfect. Block on material defects. Use notes for bounded improvements that do not prevent the artifact from serving its purpose.
- **Do not invent organizational policy.** If approval, compliance, ownership, or service-level requirements are unknown, mark them unverified and ask.
- **Respect artifact boundaries.** A plan describes executable work. Research establishes evidence. An ADR records a decision. Code implements behavior. Flag one artifact trying to silently replace another.
- **Check the whole, not only the part.** A local improvement must not make the whole flow worse.
- **Question rewrites.** When an artifact replaces something that works, check that it lists what the old one handled and depended on, and says why improving it was not enough.

### Rationalizations to reject

| Rationalization | Why it is wrong | Required action |
| --- | --- | --- |
| "Small diff, quick look." | A two-line change can remove the only auth check. | Triage by risk tier, not by size. |
| "Just a refactor, no behavior change." | Refactors silently break invariants and call order. | Treat as high risk until behavior preservation is shown. |
| "I know this code." | Familiarity hides what changed underneath you. | Read the baseline version before judging the diff. |
| "History takes too long." | History is the only way to see why removed code existed. | Run the history pass for every removed or weakened check. |
| "Blast radius is obvious." | Transitive and string-dispatched callers are missed. | List the callers and how they were found. |
| "No tests is not my problem." | Untested change is unverified change. | Apply the test-gap escalation rules. |
| "It's documented." | Under deadline, people copy the first example, not the docs. | Make the safe value the default or the only option; otherwise flag it. |
| "It's just a config option." | Config ships to production like code. | Validate the value when it loads; reject values that switch safety off. |
| "Nobody would do that." | Under pressure, someone will. | Assume the most confused caller; make misuse fail loudly. |

Over-reporting is the opposite failure: it buries real findings and trains M to skim. Reject these too:

| Rationalization | Why it is wrong | Required action |
| --- | --- | --- |
| "It deviates from the checklist, so it is a vulnerability." | A missing best practice with no affected principal or resource is not a finding. | Name who crosses which boundary and what they get, or make it a note. |
| "The second layer is missing." | If a visible first layer prevents the attack, nothing is reachable. | Name the preventing control; report the gap as a hardening note. |
| "The user can break their own data." | Self-impact and actions the caller is already allowed are not boundary failures. | Require a different, lower-trust principal and an affected resource they should not reach. |
| "It crashes, so it could be code execution." | A finding cannot claim more than the evidence shows. | Report the effect observed; severity never exceeds demonstrated impact. |
| "Production probably has a proxy, WAF, or IAM rule for that." | Facts outside the repo are proof neither way. | If decisive, must-verify with the exact missing fact and who can check it. |
| "It was rejected last time, skip it." | A rejection covers only that exact claim on unchanged source. | Re-check when the cited source changed; never skip the surrounding code. |

## Evidence procedure

For every material verdict:

1. Write the proposition being checked.
2. Name the best available evidence and why it is authoritative.
3. Inspect the evidence directly where access allows.
4. Record what was observed.
5. Compare the observation with the proposition.
6. State the verdict and confidence.
7. Name the smallest additional check that could change the verdict.

Use this evidence order unless the artifact requires a different authority:

1. Executed test, reproduced behavior, measured data, or authoritative runtime state.
2. Current source code, schema, configuration, public contract, or accepted ADR.
3. Official specification, standard, vendor documentation, or primary research.
4. Maintainer documentation and repository history.
5. Independent secondary analysis.
6. Author assertion or reviewer inference.

A lower-ranked source may be more relevant than a higher-ranked stale source. Explain that choice. Never hide inaccessible evidence; list it under scope limitations.

## Severity

| Severity | Meaning |
| --- | --- |
| blocking | The artifact can cause incorrect behavior, data loss, an auth or contract failure, an unsafe migration, or a decision based on materially false or unsupported claims. |
| must-verify | The concern is plausible and important, but one focused check can confirm or reject it. It also covers a known gap in verification, such as new behavior with no test. |
| note | The artifact is usable now; this is a bounded improvement or explicitly deferred risk. |

Severity describes impact and confidence. Do not inflate severity to make feedback sound important.

## Cross-artifact consistency

When related artifacts exist, verify this chain:

```
confirmed need
    -> research supports the available choices
    -> ADR records the chosen direction
    -> plan sequences that direction
    -> code implements the plan
    -> tests and operations verify the need
```

Check both directions:

- **Forward traceability:** Every material requirement or decision reaches an implementation task and an observable check.
- **Backward traceability:** Every material task and code path traces back to a confirmed requirement, supported recommendation, or accepted decision.

Flag these structural breaks:

- research recommends an option outside the confirmed problem;
- an ADR claims evidence that the research does not provide;
- a plan contradicts or silently extends an accepted ADR;
- code implements behavior absent from the plan or decision;
- tests verify implementation details but not the original outcome;
- a newer artifact supersedes an older one without updating links or status.

Do not require every task to have all four artifact types. Apply cross-artifact checks only to artifacts that exist or are required by the risk.

## Rubrics

Read the one rubric that matches the artifact, in full, before step 5. Each file holds the criteria, how to review that artifact, and its blocking conditions.

| Artifact | Rubric file |
| --- | --- |
| Plan | `references/rubric-plan.md` |
| Research brief | `references/rubric-research.md` |
| ADR | `references/rubric-adr.md` |
| Code | `references/rubric-code.md` |

The code rubric also uses `references/misuse-patterns.md`, `references/full-audit.md`, and the files in `references/security-audit/`.

## Risk prompts

Apply these prompts to any artifact when relevant:

| Area | Questions |
| --- | --- |
| Auth and security | Who can perform this action? Where is trust established? Can input cross the boundary unvalidated? What sensitive data is stored or logged? |
| Data | What is the source of truth? Which invariants must hold? Can writes be duplicated, reordered, partially applied, or lost? |
| Concurrency | What can happen simultaneously? Which state is shared? Is ordering assumed? Are operations atomic or idempotent? |
| External dependencies | What are the timeout, retry, quota, version, failure, and ownership contracts? What happens during degradation? |
| Migration | Can old and new versions coexist? How is data backfilled and reconciled? Where is the point of no return? |
| Operations | How is failure detected? What does an operator do? Are alerts actionable? What resource can grow without a bound? |
| Rollout | What are entry, success, abort, and rollback conditions? Who owns each decision? |
| Recovery | What is the last known safe state? Which side effects survive rollback? How is correctness verified afterward? |
| Compliance and privacy | Which policies apply? Is collection necessary? Are access, retention, deletion, and audit requirements satisfied? |
| Cost and capacity | What scales with traffic or data? Which limit fails first? Is the cost model based on measured or assumed load? |

## Output: review brief

Use normal rendered Markdown. Never wrap the review, a finding, or criterion coverage in a code fence. Use a code fence only for an actual code excerpt, a command, literal output, an alignment-sensitive ASCII diagram, or a Mermaid block where the surface renders it. Do not put sentence-length evidence in a table.

Add one short diagram (its form comes from the visual ladder in core section 2.4) when it materially clarifies a flow, hierarchy, sequence, or causal chain behind a verdict. Put it after the opening summary or inside the relevant finding before detailed evidence. Mark the changed or failing node. Skip the diagram when headings and bullets are clearer. Keep side-by-side comparisons as tables.

Keep the brief short by removing repetition, not evidence:

- Write each fact once. Cross-reference a finding instead of restating its evidence, consequence, or fix.
- Use direct sentences. Omit process narration and introductions.
- Keep a field to one sentence unless separate evidence observations require bullets.
- Preserve every material fact, limitation, verdict, and decision. There is no word limit when distinct evidence requires space.

Choose the typed bottom line: code uses `ship as is`, `ship after fixing N blocking`, or `do not ship`; plan uses `present as is`, `present after fixing N blocking`, or `do not implement until fixed`; research uses `present as is`, `present after fixing N blocking`, or `do not use for a decision until fixed`; ADR uses `accept as is`, `accept after fixing N blocking`, or `do not accept` (for a record already accepted: `keep as is`, `supersede to fix N blocking`, or `supersede`); a security audit uses `no blocking findings`, `fix N blocking before release`, or `do not release`. When no blocking finding is open but a must-verify finding is, use the first choice and add `, N must-verify open`. When a blocking or must-verify fix was applied in the last allowed round, add `, N applied not re-checked`.

## Review: <artifact type>: <what was reviewed>

### At a glance

- **Bottom line:** <typed choice; confidence: high | medium | low, and why>
- **Where we are:** reviewing <artifact and scope>; <complete | partial>; waiting on <decision | evidence | nothing>
- **Need from M:** <number of decisions, missing evidence, approval, or "nothing">
- **Why:** <one sentence naming the findings that control the result>
- **Intended outcome:** <one sentence>
- **Findings:** <N blocking, N must-verify, N notes>

### Decisions needed

Include this section only when M must act. Use a numbered list. Each item names the finding, the decision, and the recommendation in one or two short lines. The finding holds the evidence and tradeoffs.

### Findings

List one finding per violation. Order findings by `blocking`, then `must-verify`, then `note`. Use the finding format below. A blocking or must-verify finding whose fix was applied in the last allowed round is marked `Applied, not re-checked`.

### Criterion coverage

Use exactly one bullet per criterion:

- **<Criterion>: <pass / violation / n/a>.** <concise cited evidence, a finding reference for violations, or the reason for n/a>. <`Fix: None`, `Fix: Finding N`, or the owning-layer fix>.

Verdicts are only `pass`, `violation`, or `n/a`; never write `pass with note`. Severity and verdict have different jobs: severity orders the finding, while verdict says whether the criterion is satisfied. Any unresolved blocking, must-verify, or note finding makes its criterion `violation`. A criterion can return to `pass` only after the finding is resolved. A declined note counts as resolved: its criterion returns to `pass` and the note stays listed under Findings.

### What is working

Give at most three verified bullets with `file:line`, a section citation, or a source link. Do not repeat criterion coverage.

### Scope and limits

- **Reviewed:** <artifact and supporting evidence>
- **Not reviewed:** <out-of-scope or inaccessible evidence, or `Nothing`>
- **Checked and found nothing:** <checks that ran and found no problem, or `Nothing`>; keep these apart from checks not run
- **Checks not run:** <checks, or `Nothing`>
- **Ruled out:** <one line per candidate ruled out during refutation, with the preventing `file:line`; or `Nothing`>
- **Depth and coverage:** code only: <deep | focused | surgical>; high tier <N of M files>, medium <N of M>, low <N of M or skipped>
- **Reviewer:** <fresh reviewer, round N | this session (partial: author-session review) | this session (not agent-written)>
- **Working notes:** code only: for each high- or medium-tier change, its callers, the before and after behavior, and each assumption with its establishing line or `nothing found`. Code only, when they apply: the test gap table, and for a misuse finding the smallest code that triggers it. Research only: the strength grade of each claim the recommendation rests on. Any artifact: a refuter outcome you disagree with, showing both sides; for a code fix, the command that failed before it and passes after; the reference files you read; and, for a verdict you are unsure of, the smallest check that would settle it.
- **Confidence:** <high (evidence was read or run for every finding) | medium (some findings rest on inference) | low (key evidence could not be reached)>; explain only material differences between findings

### In plain terms

In at most three short sentences, state what must change, what can wait, and what M must decide. Do not repeat evidence or introduce new facts.

For file evidence, cite `file:line`. For Notion or chat artifacts, cite a section heading and a short quote. For external claims, include the source link. Do not claim exhaustive review when access, time, or scope prevented it.

## Finding format

Use one numbered section per violation. Do not combine unrelated causes. Put the consequence, recommendation, and requested action before supporting detail so M can act without reading the evidence first.

### <N>. <BLOCKING / MUST-VERIFY / NOTE>: <behavioral title>

- **Why it matters:** <specific failure or bad decision>
- **Recommendation:** <preferred option and why>
- **Need from M:** <decision, missing evidence, approval, or `Nothing`>
- **Author response:** <fix | contest | decline> (agent-written artifacts only). For `fix`, the edit is the one under Fix below; for `contest`, give the proof; for `decline`, the reason.

#### What is wrong

- **Criterion:** <criterion>
- **Where:** <location>
- **Problem:** <structural defect>
- **Fix:** <owning layer>: <structural change at that layer>

#### Evidence

- <one `[verified]`, `[inferred]`, `[disproved]`, or `[unverified]` observation per bullet>
- <for a security finding: attacker, access, entry point, path hops, exploitability, and impact (see the Security pass in `references/rubric-code.md`)>

#### Alternatives

Include this section only when real alternatives exist. With two or more options, use a table: one column per option, with rows for what it gives up and what it costs.

Titles describe behavior. Use “Retry can duplicate charges,” not “Idempotency issue.” When there is no decision, write `Need from M: Nothing`; omit the Decisions needed and Alternatives sections instead of printing empty placeholders.

## Completion gates

A review is complete only when all applicable statements are true:

- [ ] Artifact type and intended outcome are explicit.
- [ ] Full artifact and required supporting evidence were inspected.
- [ ] Scope limitations and inaccessible evidence are disclosed.
- [ ] Facts, inferences, and unverified claims are separated.
- [ ] Every applicable rubric criterion has a verdict and evidence.
- [ ] Every n/a verdict explains why it does not apply.
- [ ] Highest-risk boundaries and failure paths were traced.
- [ ] Code: every removed or weakened check had its history read.
- [ ] Code: callers are listed for every high-tier change.
- [ ] Code: every security finding shows a reachable path from an entry point.
- [ ] Code: every assumption the change relies on names its establishing line or says `nothing found`.
- [ ] Code: every changed config value, parameter, or auth path had the edge-value probe, and the matching `references/misuse-patterns.md` sections were read.
- [ ] Every blocking and must-verify finding went to an independent refuter, and its outcome is applied; ruled-out candidates are listed under Ruled out.
- [ ] Code: every blocking or note security finding passed the candidate gate and carries a security severity no higher than its demonstrated impact.
- [ ] Code: every blocking security finding had a variant search, and its variants are folded into it.
- [ ] Security audit (full mode): both upstream validators passed, Phase 5 ran on every retained record, and the report states the coverage limits.
- [ ] Unfamiliar-code map (`references/full-audit.md`): `MAP.md` lists every `nothing found` and every open question from the function files.
- [ ] Related artifacts were checked for consistency when they exist.
- [ ] Every violation has one severity and one structural owning-layer fix.
- [ ] Blocking findings are listed before must-verify findings and notes.
- [ ] Positive evidence is included to calibrate the review.
- [ ] Bottom line matches the actual findings.
- [ ] No test or exhaustive-review claim exceeds the evidence.
- [ ] The review states exactly what M must decide or provide.
- [ ] An agent-written artifact was reviewed by a fresh reviewer, or marked `partial: author-session review` with M's OK.
- [ ] Agent-written artifacts only: every finding has one author response, and the stop rule was met.

If a gate cannot be satisfied, do not pretend the review is complete. State whether the result is a partial review, what is missing, and how that limits confidence.

## Compact examples

### 1. BLOCKING: Mixed-version deployment can reject new events

- **Why it matters:** Events can fail during rollout and require manual replay.
- **Recommendation:** Use consumer-first rollout because it preserves the existing contract.
- **Need from M:** Approve consumer-first rollout.

#### What is wrong

- **Criterion:** Failure and recovery
- **Where:** Part 2, “Deploy producer before consumer”
- **Problem:** The deployment order violates the existing event contract.
- **Fix:** Rollout plan: deploy a tolerant consumer first and add a mixed-version contract test.

#### Evidence

- [verified] The current consumer rejects unknown event fields.
- [verified] The plan deploys the producer first.

#### Alternatives

- Version the event topic; this adds operational cost.

### 2. MUST-VERIFY: Cost recommendation depends on unchecked defaults

- **Why it matters:** The selected option may not be the lowest-cost option at actual usage.
- **Recommendation:** Use measured values; otherwise label the result provisional.
- **Need from M:** Nothing.

#### What is wrong

- **Criterion:** Source quality
- **Where:** “Cost comparison,” vendor calculator link
- **Problem:** The recommendation depends on defaults that were not compared with production.
- **Fix:** Cost model inputs: replace defaults with measured production values and rerun the comparison.

#### Evidence

- [verified] Request volume is entered.
- [unverified] Storage retention and egress match production.


## Reference basis

Use project-specific rules first. These external references explain the review methods behind this playbook:

- [Google Engineering Practices: What to look for in a code review](https://google.github.io/eng-practices/review/reviewer/looking-for.html)
- [NIST Secure Software Development Framework 1.1](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-218.pdf)
- [ACM SIGSOFT Empirical Standards for Software Engineering](https://www2.sigsoft.org/EmpiricalStandards/)
- [Michael Nygard: Documenting Architecture Decisions](https://www.cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [Martin Fowler: Architecture Decision Record](https://martinfowler.com/bliki/ArchitectureDecisionRecord.html)
- [Trail of Bits: Differential Review](https://github.com/trailofbits/skills/tree/main/plugins/differential-review)
- [Trail of Bits: Audit Context Building](https://github.com/trailofbits/skills/tree/main/plugins/audit-context-building)
- [Trail of Bits: Sharp Edges](https://github.com/trailofbits/skills/tree/main/plugins/sharp-edges)
- [Cloudflare: security-audit-skill](https://github.com/cloudflare/security-audit-skill) (ported in `references/security-audit/`, MIT) and [Build your own vulnerability harness](https://blog.cloudflare.com/build-your-own-vulnerability-harness)
