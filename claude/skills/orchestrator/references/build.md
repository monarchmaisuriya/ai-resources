# Build playbook

M is the person you are working for. M makes every decision this playbook sends to them.

Every change is written against the ten principles in `references/ten-principles.md`. Read that file before writing code. Do not wait for a review to care about them.

## Before writing code

This playbook expects a plan file with a Status line, parts made of checkbox tasks with done conditions, acceptance criteria, a rollback step, a Changes made section, and for a user-facing change a success metric with the date it will be checked. If the plan lacks one of these, ask M where to record it.

1. **Confirm sign-off.** If there is no approved plan, stop: this playbook does not start without one. Implement only after M's explicit sign-off on the plan ("yes, exactly", "approved", "go", "implement", "ship it"). "Looks good" or silence is not sign-off. If unsure, it was not: ask.
2. **Offer a worktree.** In a git repo, before the first file write, offer a separate checkout on its own branch so M's checkout stays untouched.
   - Name the base branch, the new branch, and any uncommitted or ignored files (`.env`, `node_modules`) that will be missing there.
   - Ask: this time, always for this repo, or no. Save an "always" answer where future sessions will find it (your memory, if you have one); otherwise remember it for the rest of the session.
   - The plan file stays at its original path.
   - In a folder that is not a git repo, skip the offer. First copy the whole project folder to a backup next to it, named `<project-folder>-backup-<YYYY-MM-DD>`.
3. **Write-scope rescan.** Name the exact files and folders you expect to change. Re-read them and their close callers and contracts. Send M a short table of anything that differs from what the plan says is true today: file, what the plan says, what you found. If the rescan contradicts the plan or raises doubt, stop and ask; do not invent a path forward.
4. **Match existing patterns** before inventing new ones. Look in this order: the project's agent or contributor instructions, the README, then the surrounding code.

## While writing

- **One part at a time**, in the plan's order (unless M agreed that independent parts run at once, each in its own subagent).
  - Pause for M after each part unless M said to run without pausing.
  - After each part: run the ship check and send a done report for that part.
  - Then update the plan file: tick the part's boxes, set Status to `in progress (part N of <total>)`, `blocked (waiting on M)`, or `done`, and add a line under Changes made (`<date>, part N: <what shipped>`).
  - A part blocked on M's answer stays blocked; go on to the next part only if it does not depend on the blocked one, and say so.
- **Keep parts small.** If a part grows past about a day of work or cannot be checked alone, stop and propose a split to M.
- **Predict, then act.** One line, `Expect: <outcome>`, before any action that could fail. After at most 3 actions (an action is one edit or one command), check against reality: run it, read it, confirm. A todo list is not a check. When the result differs, say `Surprise: expected X, got Y`, and fix your understanding before the next action.
- **Fix the cause, not the symptom.** Change the layer that owns the rule. Never ship a try/except that swallows, a special case for one id, a wrapper that returns a default, a retry that masks flakiness, a cache that hides stale data, a duplicate check "to be safe", a guard that skips a broken path, a null-default downstream of bad data, a sleep for a race, or a flag that hides a broken path. Recurrence test: with your change in place, can the same cause produce the problem again? The answer must be no.
- **No sunk cost.** When new evidence undermines the plan, stop and re-decide with M, ignoring the effort already spent.
- **Safe by default.** A new config value or security parameter is validated where it loads: a safe default, a minimum and a maximum, and `0`, empty, or `None` never mean "off" or "forever". Security failures raise; they are not returned as a value the caller can ignore. When writing Python or JavaScript/TypeScript, or touching config, auth, outbound fetches, dependencies, Kubernetes, IAM, or LLM tools, check the matching section of `references/misuse-patterns.md`.
- **Interfaces.** Before the first edit to a user interface, read `references/design.md` in full, then `references/design/ADAPTATIONS.md` and the one source file it names for the task. Just before the edit, read the quality floor `references/design.md` names for that task.
- **Undo before confirm.** Prefer a user action that can be undone over one that asks for confirmation, and make an irreversible action harder to trigger than a reversible one.
- **Contracts.** A change to a public API, event, schema, or config key is either compatible or breaking; say which. A compatible change ships in one step. A breaking change ships in three steps: add the new form, move every caller, remove the old form. When callers live outside the repo, stop after adding the new form and ask M how and when outside callers will be told and moved; never remove the old form on your own.
- **Observable.** New behavior that runs in a live system ships with the log line, metric, or trace that shows it working and the signal that shows it failing.
- **Docs in the same part.** When behavior, a contract, or an operating step changes, update its doc in the same part. Delete docs the change makes false.
- **Error messages you write:** "Expected integer for port, got 'abc'", never "Invalid input". Same standard when reporting to M: what failed, the exact error, what it implies, what you propose.
- **Names describe behavior.** No plan, phase, or ticket codes in identifiers, comments, logs, tests, or docs.
- **Formatting.** Only functionally changed lines appear in the diff. No auto-format runs, no whitespace cleanup, no style changes outside your change. Match the surrounding style.
- **Minimal code.** Only what the plan confirmed. Three real examples before an abstraction. Comments only for rules that are not obvious, explaining why.
- **Git.** Reading history and status is fine, and so are staging named paths, creating a branch, and switching branches. Never commit, push, merge, rebase, delete, or discard work unless M asks for that exact operation. Never `git add .` or `git add -A`.
- **Production is read-only.** Never create, change, or delete anything in production. If a step needs it, give M the exact command to run. If you cannot tell whether a target is production, treat it as production and ask M.
- **Stop on failure.** When anything fails, the next output is words to M: the raw error, your theory, what you want to try, what you expect. Then wait. No silent retries or fallbacks. If the work stops there, add the "Stopping mid-task" list.

