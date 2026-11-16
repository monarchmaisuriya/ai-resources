# Plan rubric

## Contents

- The criteria
- How to review a plan
- Plan-specific blocking conditions
- How the ten principles map to these criteria

## The criteria

A plan is executable when another engineer can follow it without inventing requirements or architecture.

1. **Goal fidelity:** The plan solves the problem it states (the author separately checks that this matches what was asked). Success, users, non-goals, and constraints are explicit.
2. **Current-system accuracy:** Claims about existing behavior, files, dependencies, and data flow match the repository and documentation.
3. **Scope and cohesion:** Every part contributes to the goal. Unrelated cleanup and speculative work are excluded.
4. **Architecture and dependency direction:** Work belongs in the owning layer. Contracts are defined before dependents. Existing patterns are reused when they fit.
5. **Sequencing and dependencies:** Parts are ordered by real dependencies. Each part is independently reviewable or explains why it cannot be.
6. **Completeness:** The plan covers code, data, configuration, migrations, observability, documentation, rollout, and cleanup when each applies.
7. **Failure and recovery:** Empty and invalid inputs, auth, retries, timeouts, partial failure, concurrency, rollback, compatibility, and recovery are handled or explicitly n/a.
8. **Verification:** Every task has an observable done condition. Tests and operational checks prove behavior, not merely file creation.
9. **Feasibility and precision:** Named files and contracts exist or are clearly marked as new. Unknowns have owners and resolution steps. Estimates or assumptions are not presented as facts.
10. **Decision readiness:** Open choices that materially alter the implementation are surfaced before execution. The plan does not decide critical tradeoffs for M.
11. **User and outcome:** The plan names who it is for, what they are trying to do, what they use today, one success metric, one guardrail metric (a number that must not get worse), and the date the result will be checked.
12. **Quality targets and tradeoffs:** Non-functional needs (speed, reliability, security, cost) are written as measurable scenarios. Each option says which quality it gives up. A change that crosses a trust boundary has a threat model.

13. **Cost and scope:** The plan states the effort and the time budget, what gets cut first, the risk, the blast radius, and whether the change can be undone. The plan's size fits that cost: a light plan is a short plan for a cheap, easily undone change, and a full plan is everything else. A full plan chosen because the Status line names maximal session mode fits its cost.

Criteria 11 and 12 apply only when the plan changes something with users or a live system; otherwise the verdict is `n/a` with the reason. Criterion 13 applies in full to every full plan; for a light plan, check only that the light size fits the cost.

For a plan, inspect happy path, sad path, concurrent use, deployment, rollback, and recovery. Missing one is only a violation when it can occur in the scoped system.

## How to review a plan

1. **Build a traceability map.** Connect each stated success criterion to plan tasks and verification. Flag requirements with no work and work with no requirement.
2. **Reconstruct the current system.** Verify named files, APIs, schemas, owners, data flow, callers, deployment path, and existing tests. Record drift between the plan and reality.
3. **Build the dependency graph.** For each part, list inputs, outputs, contracts created, and later dependents. Detect circular ordering and tasks that require an undeclared prerequisite.
4. **Walk scenario classes.**
   - normal use;
   - invalid, empty, duplicate, stale, and unauthorized input;
   - dependency timeout or unavailability;
   - partial completion and retry;
   - concurrent or out-of-order execution;
   - deployment with old and new versions running together;
   - rollback and recovery after data or side effects exist.
5. **Inspect every boundary change.** Public API, event, database, configuration, permissions, and operational ownership must have compatibility and validation steps.
6. **Challenge each done condition.** Ask whether a reviewer can observe it and whether the proposed test would fail if the behavior were wrong.
7. **Inspect rollout realism.** Name entry gates, exit gates, abort criteria, rollback trigger, last known safe state, and post-rollout cleanup when the change can affect a live system.

## Plan-specific blocking conditions

- The plan solves a different problem from the goal it states.
- It depends on a current-system claim contradicted by source evidence.
- A destructive or irreversible step lacks explicit approval and recovery handling.
- A public contract or data migration can break mixed-version operation without a compatibility strategy.
- A critical task has no observable done condition.
- Implementation must begin before a material unknown or decision is resolved.

## How the ten principles map to these criteria

Principle 1 is criteria 1 and 11; principle 2 is criterion 9; principle 3 is criterion 2; principle 4 is criteria 10 and 12; principle 5 is criterion 13; principle 6 is criterion 4; principle 7 is criterion 3; principle 8 is criterion 7; principle 9 is criterion 8; principle 10 is criterion 6.
