# Code rubric

## Contents

- The criteria
- Triage the diff first
- Review code in passes
- Code-specific blocking conditions
- How the ten principles map to these criteria

## The criteria

Review the changed behavior, not only changed lines. Bias toward correctness, modularity, reusability, and simplicity.

1. **Correctness and invariants:** The implementation meets the behavior the plan or description states and preserves domain rules across happy and failure paths.
2. **Modularity and cohesion:** Each unit has one job and one reason to change. Boundaries do not drag unrelated code together.
3. **Dependency direction:** UI, API, and infrastructure depend on stable domain contracts. Domain logic does not depend on delivery details.
4. **Single source of truth:** Each rule, schema, configuration value, and user-facing fact has one owner. Do not demand abstraction before three real examples justify it.
5. **Simplicity and YAGNI:** The implementation uses the fewest moving parts that meet the confirmed need. No speculative features or clever control flow.
6. **Explicit boundaries and contracts:** Public functions, APIs, events, schemas, and module exports validate inputs and do not expose internals.
7. **Composition and reuse:** Existing published units are reused when suitable. New units remain usable without pulling unrelated system state.
8. **Failure behavior:** Invalid input fails at the boundary. Errors remain actionable. Retries, timeouts, partial failure, concurrency, and cleanup preserve correctness.
9. **Data, security, and compatibility:** Authorization, validation, privacy, migrations, backward compatibility, and destructive behavior are correct where applicable. Every change to a public contract is classified as breaking or compatible.
10. **Verification:** Tests cover behavior and meaningful failure paths at the lowest useful level. Tests would fail if the reviewed defect were introduced.
11. **Operations:** Logs, metrics, traces, configuration, rollout, and resource use make production behavior diagnosable where applicable.
12. **Comments and documentation:** Public contracts and non-obvious invariants are documented. Comments explain why, remain accurate, and do not narrate obvious code.

For code, inspect callers, callees, tests, schemas, and deployment effects. A locally correct diff can still violate a system contract.

## Triage the diff first

Classify every changed file by risk tier before the passes. The tier decides review depth; severity still rates each finding.

| Tier | Triggers |
| --- | --- |
| high | Auth or permissions, secrets or crypto, billing or money, external calls, data deletion or migration, removed or weakened validation |
| medium | Business logic, state changes, new public API, task, or event |
| low | Comments, test-only changes, logging, UI copy |

- A refactor is high until behavior preservation is shown.
- Choose a depth and record it under Scope and limits: **deep** (read every dependency of the changed code), **focused** (one hop of callers and callees), or **surgical** (high-tier paths only, for very large diffs).
- **Unfamiliar code with no diff** (M wants it understood or reviewed as a whole; a security audit goes to Security audit mode in `references/review.md` instead): follow `references/full-audit.md` first. Then run passes 3, 6, and 7 on its ranked functions, starting with its `nothing found` list.
- Order the work by risk tier, then blast radius (pass 4). High tier with many callers gets the deepest look.

## Review code in passes

1. **Intent and diff shape:** Compare the behavior that the description, plan, and decision record state with the complete diff. Detect unrelated changes, generated files, debug artifacts, and omitted dependents.
2. **History of removed code:** For every removed or weakened check, find why it existed before judging its removal.
   - Read the old version with `git show <base>:<path>`; do not check out the baseline.
   - Run `git log -S '<removed text>'` and `git blame <base> -- <path>`, then read the commit that added it.
   - Escalate when the removed code came from a fix, security, or incident commit.
   - Flag a regression when added code matches code that an earlier fix removed (`git log -S '<added text>'`).
3. **Behavior and invariants:** Execute or mentally trace normal, boundary, invalid, duplicate, stale, unauthorized, concurrent, retry, timeout, and partial-failure paths. For each high-tier diff region, state before, after, and the behavioral change.
   - **Name what makes each assumption true.** For each assumption the changed code relies on (a bound, a non-null value, an authenticated caller, a unique row), cite the `file:line` that establishes it. Read the function that is supposed to establish it and follow every path through it, not only the one that succeeds: a check present on three paths of four is an assumption, not an invariant. When no line establishes it, write `nothing found`. Keep that exact phrase so a search lists every unenforced assumption.
   - Never infer behavior from a name: `validate_x` may not validate anything.
   - When the source of a callee is not available (external API, unread library), treat it as hostile: record what is sent and which outcomes are not ruled out (error, unexpected value, timeout, re-entry).
