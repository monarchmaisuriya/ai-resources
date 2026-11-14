# Test quality: checklist, mistakes, smells

Read before writing a test in cover mode and before the smell scan in audit mode. Each item names what goes wrong when it is ignored.

## Checklist for one test

| Check | Why it matters | Source |
| --- | --- | --- |
| Calls the code the way a real caller does (public function, route, task) | Tests bound to internals break on refactors and stay green on behavior changes | Google ch. 12, "Test via Public APIs" |
| One behavior; the name states behavior and condition | A failure names the broken rule before anyone reads the body | Google ch. 12, "Test Behaviors, Not Methods" |
| Asserts resulting state or output, not calls made (unless the call itself is the contract) | Interaction assertions pin the implementation, not the behavior | Google ch. 12, "Test State, Not Interactions" |
| Body has everything a reader needs and nothing else | A reader must not open three helpers to learn the expected value | Google ch. 12, "complete and concise" |
| No loops, branches, or computed expected values | "If you feel like you need to write a test to verify your test, something has gone wrong" | Google ch. 12, "Don't Put Logic in Tests" |
| Failure message gives expected, actual, and inputs | A bare assertion failure sends the reader back to the debugger | Google ch. 12, "Write Clear Failure Messages" |
| Duplication kept when it makes the test clearer | Shared helpers hide the setup that explains the expected value | Google ch. 12, "DAMP, Not DRY" |
| Would fail if the behavior broke (seen red at least once, or killed a mutant) | A never-red test can be asserting the wrong thing or nothing | Beck, Canon TDD; Petrovic and Ivankovic 2018 |
| Deterministic: clock, randomness, ordering, network controlled | Otherwise it becomes a flaky test and trust in the suite drops | Gruber and Fraser 2022; `references/flaky-tests.md` |
| Runs against local or CI resources only | Production is read-only; a test is still a write | The rule in `references/test.md`: tests touch local or CI resources only |

## Beck's mistakes (Canon TDD, 2023)

Quoted or closely paraphrased from https://newsletter.kentbeck.com/p/canon-tdd:

- Converting all items on the test list into concrete tests, then making them pass one at a time (rework and discouragement).
- Mixing refactoring into making the test pass.
- Refactoring further than necessary for this session.
- Writing tests without assertions just to get code coverage.
- Deleting assertions so the test pretends to pass.
- Pasting actual output into the expected value.
- Abstracting too soon: "Duplication is a hint, not a command."

## Smell catalog for the audit scan

Names follow Meszaros (xUnit Test Patterns, 2007), van Deursen et al. (Refactoring Test Code, 2001), Delplanque et al. (Rotten Green Tests, ICSE 2019), and the Open Catalog of Test Smells (https://test-smell-catalog.readthedocs.io/). The one-line definitions below were written from those sources without re-reading them in this session; check the original before quoting a definition to M [unverified: Meszaros's site refused the connection on 2026-10-24].

| Smell | What you see | What goes wrong |
| --- | --- | --- |
| Assertion roulette | Many assertions in one test, none with a message | A failure does not say which rule broke |
| Eager test | One test exercises several behaviors | One failure masks the others; the name cannot be honest |
| Conditional test logic | `if`, `for`, `try` inside the test | Paths that never run; the test needs its own test |
| Mystery guest | The test depends on a file, fixture, or database row it does not show | The reader cannot see why the expected value is right; the external state drifts |
| Resource optimism | Assumes a file, port, or service exists | Passes on one machine, fails on another |
| Sensitive equality | Compares a whole string or serialized dump | Breaks on formatting that is not the behavior |
| Fragile test | Breaks when unrelated code changes | Bound to interface, data, or behavior it does not test |
| Erratic test | Passes on some runs | See `references/flaky-tests.md`; find the root cause before changing the test |
| Sleepy test | `sleep` to wait for something | Slow, and still flaky under load |
| Rotten green test | Passes, but some assertion never executes (early return, guard, forgotten branch) | Green with no proof |
| Obscure test | Long setup; hard to tell what is being tested | Nobody fixes it when it fails; they delete it |
| Test code duplication | The same assertion logic or helper code pasted across tests (repeated setup that keeps inputs visible is fine) | A behavior change needs N edits; one gets missed |
| Order dependence | Passes only after another test ran | Shared mutable state; see `references/flaky-tests.md` |

## Sources

- Software Engineering at Google, ch. 12 Unit Testing: https://abseil.io/resources/swe-book/html/ch12.html
- Kent Beck, Canon TDD: https://newsletter.kentbeck.com/p/canon-tdd
- Petrovic and Ivankovic, State of Mutation Testing at Google (ICSE-SEIP 2018): https://research.google/pubs/state-of-mutation-testing-at-google/
- Gruber and Fraser, A Survey on How Test Flakiness Affects Developers (ICST 2022): https://arxiv.org/abs/2203.00483
- The Open Catalog of Test Smells: https://test-smell-catalog.readthedocs.io/