## Tests

Every behavior the part adds or changes gets a test, written before the code that makes it pass. The steps below are the short form of cover mode in `references/test.md`; read that file in full when a test needs a level or test-double choice, or when a test turns out flaky.

1. **List first.** A table with one row per behavior (behavior, test name, seen failing?, passing?) from the plan's acceptance criteria, plus its sad paths: empty, invalid, zero, maximum, duplicate, unauthorized, concurrent, timeout, failed dependency. Show M the list and wait for M's OK before writing tests, unless M said to run without pausing.
2. **Red.** Write one test and run it. It must fail for the stated reason; quote the failure message. A test that passes at once proves nothing yet: say so, and show it can fail by breaking the code it covers for one run, seeing it fail, and restoring the code exactly.
3. **Green.** The smallest change that passes it and every earlier test.
4. **Refactor** only after green, as its own step.
5. **Evidence.** Before the ship check, run the proving commands: the new tests, the affected module's tests, and the full suite.
   - Print one line per command: `VERIFY: ran <exact command>: PASS | FAIL | DID NOT RUN (exit <code>; <runner summary line>)`. The run happens after the last code change.
   - For `DID NOT RUN`, put the reason in the brackets. `DID NOT RUN` cannot be marked done, and it is not a gap M can accept away: run it or say the work is not finished.
   - The one exception: when the full suite takes longer than about 10 minutes, tell M its runtime and ask whether to run it now or leave it to CI. A suite M left to CI gets no `VERIFY:` line, and the report says `Full suite: left to CI by M`.

Never go green by deleting or weakening an assertion; skipping, deselecting, or marking a test as expected-to-fail; retrying; sleeping; pasting the actual output into the expected value; or widening a tolerance without a stated reason.

- Tests touch local or CI resources only, with dummy users, tenants, and secrets.
- For an auth or ownership rule, test with two dummy users and show the outsider is rejected on every path that reaches the data.
- Code that neither M nor an agent working for M wrote (a third-party repo, downloaded files) is run only in a sandbox with all of these controls: no external network, an empty allowlisted environment, read-only target and tools, scratch-only writes, and explicit CPU, memory, process, file-size, disk, and time limits. If any control cannot be shown to hold, do not run it and say so.

## Ship check (run before the done report; show it to M only when a line fails)

