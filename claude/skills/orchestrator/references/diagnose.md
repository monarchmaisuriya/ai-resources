# Diagnose playbook

M is the person you are working for. M makes every decision this playbook sends to them.

The ten principles in `references/ten-principles.md` apply to every diagnosis and fix. Read that file first.

Tag every claim about the system: `[verified: file:line, command and output, or log line]` when you read or ran it this session, `[disproved: same kind of evidence]` when you checked and it is false, `[inferred: from which facts]` when it follows from verified facts, `[unverified: the check that would settle it]` otherwise.

Understand the mechanism before you touch the system. No fix until the mechanism is verified with evidence. Investigation produces a diagnosis; only then does implementation produce a structural change. Reproduce before you fix; trace, don't hop; kill hypotheses with experiments; done means "cannot recur", not "green".

## If users are being hurt right now

Stop the damage before finding the cause.

1. Save the evidence that a rollback would erase (the error, the logs, the failing request) by quoting it in your message to M, when that takes seconds. If it would take longer, tell M what will be lost and let M choose.
2. Tell M the fastest safe way to stop it: roll back the last deploy, turn off the flag, or fail over. Give the exact command. You never change production yourself.
3. Say plainly that this is a stopgap: name what it does not fix and when it must be removed.
4. Then start the phases below. A stopgap is never the final fix.

## Size it

Match the write-up to the cost of the bug (principle 5). The phases are never skipped; only the paperwork shrinks.

- **Small bug:** one place, found before it reached users, fix easy to undo. The diagnosis card may be five lines (symptom, mechanism, root cause, structural fix, recurrence test) plus `Need from M` and one line on the theories considered. The RCA is three lines in chat with no file: root cause, structural fix with `Recurrence test: no`, and prevention.
- **Everything else:** anything that reached users or data, touches several files, or recurred. Full card and a saved RCA.

## Phases (in order; do not skip)

1. **Reproduce.** A reliable trigger (same symptom at least twice). Cannot reproduce = not ready to fix; say so and say what you tried. For a failure seen only in production, reproduce it in a local or staging copy or from the logs, never by writing to production. While it stays unreproduced, continue phases 2 to 7 by reading the code and logs; only phase 8, the fix, is blocked.
2. **Characterize.** Always or intermittent; which environment; which data; since when (diff and blame near the invariant).
3. **Localize.** Which layer: UI, API, job, DB, cache, external service, config, deploy.
4. **Trace, don't hop.** Inputs -> Transforms -> State -> Decision points -> Outputs, with evidence at every step.
   - At each step ask "what did this step assume was true?"
   - Then ask which line makes it true.
   - Read that function across all its paths, not only the success path, and cite it.
   - If no line makes it true, write `nothing found`: that assumption is the lead.
   - Never trust a name: `is_valid` may not check anything.
   - Code that never calls each other is still coupled through shared state: a row two handlers write, a cache key one job writes and another reads.
   - The first false assumption upstream of the symptom is the lead.
   - One hop from stack trace to edit is forbidden.
   - Read stack traces bottom-up for origin, top for symptom; the frame you need is often in the middle.
   - Find the smallest diff between the passing case and the failing case.
5. **Hypothesize.** At least two plausible causes with different predictions. Open the ledger.
6. **Experiment.** One test that would kill the leading theory if it is wrong. One technique, one variable per experiment. Update the ledger after each one.
7. **Diagnose.** Fill the diagnosis card.
   - Ask "why?" of each answer in turn (Five Whys) when the cause chain is a straight line; use the cause tree from the techniques table when causes branch. Go on until you hit a design or invariant you can change.
   - Classify: symptom -> immediate cause (what failed) -> systemic cause (what let it fail) -> root cause (the design or rule that made it possible). The question is "why was this breakable?", not "why did it break?"
   - A person's mistake is never the root cause: when someone did the wrong thing, find what in the system made it easy, and say whether it was a slip (right goal, wrong action) or a mistake (wrong goal).
8. **Fix** at the owning layer (see "Owning layer lookup" and the symptom-fix table).
   - A small fix in one place needs M to confirm the diagnosis card first.
   - If the fix touches several files, a contract, a schema, or stored data, write a plan and get M's explicit sign-off ("yes, exactly", "approved", "go", "implement", "ship it") before changing code. Write the plan by `references/plan.md` (read it in full first), carrying the diagnosis card in as what is true today; after sign-off, implement it by `references/build.md`.
   - Temporary logging added for an experiment is allowed and is removed before the fix.
   - Never commit or push unless M asks.
   - Code that neither M nor an agent working for M wrote (a third-party repo, downloaded files) is run only in a sandbox with all of these controls: no external network, an empty allowlisted environment, read-only target and tools, scratch-only writes, and explicit CPU, memory, process, file-size, disk, and time limits; if any control cannot be shown to hold, read it without running it.
   - When the fix is in, run the Ship check section of `references/build.md` before reporting (its plan lines are n/a when there is no plan).
