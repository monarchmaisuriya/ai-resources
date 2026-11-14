# Decision record template

Use for one decision that is costly to reverse: a data model, a public contract, a vendor, a framework, a security boundary. One decision per record. Save as `docs/adr/NNNN-short-title.md`, numbered in order. Never rewrite an accepted record: write a new one and mark the old one `superseded by NNNN`.

Copy everything below this line.

---

# NNNN. <Decision in a short phrase>

- Status: proposed | accepted | superseded by NNNN
- Date: <YYYY-MM-DD, from the system clock>
- Decided by: <M, or "proposed by agent, waiting on M">
- Supersedes: <NNNN or none>
- Related records: <NNNN, or none>
- Owner: <who keeps this decision true>
- Stakeholders: <who is affected and must be told>
- Review when: <the event or date that should reopen it>

## Context

What is true today, the problem, and why a decision is needed now. Facts only, each tagged `[verified: file:line or link]`. No argument for an option here.

## Assumptions and unknowns

What this decision takes as true without proof, and what is not yet known, each with the check that would settle it. Write `None` when there are none.

## Decision drivers

The needs and limits that matter, most important first: quality needs, cost, deadline, policy. Where two conflict, say which wins.

## Options considered

Include keeping things as they are when that is possible.

| | Option A | Option B | Keep as is |
| --- | --- | --- | --- |
| How it works | | | |
| How it serves each driver | | | |
| What it gives up | | | |
| What it costs (effort, money, risk) | | | |
| When it fails | | | |
| Why chosen or rejected | | | |

## Decision

We will <do what>, because <the driver it serves best>. State who does what, where the boundary sits, and what is deliberately not decided.

## Consequences

- Good: <what gets better>
- Bad: <what gets worse; do not soften it>
- Can it be undone: <yes or no, the cost of undoing it, and the sign that would make us reconsider>

## Transition

Only when the decision changes a live system: how data and callers move, the rollout order, how it will be watched, how to roll back, and what gets retired. Otherwise write `n/a` and why.

## How we will know it is followed

The test, automated check, or review step that confirms the decision is being honored.
