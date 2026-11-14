# Flaky tests: root causes and the experiment for each

Read when a test passes on some runs and fails on others. This file does not fix anything; it seeds the list of candidate causes so the first experiment discriminates instead of guessing. Start the root-cause work with: the test id, one captured passing output, one captured failing output, and the rows of the root-cause table below that apply.

## Why not rerun it

Luo et al. studied 201 commits that fix flaky tests across 51 open-source projects. 24% of the fixes changed the code under test, and 94% of those fixed a real bug there; the authors conclude that "Flaky tests should not simply be removed or disabled because they can help uncover bugs in the CUT" (finding F.12). 78% of flaky tests were flaky from the moment they were written (F.2), so a new test that flickers is the normal case, not bad luck.

Source, read from the paper's text on 2026-10-24: Luo, Hariri, Eloussi, Marinov, An Empirical Analysis of Flaky Tests, FSE 2014. https://dl.acm.org/doi/10.1145/2635868.2635920

## Root causes (Table 2, the 201 inspected commits) and the experiment that confirms or kills each

| Cause | Commits | What it looks like | One discriminating experiment | Usual fix (layer that owns the rule) |
| --- | --- | --- | --- | --- |
| Async wait | 74 | The test checks a result before an asynchronous step finished; often a fixed sleep | Vary the delay: make it much longer and much shorter; failure rate moves with it | Wait for the condition, not the clock (54% were fixed with an explicit wait on the event, F.8) |
| Concurrency | 32 | Two threads touch the same memory; the result depends on scheduling | Run under a tool that raises context-switch frequency, or pin to one thread; 97% involved only memory objects and two threads (F.6) | A lock, deterministic ordering, or a changed condition; no single recipe (F.9) |
| Test order dependency | 19 | Passes alone, fails after another test (or the reverse) | Run the test alone, then after each candidate test; 47% depended on an external resource such as a file or database row (F.7) | Clean shared state before and after (74% fixed this way, F.10) |
| Resource leak | 11 | A connection, file handle, or temp file is not released; later tests starve | Count open handles or connections before and after the test | Release in teardown; the leak is usually in the code under test |
| Network | 10 | The test reaches a real host | Cut the network; the test must fail deterministically, not sometimes | Stub the boundary; a contract test keeps the stub honest |
| Time | 5 | Depends on wall-clock, time zones, or midnight | Freeze the clock to the boundary value (23:59:59, DST change, leap day) | Inject or freeze the clock |
| IO | 4 | File system ordering, partial writes, encoding | Run on a fresh temp dir; list the directory the test assumes | Explicit ordering and encoding |
| Randomness | 4 | Unseeded random input | Fix the seed; the test becomes deterministic or fails always | Seed or inject the generator |
| Floating point | 3 | Equality on floats | Print the two values at full precision | Integers for money and ids; tolerance with a stated reason otherwise |
| Unordered collections | 1 | Iterating a set or dict and asserting order | Compare as sets, or sort before comparing | Assert on the set, or sort |
| Hard to classify | 40 | None of the above explains it | Collect ten runs with timestamps and environment; diff the first divergence | Depends on the finding |

The counts are the "Total" column of Table 2 for the inspected commits. The paper reports percentages against the 161 classified commits (for example, 32 of 161 Concurrency commits is 20%); use counts rather than recomputing percentages.

## Starting block for the root-cause work

- **Symptom:** `<test id>` passed on run `<n>` and failed on run `<m>` with `<quoted assertion>`.
- **Captured:** both outputs, verbatim, in the report.
- **Candidate causes:** the causes above whose "what it looks like" matches, each with its experiment as the first action.
- **Not allowed as the fix:** rerun, skip, expected-to-fail marker, sleep, a tolerance widened without a stated reason.
- **Done means:** the cause is confirmed by its experiment, the fix is made in the layer that owns the rule, and the test is shown deterministic (for example 20 consecutive runs, alone and inside the suite) with the `VERIFY:` lines.

## Why trust matters more than compute

Gruber and Fraser surveyed 335 professionals: flaky tests are "a common and serious problem", and developers worry less about the cost of re-running tests than about "the loss of trust in the test outcomes" (ICST 2022, https://arxiv.org/abs/2203.00483). A retried-to-green test costs a little compute and all of the suite's credibility.
