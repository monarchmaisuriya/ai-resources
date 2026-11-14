# Test playbook

M is the person you are working for. M makes every decision this playbook sends to them.

The ten principles in `references/ten-principles.md` apply to tests as much as to code; principle 9, provably done, is this playbook's whole job. Read that file first.

A test earns its place by failing when the behavior breaks. Every test answers two questions before it is written: which behavior, and what would make this test fail. A green run that was never red proves only that the assertion holds in the current state; it says nothing about the code.

## Pick the mode

| The ask | Mode |
| --- | --- |
| new or changed behavior; a part of a plan; a bug fix that needs its proving test; "write tests for X" | cover |
| "is X tested", "what would the suite miss", "audit the tests", an unfamiliar module before changing it | audit |
| "run the tests", "prove it passes", before calling work done, before any "tests pass" claim | verify |

Cover ends with verify.
- A bug fix enters cover with the broken invariant from its diagnosis as the first row of the test list.
- A confirmed security finding enters cover the same way: its regression case is the first row, its bounded reproduction (dummy principals, the interface a real caller uses, the observed result) becomes the red test, and each variant path in the finding is its own row.

## Rules in every mode

- Test through the entry point the real caller uses (public function, route, task), because tests bound to internals break on every refactor and stay green on behavior changes. A pure function with no I/O may be tested directly.
- One behavior per test; the name states the behavior and its condition (`test_refund_rejected_when_order_already_refunded`), because a failure then names the broken rule before anyone reads the body.
- Passing examples do not prove a rule. For each rule, look for one input that would break it, and add it to the test list as a row.
- Assert state and outputs, not which collaborators were called, because interaction assertions pin the implementation rather than the behavior. The one exception is when the interaction itself is the contract (for example, "sends exactly one email").
- Straight-line test code: no loops, branches, or computed expected values; expected values are literals (a property-based test is the one exception: it asserts a rule over generated inputs, and a failing example is replayed from the tool's own record or a pinned seed), because a test with logic needs its own test.
- Prefer a little duplication to a helper that hides the setup; the reader must see inputs and expected outputs in the test body.
- Failure messages name expected, actual, and the inputs; "assertion failed" is not a message.
- Deterministic by construction: the clock, randomness, ordering, and the network are controlled inside the test, never retried around.
- Tests touch local or CI resources only: never a production database, queue, bucket, or API. A connection string in a test that resolves to production stops the work: tell M.
- Dummy principals, tenants, tokens, and secrets only. A real credential, token, or production identity in a fixture stops the work: tell M, even if it is expired.
- Code that neither M nor an agent working for M wrote (a third-party repo, an audit target, downloaded files) runs only inside a sandbox with all of these controls: no external network, an empty allowlisted environment, read-only target and tools, scratch-only writes, explicit CPU, memory, process, file-size, disk, and time limits. If any control cannot be shown to hold in this session, do not run it: the evidence line is `DID NOT RUN` with the missing control named.
- A security test stops at the minimum observed effect: the wrong return value, the unauthorized dummy record, the sanitizer report. No payload chains, no load generation, nothing past the first proof of the broken boundary.
- Forbidden ways to go green: deleting or weakening an assertion; skipping, deselecting, or marking a test as expected-to-fail; retrying; sleeping; pasting the actual output into the expected value; widening a tolerance without a stated reason. Each hides the defect the test exists to catch. A characterization test (cover step 8) records current behavior on purpose and is the one exception to the pasting rule.
- Evidence line, defined here:
  - `VERIFY: ran <exact command>: PASS | FAIL | DID NOT RUN (exit <code>; <runner summary line>)`.
  - The run happens in the current session, after the last code change.
  - For `DID NOT RUN`, put the reason (for example the missing sandbox control) in the brackets instead of an exit code.
  - A `DID NOT RUN` command proves nothing: never report its behavior as tested.
  - The one exception is a full suite M chose to leave to CI: it gets no `VERIFY:` line, and the report says `Full suite: left to CI by M`.

Quality checklist, smells, and Beck's mistake list: `references/test-quality.md`. Doubles and fixtures: `references/doubles-and-fixtures.md`. Framework facts: `references/frameworks.md`.

## Mode: cover

Before the first test, read `references/test-quality.md`. For a framework that `references/frameworks.md` does not cover, read that framework's official docs for the version in the project's lockfile first. When the code under change has no tests at all, do step 8 before step 1.

1. **Test list.** One row per behavior, from the plan's acceptance criteria (Given/When/Then), the diagnosis, or M's ask. Show it to M as a table `| behavior | why it could break |`; level and doubles are chosen in steps 2 and 3. Then add rows for:
   - **Sad paths** the interface accepts: absent, empty, invalid, zero, negative, maximum, over-limit, duplicate, mixed encoding or Unicode forms, stale, revoked or expired, reordered, unauthorized, concurrent, timeout, partially migrated, failed dependency, and rollback, plus the edge values `0`, `""`, `None`, `[]`, `-1`.
   - **Handoffs:** at each handoff where one component passes data to another that may read it differently (a parser and the policy check after it, for example), a row where the two sides disagree on canonical form or units.
   - **Slips:** steps done in the wrong order, the same action submitted twice, and the right action taken in the wrong mode.
   - **Limits:** where a limit or load is stated, a row beyond it, not only at it.
   - **Counterexamples:** for each rule, the one input you think most likely to break it.
   - **Special cases:** for a user interface, the accessibility level the plan names; for an A/B experiment, a check that the split between groups matches the configured ratio.
   - Wait for M's OK on the list before writing a test, unless M said to run without pausing. The characterization tests in step 8 come first and are the one exception.
   - **Boundary tests.** For every auth, ownership, or tenant invariant, use two dummy principals (owner and outsider, or tenant A and tenant B). List every path that reaches the same effect: direct read, list, count, search, bulk, export, import, background task, webhook, admin, legacy, retry, restore. One row per path: the outsider is rejected and nothing of the owner's leaks in the response body, error message, status code, or a derived copy (cache, index, export file). Rows discovered while working are added to the list, not handled on the spot.
2. **Level per row.** Push down.
   - Pure logic -> unit (one process, no I/O).
   - A database, queue, cache, or HTTP boundary -> integration against a local instance.
   - End-to-end only for what lower levels cannot show (the route is wired, the worker consumes the queue).
   - Name each test's size: small (one process, no I/O, no sleep), medium (this machine only), or large (more than one machine).
   - A unit test is small, an integration test against a local instance is medium, and an end-to-end test across machines is large.
   - Most tests are small.
   - A behavior proven at a lower level is not repeated at a higher one.
3. **Doubles per row.** Real collaborators inside the process by default. Stub only at boundaries we do not own: the network, the clock, randomness, a third-party API, the filesystem outside the test's temp folder. Never double the unit under test or its own module. When stubbing a service we also own, name the contract test that keeps the stub honest. Read the decision table in `references/doubles-and-fixtures.md` before choosing.
4. **Red.** Write exactly one test.
   - Run it.
   - It must fail for the stated reason: quote the assertion message.
   - A test that passes immediately is a finding, not progress: either the behavior already exists (say so; keep the test as a characterization and show it can fail by running one mutant probe, audit step 4, against it) or the test does not exercise it (fix the test).
   - A never-red row with no killed mutant is listed under Gaps.
   - A test that fails for another reason (import error, missing fixture) is not red; fix the harness first.
5. **Green.** The smallest code change that passes this test and all earlier ones. Run the new test and the module's tests. Cases discovered here go on the list.
6. **Refactor** only after green, as its own step, inside this part's scope; never mixed into step 5.
7. Repeat from step 4 until the list is empty.
8. **Untested code.** When the code under change has no tests, write characterization tests first: call it with representative and edge inputs, assert what it does now, run them green, and show each one can fail with one mutant probe (audit step 4). Then start the list. Changing behavior nobody has pinned is how regressions ship unseen.
9. **Verify mode**, then the report.

Predict before each run: `Expect: fails with <message>` or `Expect: passes`. A wrong prediction is a surprise: say it, fix the model, then continue.

## Mode: audit

Audit reports; it never leaves a change in the code or the tests. The mutant probe in step 4 is temporary and is restored exactly.

1. **Scope and invariants.** The module or diff, and what must hold: from the plan, the diagnosis, or a read of the code. Tag each invariant with the line that establishes it or `nothing found`. For each invariant, list every path that produces the same effect (route, task, bulk, export, import, search, admin, legacy, retry, migration); each path is its own row, because a check present on three paths of four is a gap on the fourth.
2. **Inventory.** Run the suite once on the code as you found it, before any probe, and keep its `VERIFY:` line. Which tests exist and which behavior each actually asserts: read the assertions, not the names. `test_validate` may assert nothing about validation.
3. **Catching test or `nothing found`.** For each invariant and path, the test that fails if it breaks. Keep the exact phrase `nothing found` so a search lists every gap. When the answer depends on a fact the repo cannot show (deployed config, a provider setting), write `blocked: <exact missing fact>`; it never counts as covered.
4. **Manual mutant probe** on the three highest-risk paths (pick them from auth, money, data writes, and validation). When auditing a diff, probe only lines the diff changed.
   - Copy each file you will mutate first.
   - Invert the condition, delete the line, or remove the tenant or owner filter on one other path from step 1.
   - Run the tests that should catch it, and record `killed` (a test failed) or `survived` (all green). Survived means the suite would ship that defect.
   - Restore the code exactly, and confirm each mutated file is byte-identical to its copy with `cmp <file> <copy>`. Compare the files, not whole directories: test runs create cache and build files that are not changes to the code.
5. **Smell scan** against the catalog in `references/test-quality.md`.
6. **Report** the Surviving defects table. Fixes go to cover mode; a suite that needs structural work (shared mutable fixtures, no harness) needs a written plan (`references/plan.md`) and M's sign-off.

## Mode: verify

1. **Name the proving commands:** the new tests by node id, the affected module's suite, then the full suite (state the runtime; if unknown, ask M, or run it under a 10-minute timeout; a run the timeout cuts off is `DID NOT RUN (timed out after 10 minutes)`). When the full suite takes longer than about 10 minutes, tell M its runtime and ask whether to run it now or leave it to CI, and record M's answer.
2. **Run each**; read the exit code and the runner's summary line. Never reuse an earlier run. Never accept a subagent's "tests pass" without its evidence line.
3. **Print one `VERIFY:` line per command.**
4. **Any failure stops the work**: the raw error, your theory, the proposed next step, then wait for M. Failures you did not cause are still reported by name; "pre-existing" is a fact to report, not a reason to omit.

## Flaky tests

A test that passes on some runs is a bug, in the test or in the code. Do not rerun it to green, skip it, or add a sleep. Capture one passing and one failing output, then find the root cause before changing the test's logic or the code (an experiment may edit the test for one run and is then restored), starting from the candidate causes in `references/flaky-tests.md` (one discriminating experiment per cause). State how determinism will be shown after the fix (for example, 20 consecutive runs, alone and inside the suite).

For a flaky test, report under the H2 heading `## Flaky test: <test id>` with the same three opening fields (Bottom line: the cause, or that it is not yet found; Where we are: Test, flaky, <N> causes tested; Need from M: OK to fix, or a decision), then a table `| candidate cause | experiment | result |`, the captured passing and failing output word for word, one line saying how determinism was shown (for example 20 consecutive passes, alone and inside the suite), and the `VERIFY:` lines for those runs. After M's OK, a cause in the test is fixed here. A cause in the code under test goes to `references/diagnose.md` (read it in full): the flaky report is its reproduction and trace, and the work continues from the diagnosis card.

## Reports

A change made by this playbook passes the Ship check section of `references/build.md` before its report (the plan lines are n/a when there is no plan).

Cover and verify open with the H2 heading `## Tests: <behavior covered>`, then:

- **Bottom line:** <what is now proven; N of M rows green>
- **Where we are:** Test, <mode>, step <n>; <rows red seen / green>; <full suite run or not>
- **Need from M:** <accept a row marked "did not fail first" | decide on a gap | nothing>

Follow with H3 sections:

- **Test list:** table `| behavior | level and size | test | red seen? | green? | note |`. A test that passed on its first run gets `no: did not fail first` under red seen, and its mutant probe result under note.
- **Evidence:** the `VERIFY:` lines, one per command.
- **Gaps:** rows not done and why; tests that did not fail first and have no killed mutant; `nothing` when none.

Audit opens with `## Test audit: <scope>` and the same three opening fields (Bottom line: what the suite would miss; Where we are: Test, audit, step <n>, <N> invariants and <N> gaps; Need from M), then:

- **Surviving defects:** table `| invariant | established by (file:line, or nothing found) | path | catching test, nothing found, or blocked | mutant probe |`.
- **Smells:** one bullet per smell with `file:line`.
- **Evidence:** the `VERIFY:` line for the suite run on the code as found, then one line per mutant probe: the change made (`file:line`), the command run, and the runner summary line that shows `killed` or `survived`.
- **Recommendation:** what cover mode should write first; what needs a written plan.

End with `## In plain terms`: what is proven, what is not, and the next step. Do not repeat `Need from M`. Use code fences only for commands, runner output, or source code.

## Anti-patterns (automatic stop)

When you catch yourself in one of these, stop, tell M which one, and redo the step properly.

Turning every list row into a test before any passes; counting a never-red test with no killed mutant as coverage; asserting mock calls instead of outcomes; a test that depends on a file or row it never shows (mystery guest); rerunning a flaky test until green; claiming "tests pass" from memory of an earlier run; running tests against a production connection string.
