# The ten principles

Every piece of work, whatever its type, is written and reviewed against these ten: an idea, a research brief, a plan, an architecture decision, code, a test, or a review.

| # | Principle | The question |
| --- | --- | --- |
| 1 | Start from the need | Does it say who this is for, what they are trying to do, and how we will know it worked? |
| 2 | Stands on its own | Could someone who never saw the conversation understand it and act on it? |
| 3 | Evidence over confidence | Is every important claim checked, and labelled verified, disproved, inferred, or unverified? |
| 4 | Name the tradeoff | Does each choice show the options considered, what was given up, and who decided? |
| 5 | Count the cost | Does it state the effort, the risk, and the blast radius, and is the process sized to them? |
| 6 | One home for everything | Does each fact have one owner, each part one job, and each edge a clear contract, with fixes made where the fact lives? |
| 7 | Simplest thing that works | Is this the fewest moving parts that meet a confirmed need? |
| 8 | Fail loudly, undo safely | Does it stop with a clear message when something is wrong, and is there a stated way back? |
| 9 | Provably done | Is "done" something a person can observe or a test can fail on? |
| 10 | Kept true | Does the record still match reality, and does it explain why? |

Principle 5 has three required parts. **Effort:** how much work, and the time budget it must fit. **Risk:** what could go wrong, how likely, how bad, and whether it can be undone. **Blast radius:** everything that reads, calls, or depends on what is changing. A cheap change that is easy to undo gets a light process; a costly or irreversible one gets the full one.

To satisfy principle 1 on a new problem, frame it with the 6W2H questions: What, Why, Who (and for whom), When, Where, Which, How, and How much.
