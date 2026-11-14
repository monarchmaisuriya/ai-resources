# Test doubles and fixtures

Read in cover mode, step 3, and whenever a test needs setup data.

## Kinds of double

A test double is any stand-in for a real collaborator. The names matter because they decide what the test can assert.

| Double | What it does | Use when | Assert on |
| --- | --- | --- | --- |
| Stub | Returns canned answers | The collaborator's output drives the behavior under test (a payment API says "declined") | The resulting state or output of the unit under test |
| Fake | A working, simpler implementation (a local clock, an in-memory stand-in for a third-party service) | The real one needs a network or a service we do not own, and behavior across several calls matters | State, as with the real thing |
| Spy | Records calls, otherwise real or stub | You must prove an outbound side effect happened (an email was queued) and there is no observable state to check instead | The recorded call, only when no state exists |
| Mock | Pre-programmed expectations about calls; fails if unmet | Rarely; only when the interaction is the contract (a required webhook) | The interaction itself |

Source for the stub versus mock split: Ham Vocke, The Practical Test Pyramid: "Mocks and Stubs are two different kinds of Test Doubles" (https://martinfowler.com/articles/practical-test-pyramid.html). Prefer state over interactions (Google ch. 12, "Test State, Not Interactions").

## What to double

- Default: real collaborators inside the process. Prefer a sociable unit test (real collaborators). A solitary one (collaborators replaced) is acceptable only when those collaborators sit at a boundary listed in the next line (Vocke).
- Replace only at boundaries we do not own: the network, the clock, randomness, filesystem outside the test's temp dir, third-party APIs.
- Never replace the unit under test, its own module, or the database in a test whose behavior is the query. Use a local database.
- When the stub stands in for a service we also own, there must be a contract test somewhere that runs the consumer's expectations against the real provider; otherwise the stub drifts and the green test lies. Consumer-driven contract tests "let the consumers drive the implementation of a contract" (Vocke). Name that test in the row's note, or write `nothing found` and add it to Gaps.

## Fixtures and data

- Each test creates the data it needs, in the test body or a factory call visible from the test. Loaded fixture files go stale and hide the inputs (mystery guest).
- No shared mutable fixture across tests. Module- or session-scoped objects that a test mutates are the most common source of order-dependent flakiness.
- Factories over fixture files: a factory call with the two fields that matter is readable; a 300-line JSON file is not.
- The clock is injected or frozen; `now()` inside the unit under test is a hidden input.
- Randomness is seeded or injected; an unseeded random value in a test is a flaky test waiting to happen.
- Database tests: use the rollback-per-test fixture by default; use the real-transaction fixture only when the code commits or relies on commit hooks (`references/frameworks.md`).
- Temp files go in the runner's temp directory and are not shared between tests.

## Property-based tests

When a behavior holds for a whole range of inputs (round trip, idempotence, an invariant such as "sorted output has the same elements"), a property-based test generates the inputs and shrinks a failure to a minimal example. Hypothesis: "you write tests which should pass for all inputs in whatever range you describe" (https://hypothesis.readthedocs.io/en/latest/). Use it for parsers, serializers, money arithmetic, and permission logic; keep one plain example-based test beside it so the reader sees a concrete case.

## Sources

- Ham Vocke, The Practical Test Pyramid (martinfowler.com, 2018): https://martinfowler.com/articles/practical-test-pyramid.html
- Software Engineering at Google, ch. 12 Unit Testing: https://abseil.io/resources/swe-book/html/ch12.html
- Hypothesis documentation: https://hypothesis.readthedocs.io/en/latest/