4. **Architecture and blast radius:** Check ownership, cohesion, dependency direction, contracts, reuse, simplicity, and whether the change introduces a second source of truth. For each modified high- or medium-tier unit, list its callers and how they were found. Mark callers reached through string dispatch (task names, signal handlers, `getattr`, reflection) as `[unverified]` unless traced, because text search misses them.
   - **Where auth actually lives:** in a service, the check usually sits in middleware, a decorator, or the route registration, not in the handler. Name where it lives, and confirm every route on that path gets it.
   - **Coupling through shared state:** code that never calls each other is still coupled when it writes the same row without a transaction, or one part writes a cache key another reads. List those writers and readers with the callers.
5. **State and data:** Trace reads, writes, transactions, ordering, idempotency, consistency, migrations, backfills, retention, privacy, and rollback. In a multi-tenant store, every read and write is scoped by the caller's tenant, taken from the authenticated context and never from the request body. Check that a cross-tenant read fails.
   - **A tenant field is not isolation.** Find the query, key, path, or policy that enforces it on each read and write path, including joins, aggregates, raw or service clients, and paths where the context is missing.
   - **Derived copies.** Follow the data into caches, search indexes, exports, backups, logs, analytics, previews, and queued jobs. Each copy needs the same access rule and the same deletion or revocation behavior.
   - **Keys that omit the tenant.** Cache keys, object paths, dedup keys, temp file names, and search document IDs that leave out the tenant let two tenants read or overwrite one entry.
6. **Security:** Trace trust boundaries, authentication, authorization, input handling, output encoding, secret exposure, injection, dependency risk, logging of sensitive data, and abuse limits when applicable.
   - **Defense in depth:** When a check is removed, search for the same check on sibling paths. If the siblings all keep it, flag the removal as breaking the pattern.
   - **Reachability:** A security finding must show the attacker (who), their access (privileges), the entry point (exact endpoint, task, or function), each hop from entry to the changed line as `file:line`, exploitability (`easy`: one call with ordinary access; `medium`: several steps, timing, or obtainable elevated access; `hard`: admin access or rare conditions), and concrete impact. Downgrade or drop a finding whose path is unreachable.
   - **Candidate gate (`references/security-audit/HUNTING.md`).** Before a security candidate becomes a finding, name: the lower-trust principal and its starting capability; the accepted input or action; the control that should reject, bind, isolate, limit, or revoke it; the crossed boundary; the affected principal or resource; and the concrete result. A candidate missing any of these is a note or is dropped. A candidate that the source disproves goes to Ruled out, not must-verify.
   - **Preventing layer.** If a visible control already prevents the attack, the absence of a second layer is a `note` (hardening), not `blocking`.
   - **No escalation.** Do not turn a crash into code execution, ordinary work into a shared-availability outage, or a same-principal action into privilege gain.
   - **Outside the repo.** Proxy, WAF, CDN, browser, identity provider, IAM, Kubernetes, broker, and provider settings that the source does not show are neither present nor absent. When one decides the finding, it is `must-verify` with the exact missing fact and a safe check the owner can make; never probe a live system to settle it.
   - **Security severity anchors.** Record one of these in the finding's Evidence, from the demonstrated result only. `critical`: an unauthenticated actor gains code execution, full data-store access, or takeover of arbitrary accounts. `high`: an explicit control is fully defeated with real consequences (auth bypass, cross-tenant read or write, stored script running for other users, authenticated code execution, unauthenticated stop of a shared service). `medium`: a real boundary violation with limited blast radius or uncommon preconditions. `low`: non-secret internals disclosed, or minimal gain for sustained effort. `informational`: confirmed but minimal, mainly a prerequisite inside a larger finding. Discriminator between high and medium: does the result fully defeat an explicit control for an action with real consequences, or only weaken it? If you cannot state the concrete damage, it is lower than it feels. Mapping to the severity in `references/review.md`: critical, high, or medium -> `blocking`; low or informational -> `note`; unresolved deciding fact -> `must-verify` with no security severity.
   - **Variant search.** After any blocking security finding, search for the same root cause by text (same call or pattern), by structure (same call shape on another route, task, or consumer), and by logic (the same rule enforced elsewhere, or a sibling path to the same effect: list, bulk, export, import, search, background task, admin, legacy, retry, migration). Fold every variant into the one finding, each with its own path; a variant with different conditions gets its own reachability.
   - **Attack classes.** For a high-tier security-relevant diff, read `references/security-audit/ATTACK-CLASSES.md` (Access control, Business logic, Feature abuse and data leakage, Chained vulnerabilities, Obvious things) and the companion file in that same folder whose "When to use this file" matches the diff: `WEB-PROTOCOL-AND-AUTH.md`, `DATA-ISOLATION-AND-LIFECYCLE.md`, `AI-AND-LLM.md`, `CLIENT-SIDE.md`, `SUPPLY-CHAIN-AND-RELEASE.md`, `CLOUD-AND-DEPLOYMENT.md`, `PROTOCOLS-RPC-AND-MESSAGING.md`, `RESOURCE-EXHAUSTION-AND-AVAILABILITY.md`, `DESKTOP-MOBILE-AND-LOCAL-IPC.md`, or `MEMORY-SAFETY-AND-BINARY.md`. Apply its Core discipline and Validation rules. Name the files read under Scope and limits.
