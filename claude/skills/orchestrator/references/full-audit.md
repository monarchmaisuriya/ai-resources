# Full audit of unfamiliar code

Use when M asks to audit or understand a codebase or module, not a diff. For a security audit or vulnerability hunt, use `references/security-audit/WORKFLOW.md` instead of this whole file. This step builds understanding before anyone looks for bugs. Ideas adapted from Trail of Bits Audit Context Building (see Reference basis in `references/review.md`).

## Rules for this step

- Record structure, not verdicts: no vulnerability names, severities, or fixes yet. An assumption nothing enforces is written as `nothing found` and handed to the review passes.
- Every claim cites `file:line`, or becomes an open question: "unclear; need to inspect X". No "probably" or "seems to".
- Never infer behavior from a name.
- When new evidence contradicts an earlier note, correct it in place and say what changed.
- Depth follows the code: branches, external calls, and state changes get attention; a line that copies a value gets a line.

## Where the output goes

`docs/YYYY-MM-DD-HHMM_<topic>_audit/` in the touched repo: `MAP.md`, plus `functions/NN-<function>.md` per analyzed function.

## Step 1: Orient (main session)

Read broadly and shallowly, then write `MAP.md` with:

- **Pieces:** modules and what each is for.
- **Ways in:** routes, tasks, queue consumers, commands, and who reaches each.
- **Actors and trust:** each actor marked untrusted, semi-trusted, trusted, or unclear.
- **Data that survives between calls:** rows, cache keys, queues, files, and which functions write each.
- **Where auth lives:** middleware, decorators, route registration, and which routes on each path get it.
- **Config that sets a bound or a switch:** each module-level setting that limits or turns off a check, with its `file:line`, its default, and the functions that read it. It is covered in the write-up of the first function that reads it.
- **Ranked functions:** the ones most of the system's correctness rests on: they validate untrusted input, enforce a bound or permission, change shared state, or sit between an untrusted actor and that state. Skip getters, setters, and pass-throughs. Default 12, at most 40. List the ones cut.
- **Coverage:** one row per way in, with columns `way in | trust boundary | functions that cover it | status`. Status is `covered` (a function record traces it), `blocked: <exact missing fact>`, `not reviewed: <reason>`, or `out of scope`. A row is `covered` only when a function record names its paths; "auth reviewed" is not coverage. This table is the coverage claim; nothing else counts.

Show M the map and the ranked list. Start step 2 only after M says yes, because it starts one subagent per function.

## Step 2: One subagent per function

Each subagent writes its full write-up to its file, then returns only a short record:

- **Invariants:** what must hold, each with a line.
- **Assumptions:** each with the line that establishes it, or `nothing found`.
- **Calls:** each callee, and what this function needs it to establish.
- **Open questions.**

The write-up uses these sections: Purpose; Inputs and trust; Outputs and effects; Block by block (what it does, why it sits here, what it assumes, what it establishes, what later code depends on it); Dependencies on other functions; Open questions. Leave a section out only when it is truly empty, and say so.

Follow every call, through every path, not only the successful one. Code whose source is not available is hostile: record what is sent and which outcomes are not ruled out.

## Step 3: Pull it together (main session)

Add to `MAP.md`:

- How each piece of state is read and written across functions, and the rule that implies.
- The end-to-end flows.
- Which actor can reach which way in, and what that reaches.
- Where complexity clusters.
- Every `nothing found` in the function files (search them for the phrase; the records can drop some), copied word for word, as unenforced assumptions.
- Every open question, carried forward.
- Where two records disagree: quote both; do not pick one.

Then run one **coverage critic**: a fresh `general-purpose` subagent that gets only `MAP.md` and the function files, reads the source, writes nothing, and returns:

- ways in that have no Coverage row;
- sibling paths to the same state (list, bulk, export, import, search, background task, admin, legacy, retry, migration) that no record traced;
- cut functions that guard shared state or a permission;
- rows marked `covered` whose record does not name the paths.

Add each accepted gap as a Coverage row and analyze it with Step 2, then run the critic once more. Stop when a critic returns nothing, or when M says stop; in that case mark the open rows `not reviewed` with the reason. State the critic's last result in `MAP.md`. Unless the last critic returned nothing, the audit is a partial pass and says so. One pass never exhausts a codebase: Cloudflare measured that a single run found about half of what repeated runs found (`references/security-audit/README.md`, Design principles).

Then open `references/rubric-code.md` and run passes 3, 6, and 7 on the ranked functions, starting with the `nothing found` list.