9. **Verify.** Recurrence test (with the fix in place, can the same cause produce the problem again?) = no. Add the test that proves the invariant. It must fail on the pre-fix code and pass on the fixed code; show the exact command and the result of both runs. When the test needs a level or test-double choice, write it by `references/test.md` (read it in full), cover mode, with the broken invariant as the first row of the test list. Never go green by weakening an assertion, skipping, retrying, or sleeping.
10. **Prevent and report.** RCA (below).

## Hypothesis ledger (keep it current; include it in every message you send M while debugging)

| # | theory | predicts if true | would be disproved by | status: open / confirmed / killed | evidence |
| --- | --- | --- | --- | --- | --- |

At least two open until one is confirmed by a discriminating experiment (one whose result the competing theories predict differently). When a theory is killed and fewer than two remain open, add a new one. Killed = an experiment returned what the theory cannot explain. Anchoring on #1 without trying to kill it is shallow debugging.

## Techniques (one per experiment)

| technique | when |
| --- | --- |
| binary search | large surface: disable or halve until the symptom disappears |
| differential | "works in A, fails in B": diff one variable at a time |
| timeline | intermittent or race: order the events, find the reorder or the gap. Confirm each source's timezone first, then convert everything to UTC |
| invariant check | logic bug: state what must hold, find the first violation |
| minimal repro | noisy failure: strip until the smallest input still fails |
| static trace | no runtime access: read the call graph, grep the producers |
| dynamic probe | runtime access: log at boundaries, assert invariants |
| edge-value probe | a config value or parameter is involved: try `0`, `""`, `None`, `[]`, `-1`, and a stale cached value; find the one that silently disables the path. The Config section of `references/misuse-patterns.md` lists common traps |
| black-box boundary | the trace reaches code you cannot read (external API, unread library, cache): list what was sent and which outcomes are not ruled out (error, empty, stale, duplicate, timeout), then test the likeliest |
| known-positive control | a search, query, or filter returns nothing: first run it on a case known to produce a hit. Only then does an empty result mean anything |
| behavior over time | the symptom changed over time: put the symptom and two or three suspect variables on one timeline and note which moved first |
| cause tree | causes branch instead of forming one chain: draw a tree with the symptom at the root and one branch per category (code, config, data, dependency, environment, process), and mark each branch ruled in or out with its evidence. Use Five Whys only when the chain is a straight line |
| hot spot | a failure that recurs: measure where most of it occurs and fix that spot before proposing any process change |
| stock and flow | something keeps piling up (a queue, a backlog, a table): compare the rate going in with the rate going out |

Read evidence literally: error type, constraint name, null field, timeout in ms. Logs: follow one request id across services; find the last good state. Diff and blame: what changed near the invariant.

Guard against false patterns. State the suspected relationship before searching logs; a pattern found by open-ended searching is unconfirmed. Before reading a frequency as a cause, check the base rate (how often it happens anyway); a cluster alone is not a cause. Say where the confirmed explanation is known to break down, not only where it holds.

## Diagnosis card (required before proposing any fix; this is what M reads)

Open with the H2 heading `## Diagnosis: <short name>`, then:

- **Bottom line:** <the mechanism, broken invariant, and root cause in one or two sentences; confidence: high | medium | low, and why>
- **Where we are:** Diagnose, step 7 of 10; reproduced or not reproduced; fix not started.
- **Need from M:** <confirm the structural fix | choose between real fixes | provide missing evidence>; `nothing` only when the problem is reproduced and no fix is being proposed
- **Symptom:** <what M saw, quoted> [verified: <evidence>]
- **Reproduced:** <exact steps and repeated result> or <not yet, because reason>. When not reproduced, the Structural fix field says `not proposed until reproduced` and Need from M asks for what would make it reproducible.

Follow with these labeled bullets:

- **Flow:** an optional picture of the Mechanism bullets: when the mechanism spans several meaningful steps, an alignment-sensitive ASCII diagram from trigger to failure, with the failing node marked `<-- fails here`. For a simple one-boundary failure, omit this field. A cause tree, when you drew one, goes here too.
- **Mechanism:** one tagged step per bullet.
- **Broken invariant:** what should have remained true and where it first became false.
- **Root cause:** why the system was breakable: the design, contract, data, or model flaw.
- **Blast radius:** who and what else the defect touches: other users, callers, data already written wrong.
- **Owning layer:** who is allowed to decide this truth, with `file:line`.
- **Symptom-fix temptation:** what would quiet the visible failure without changing its generator, and why it is rejected.
- **Structural fix:** what rule, model, or contract changes so this failure class cannot recur (the generator: whatever produces the failure, as opposed to where it shows); its effort, and whether it can be undone.
- **Side effects:** the fix's risk: what it could make worse now or later, and how that would be noticed.
- **Recurrence test:** the question from phase 9, answered; the required answer is no.
- **Hypotheses considered:** the ledger summary and why the confirmed theory beat the alternatives.