7. **Misuse resistance:** Run when the diff adds or changes a config value, flag, environment variable, constructor or public-function parameter, an auth or session path, a verify/check function, a Kubernetes manifest or Helm values, an IAM policy, a dependency manifest or lockfile, a server-side URL fetch, or an LLM call, prompt, or tool definition.
   - **Probe edge values:** what do `0`, `""`, `None`, `[]`, `-1`, and a typo (`"fasle"`) do? Flag any value that disables a check, means "forever", or is accepted with no error.
   - **Classify what you find:** unsafe default; one setting that turns safety off; failure reported as a return value the caller can ignore; caller picks the algorithm or mode; two same-typed parameters that can be swapped with no error; security values as free strings (roles, scopes, SQL).
   - **Ask three questions:** can whoever controls config switch the safety off? Is copying the first example safe? Can a confused caller swap arguments and get no error?
   - **Required read:** before reporting, read the sections of `references/misuse-patterns.md` that match the diff (its language, plus Config, Auth, Outbound requests, Dependencies, Kubernetes and Helm, IAM, or LLM and agent tools when touched). Name the sections read under Scope and limits.
   - **Show it:** for each misuse finding, give the smallest code that triggers it.
   - **Severity:** unsafe default or unsafe obvious usage is `blocking`; an easy misconfiguration is `blocking` on a high-tier file, otherwise `must-verify`; an unusual combination is a `note`.
8. **Compatibility:** Check API, event, schema, configuration, serialization, client, deployment, and mixed-version compatibility.
9. **Tests:** Confirm tests exercise behavior, meaningful failures, regression risk, and contract boundaries. Check assertions, fixtures, isolation, determinism, and whether each test would fail for the targeted defect. When the suite itself is in scope, build a gap table: each invariant, each path that reaches it, the test that fails if it breaks or `nothing found`, and a mutant probe on the riskiest paths (change one line so the code is wrong and see whether a test fails). Escalate test gaps:
   - New behavior with no test: `must-verify`; `blocking` when it is the change's highest-risk behavior or the file is high tier, because that meets the blocking condition "no credible verification for its highest-risk behavior".
   - Changed validation or auth with unchanged tests: at least `must-verify`; `blocking` when the file is high tier.
10. **Operations:** Check useful failure signals, metrics, traces, alert implications, resource bounds, cancellation, cleanup, feature rollout, and rollback. Login, one-time codes, public ingestion endpoints, and LLM calls have a rate or cost limit per caller.
11. **Documentation:** Check public behavior, operational instructions, migration notes, and non-obvious invariants.

Do not claim a test, build, linter, security scan, or runtime behavior passed unless it was actually run and observed. Static inspection and execution evidence are separate.

## Code-specific blocking conditions

- The implementation violates stated behavior or a domain invariant.
- Auth, data integrity, public contracts, or destructive operations are unsafe.
- A migration cannot preserve or recover data under a credible failure.
- Failure handling can silently corrupt state or report success incorrectly.
- Required compatibility is broken.
- The change has no credible verification for its highest-risk behavior.
- A local fix leaves the systemic cause intact and recurrence remains likely.

Escalate these immediately, even in a surgical review, and run the history and reachability checks on them:

- Removed code that a fix, security, or incident commit added.
- A permission check removed or relaxed (a permission class dropped, a private unit made public).
- Validation removed with no replacement.
- A new external call with no timeout or error handling.
- A high-tier change with a wide blast radius.

## How the ten principles map to these criteria

Principle 1 is criterion 1; principle 6 is criteria 2, 3, 4, 6, and 7; principle 7 is criterion 5; principle 8 is criterion 8; principle 9 is criterion 10; principle 10 is criterion 12. Criterion 9 carries principles 5 and 8, and criterion 11 carries principle 9 (the behavior can be observed in production). Principles 2 to 4 are carried by the Evidence procedure in `references/review.md`, and principle 5 also by the blast-radius pass.
