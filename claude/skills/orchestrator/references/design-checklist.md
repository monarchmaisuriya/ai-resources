# Design checklist

Work through every item when writing the Design section of a full plan. Add the item when it applies. When it does not, write `n/a` and the reason in one line. A light plan uses item 1 only.

## Contents

- 1. Safe defaults
- 2. Contract changes
- 3. Quality scenarios
- 4. Reliability targets
- 5. Threat model
- 6. Views
- 7. Watching it in production
- 8. Accessibility and personal data
- 9. Experiments
- 10. What is missing
- 11. Edge cases
- 12. Leverage points
- 13. Margins
- 14. Room to grow

## 1. Safe defaults

Applies when the design adds a config value, flag, or public parameter.

- State what `0`, empty, and `None` mean. None of them may mean "off" or "forever".
- State the default, and that it is the safe value.
- When the design adds a route, task, or consumer, name where its auth check lives.

## 2. Contract changes

Applies when a public API, event, schema, config key, or file format changes.

- Mark each change `breaking` or `compatible`.
- A breaking change is split into three steps that ship separately: add the new form, move every caller, remove the old form.
- Say how old and new versions run side by side during rollout.

## 3. Quality scenarios

Applies when the change affects speed, reliability, security, or cost for users or operators.

Write each need as one measurable scenario, not an adjective:

| Part | Example |
| --- | --- |
| Trigger | 500 checkout requests per second arrive |
| Conditions | normal operation, one database replica down |
| Expected response | every request is answered |
| Measure | 95% within 300 ms; none lost |

Each scenario becomes an acceptance criterion or an automated check.

## 4. Reliability targets

Applies when the change touches a live service or stored data.

- The signal that shows the service is healthy (for example, the share of successful requests) and its target. Never 100%.
- What happens when the target is missed (for example, new releases pause).
- For stored data: how long recovery may take, and how much recent data may be lost. Neither may be "zero" without a tested mechanism.

## 5. Threat model

Applies when the change crosses a trust boundary: new input from users or other systems, auth, secrets, personal data, or a new outbound call.

Answer four questions:

1. What are we working on? One data-flow diagram (its form comes from the visual ladder in core section 2.4) with the trust boundaries marked.
2. What can go wrong? Walk each boundary for: pretending to be someone else, tampering with data, denying an action, leaking information, denying service, and gaining privileges.
3. What will we do about it? One mitigation per threat, or an accepted risk with M's name on it.
4. Did we do a good enough job? Each mitigation becomes a test in Parts.

## 6. Views

Applies when more than one component changes or a new component is added.

- **Context:** the system as one box, with the people and outside systems it talks to.
- **Containers:** the deployable pieces inside it (services, databases, queues) and how they talk.

Both are diagrams with the changed pieces marked `changed` or `new`. Skip both for a short two-step change that a sentence explains.

## 7. Watching it in production

Applies when the change runs in a live system.

- The log line, metric, or trace that shows the new behavior working.
- The signal that shows it failing, and who sees it.
- The number that triggers a rollback.

## 8. Accessibility and personal data

Applies when the change touches a user interface or personal data. For a user interface, also state how each action is signposted, what feedback follows it, which actions can be undone, and what stops steps from happening in the wrong order.

- User interface: the accessibility level targeted (for example WCAG 2.2 level AA) and how it is tested.
- User interface: name the files under `references/design/` used for visual craft, screen states, and interface copy (the plan playbook says when to read `references/design.md`). This checklist itself covers system design only.
- Personal data: what is collected, why, how long it is kept, and who can read it. Collect only what the purpose needs.

## 9. Experiments

Applies when the change ships as an A/B test or staged experiment.

- The hypothesis, written so a result could prove it wrong.
- The primary metric and at least one guardrail metric.
- The sample size and the stopping rule, fixed before launch. Do not stop early at the first good-looking result.
- A check that the split between groups matches the configured split before reading results.

## 10. What is missing

Applies to every full plan.

- List the skills, access, data, and dependencies the plan needs before it can work, and who provides each.

## 11. Edge cases

Applies when the change handles user input or external data.

- List each edge case with its impact and the effort to handle it.
- Consider handling it without code first (a limit, a message, a manual step).

## 12. Leverage points

A leverage point is a part many others depend on. Applies when the change touches an interface, a data model, or a stateful system.

- Walk the proposed shape through about six real callers or scenarios on paper before fixing it. If that needs running code, make it a labelled trial part in Parts.
- Exercise its failure modes, not only the normal path.

## 13. Margins

Applies when the design sets a limit, capacity, timeout, or estimate.

- State the expected value, the worst case, the margin chosen, and the assumption the margin rests on.

## 14. Room to grow

Applies when demand is uncertain.

- Prefer the design that can be extended later, and write the trigger that says when to extend it.