End the card with `## In plain terms`: what broke, why it could break, and what the fix changes. Do not repeat `Need from M` at the end. Use a code fence only around the ASCII flow or a literal log excerpt.

## Owning layer lookup

| if the bug is... | the owning layer is usually... |
| --- | --- |
| wrong shape or type of data | schema, serializer, DTO (data transfer object), DB constraint |
| wrong business rule | domain model, policy or service, state machine |
| wrong authorization | auth boundary, permission-check owner |
| wrong timing or order | lifecycle owner, queue or transaction boundary |
| wrong config | config schema and loader, not every consumer |
| inconsistent across endpoints | the shared contract, not each handler |

## Symptom fix versus structural fix

| forbidden symptom patch | required structural fix |
| --- | --- |
| try/except swallowing errors at the call site | fix why it throws; fix the contract or upstream validation |
| extra if, flag, or special case for one id or path | fix the rule that misclassifies the case |
| wrapper catching and returning a default | fix the producer; fail loudly at the boundary that owns truth |
| retry loop masking a flaky upstream | fix idempotency, timeout, or the upstream bug |
| cache hiding stale or wrong data | fix invalidation, source of truth, or the write path |
| duplicate validation "to be safe" | one owner for the invariant |
| guard clause skipping a broken path | remove or redesign the path |
| sleep or delay to win a race | fix ordering, lifecycle, or the async contract |
| null-coalescing downstream of bad data | fix the producer; validate at the owning boundary |
| feature flag hiding a broken path | fix the path; flags are for rollout, not for broken core |

## Anti-patterns (automatic stop)

When you catch yourself in one of these, stop, tell M which one, and go back to the phase it skipped.

Fix-first (changes mask evidence); grep-and-patch (first match is not root cause); retry until green (hides the flake mechanism); shotgun changes (several changes at once, hoping one works); narrating what the code says instead of what the system does; reading only the throw site; trusting a function's name instead of reading it; correlation as cause; "fixed" because the symptom vanished once.

## Stuck (about 15 minutes with no new experiment, or 3 or more experiments without a confirmed theory)

- First stop pushing on the same line and restate the problem from zero: what must be true, what was observed, and what is only assumed.
- Then send M: repro status, the ledger, the last two experiments and their results, and what would break the tie.
- Do not silently switch to implementation.
- For long investigations (more than about an hour or 6 experiments) create `docs/investigations/<topic>.md` with FACTS separated from THEORIES (2 or more competing, 5 or more when the space is wide); a hypothesis before each action, a result after; kill or confirm, never "maybe".

## RCA (after the fix is in place and its proving test passes; blameless postmortem shape)

RCA means root cause analysis. Save it as `docs/YYYY-MM-DD-HHMM_<short-topic-slug>_rca.md` in the repo that was fixed, with the date and time read from the system clock (`date`), and give M the same content in chat. When you deliver it, ask M whether to keep or delete the file.

Open with the H2 heading `## RCA: <title>`, then:

- **Bottom line:** <what broke, why it was possible, and what changed so it cannot recur>
- **Where we are:** fix in place and proven by its test, whether M has released it yet, or the exact remaining gap.
- **Need from M:** <accept a known gap | make a follow-up decision | nothing>

Follow with these labeled bullets:

- **Impact:** who or what was affected, for how long, and how many when known. If personal data crossed a tenant boundary or left the system, say so and escalate to M: legal notification rules may apply, and that decision is M's.
- **Timeline:** first bad -> detected -> stopped -> diagnosed -> fixed, with times when known. Say whether a deploy or a config change started it.
- **Symptom -> mechanism -> root cause:** the diagnosis card condensed, with an ASCII flow when it materially clarifies the causal chain.
- **Trigger:** the event that exposed the defect, as distinct from the root cause above.
- **Structural fix:** what generator changed, with `file:line`; state `Recurrence test: no`.
- **Prevention:** test added, invariant asserted, alert, or monitor.
- **Detection gap:** why it was not caught earlier and what would catch it next time.
- **Follow-up actions:** one line each, with an owner and a priority. No action is left without an owner; ask M who owns it when unclear.

End with the H2 heading `## In plain terms`:

- **The problem:** <what M or the user saw>
- **The fix:** <what changed and what they get instead>
- **Closest software parallel:** <one sentence using a software mechanism such as a cache, index, queue, lock, or migration; omit when no useful parallel exists>

Do not repeat `Need from M` in the closing block.
