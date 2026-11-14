# ADR rubric

## Contents

- The criteria
- How to review an ADR
- ADR-specific blocking conditions
- How the ten principles map to these criteria

## The criteria

An ADR is an Architecture Decision Record: a durable record of one architecturally significant decision and why it was made.

1. **Decision scope:** The ADR contains one decision. Its boundary and architectural significance are clear.
2. **Context and problem:** The current state, problem, forces, stakeholders, and trigger for deciding now are factual and sufficient.
3. **Decision drivers and constraints:** Requirements, quality attributes, policies, cost, deadlines, and hard constraints are explicit and prioritized where they conflict.
4. **Options considered:** Viable alternatives, including keeping the current state when plausible, receive a fair comparison under the same drivers.
5. **Evidence and assumptions:** Claims are sourced or verifiable. Assumptions and unknowns are labeled. The decision does not depend on hidden or false premises.
6. **Decision clarity:** The chosen direction is written in active, testable language. Scope, boundaries, ownership, and affected contracts are explicit.
7. **Consequences and tradeoffs:** Positive, negative, operational, security, cost, performance, and organizational effects are recorded when applicable. Downsides are not softened into benefits.
8. **Dependency and system fit:** The decision respects existing boundaries or explicitly changes them. Related ADRs are linked. Contradictions and superseded decisions are handled.
9. **Delivery and transition:** Migration, compatibility, rollout, observability, rollback, and decommissioning are covered when the decision changes a live system.
10. **Reversibility:** The ADR states whether the decision is reversible, the cost of reversal, lock-in risks, and the signals that would trigger reconsideration.
11. **Lifecycle integrity:** Status, date, owners, approvers, supersedes/superseded-by links, and review conditions match reality. Proposed work is not written as accepted fact.

Reject an ADR that merely documents a preference. A valid ADR makes the decision and its consequences understandable to someone who was not in the discussion.

## How to review an ADR

1. **Confirm significance.** Verify that the choice affects structure, quality attributes, dependencies, interfaces, data, operations, or a costly-to-reverse constraint.
2. **Separate context from advocacy.** Context states facts and forces neutrally. Decision rationale explains the preference later.
3. **Build a driver-option matrix.** Compare every viable option against the same prioritized drivers. Look for an omitted status quo, a favored option with richer evidence, or rejection reasons that merely restate the choice.
4. **Test the decision sentence.** It should identify who will do what, where the boundary sits, and what is intentionally not decided.
5. **Trace consequences.** Follow effects on users, teams, security, reliability, performance, cost, data, contracts, deployment, support, and future choices.
6. **Test reversibility.** Identify the point of no return, retained escape paths, migration cost, stranded data or dependencies, and measurable reconsideration triggers.
7. **Verify lifecycle.** Proposed means not yet approved. Accepted means the named decision-makers agreed. Changed decisions receive a superseding ADR rather than a rewritten history.

## ADR-specific blocking conditions

- There is no single unambiguous decision.
- The chosen option violates a stated hard constraint.
- A viable alternative was omitted or rejected using different criteria.
- Material consequences, migration risk, or lock-in are hidden.
- The decision relies on unverified evidence presented as fact.
- Status or approval claims do not match reality.
- The ADR contradicts an active ADR without explicitly superseding it.

## How the ten principles map to these criteria

Principle 1 is criterion 2; principle 2 is criteria 6 and 11; principle 3 is criterion 5; principle 4 is criteria 3, 4, and 7; principle 5 is criteria 7 and 10; principle 6 is criteria 1 and 8; principle 7 is criterion 4 (the current state stays an option); principle 8 is criteria 9 and 10; principle 9 is criterion 6; principle 10 is criterion 11.