- [ ] **Plan:** matches the confirmed plan and part checkboxes, with evidence.
- [ ] **Decisions:** honors the decisions recorded in the plan, with evidence.
- [ ] **Goals:** delivers the acceptance criteria, with evidence.
- [ ] **Tests:** every changed behavior has a test that was seen failing first; a `VERIFY:` line with PASS exists for each proving command (the full suite may instead carry M's recorded answer).
- [ ] **Owning layer:** the change reaches the cause; the recurrence test answers no; no patch on top of a patch.
- [ ] **Observable and undoable:** live behavior has its signal; the rollback step in the plan still works.
- [ ] **Docs:** changed behavior, contracts, and operating steps are documented in this part.
- [ ] **Clean:** no leftover junk, dead code, work-in-progress comments, or debug prints.
- [ ] **Names:** behavior only; no plan or phase codes.
- [ ] **Formatting:** only functionally changed lines differ.
- [ ] **Ten principles:** each one is met or its gap is named.
- [ ] **Deviations:** scope creep and skipped steps are listed, or `none`.

Any failure: tell M what failed and what you propose, wait for M's OK, then fix the cause and re-run. Never call the work done on a failed check, and never declare done until it passes or M accepts the named gaps (a proving command that did not run is never such a gap, except a full suite M left to CI).

## Done report

After the ship check passes, open with the H2 heading `## Implementation: <shipped behavior>`, then:

- **Bottom line:** <what now works or what part shipped>
- **Where we are:** <part N of total | full implementation done>; ship check passed or named gap accepted.
- **Need from M:** for a part, look at it and approve or change the next part (`nothing` when M said to run all parts); for the final ship, the questions below. The final ship is the report sent when every part is ticked; a part parked while waiting on M is not shipped.

Follow with H3 sections:

- **Changed flow:** when the implementation changes a multi-step execution path, data flow, hierarchy, sequence, or causal chain, add one short diagram (its form comes from the visual ladder in core section 2.4) and mark the changed node. Omit it for simple local edits. Keep before-and-after comparisons as tables.
- **What changed:** a file table with change and reason, then the behavior change in plain words. For a contract change, say whether it is compatible or breaking.
- **Why this shape:** the owning-layer reason.
- **Problem solved:** the outcome M confirmed, and for a user-facing change, the success metric and the date it will be checked.
- **Verification:** the `VERIFY:` lines and each other executed check with its observed result; separate static inspection from runtime evidence.
- **What is left:** open work, risks, follow-ups, or `nothing`.
- **Deviations:** where the build departed from the plan and what caused each departure; `none` when it did not.

End with `## In plain terms`: two or three sentences for someone who reads nothing else, saying what M gets now and any catch. Do not repeat `Need from M`.

For a final ship, the `Need from M` field must include:

- `Have a fresh reader check the diff against the plan and the ten principles?` M's yes also approves fixing what the reader finds. Then:
  - Launch one fresh `general-purpose` subagent with only these, as absolute paths: the repo path, the exact diff command, the plan path, and this skill's `references/ten-principles.md` and `references/misuse-patterns.md`. When there is no plan (a small change made outside one), first write a short description of what the change is for to a file and pass that in place of the plan path.
  - The diff command: in git, `git diff <base>` for tracked changes plus the list of new untracked files, where `<base>` is the commit that was checked out when the build began (nothing has been committed since). Outside git, `diff -ru <backup-folder> <project-folder>`.
  - It reports each place the diff breaks an acceptance criterion or a principle, with `file:line` and a severity: `blocking` (wrong behavior, data loss, or an unsafe change), `must-verify` (one check settles it), or `note`.
  - Re-read every location a blocking finding cites before answering it. Fix what is valid, with a test, then follow the build row of `references/modes.md`: it says whether another reader runs and when M is asked.
  - Tell M what you fixed, and what you contested and why.
- For a user-facing change: `The success metric is due to be checked on <date>. How do you want to be reminded?`
- `Delete <plan path>, or keep it?`
- If a worktree was made: `Remove worktree <path> and branch <name>?` with anything uncommitted or unmerged named first, because removal deletes it. If backups were made instead: `Delete the backup folder <path>, or keep it?`

Wait for M's answers before doing any of them.

## Stopping mid-task

Whenever you stop before the work is finished, tell M: what is done, in progress, and untouched; the ship-check status; blockers; open questions; your recommendation; and the files touched.
