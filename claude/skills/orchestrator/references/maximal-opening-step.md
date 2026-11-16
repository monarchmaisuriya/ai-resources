# Maximal opening step

Professional-grade task discovery and scope validation. `references/modes.md` rule 6 says when this file applies: maximal session mode only, in the Research, Interrogate, and Plan lanes. M is the person you are working for.

Before beginning the task, proposing a solution, or proceeding to the playbook's own opening question or first step, conduct a maximal professional-grade assessment of what the task should actually involve.

Do not merely accept the task as M initially describes it. Determine what the task requires to be done properly, at the highest relevant professional standard, and identify any critical work, evidence, investigation, or verification that M has not explicitly requested but that a genuinely exceptional professional would consider necessary.

The objective is not to enlarge the assignment, introduce unnecessary complexity, or create additional work for its own sake. It is to identify the deepest, most consequential version of the task that remains faithful to M's underlying objective.

### 1. Identify the professional standard that should govern the work

Establish what an exceptional, highly experienced specialist in the relevant field would recognize as the correct way to approach this particular task.

Identify:

- What a top-tier professional would investigate, establish, or do before proceeding that M has not explicitly requested.
- What important considerations, hidden dependencies, assumptions, risks, edge cases, or failure modes might be overlooked by someone who simply follows the stated instructions.
- What distinctions an expert would make that a less experienced practitioner might miss.
- What would distinguish a genuinely rigorous, defensible result from an answer that merely appears plausible, polished, or complete.
- Whether the task requires diagnosis before execution, investigation before recommendation, measurement before judgment, or verification before acceptance.

Be concrete and task-specific. Do not provide generic statements about best practices.

### 2. Identify the rawest and most authoritative starting material

Determine what primary evidence an expert would actually begin with, rather than relying on M's description, an AI-generated summary, secondary commentary, or another person's interpretation.

Identify the most authoritative and unprocessed materials available or required, as appropriate to the field. These might include:

- Original source code, repository history, commits, configuration files, dependency manifests, and execution traces.
- Raw logs, error reports, telemetry, database records, and system outputs.
- Original specifications, contracts, standards, requirements, and technical documentation.
- Primary research, original datasets, experimental results, and source publications.
- Direct measurements, physical observations, recordings, photographs, and inspection results.
- Original financial records, transaction data, calculations, and underlying assumptions.
- Firsthand statements, contemporaneous records, and independently documented evidence.

For each proposed source, explain:

1. Exactly what material should be examined.
2. Why it is more authoritative than a summary or interpretation.
3. What it could reveal that the available description might conceal.
4. Whether it is actually available, must be obtained, or cannot currently be accessed.

Do not pretend to have inspected source material that has not been provided or accessed. Clearly distinguish available evidence, missing evidence, and evidence that would materially improve the investigation.

### 3. Specify the professional methods, analytical techniques, and tools

Identify the exact methods an outstanding practitioner would use to investigate, execute, and assess the task.

Where relevant, specify:

- The analytical framework, diagnostic procedure, or investigative methodology.
- The appropriate technical tools, software, instruments, testing environments, or reference materials.
- The sequence in which investigations and operations should occur.
- The measurements, comparisons, calculations, experiments, or tests required.
- How competing explanations, hypotheses, or interpretations should be evaluated.
- How uncertainty, conflicting evidence, and limitations should be handled.
- Which methods are essential and which are optional improvements.

Explain not only what tools or methods should be used, but why they are appropriate and what evidence they are expected to produce.

Avoid tool-name dropping. Every proposed method must have a clear purpose connected to the task.

### 4. Establish independent criteria for determining whether the result is correct

Before doing the work, identify what would demonstrate that the eventual result is actually correct, rather than merely convincing because the agent produced it.

Establish objective acceptance criteria and, wherever possible, independent verification mechanisms.

Consider:

- What test, measurement, experiment, or observable outcome would establish correctness?
- What independent source, authoritative reference, or external standard could corroborate the result?
- What would falsify the proposed conclusion or reveal that the solution is incomplete?
- How could another competent professional independently reproduce or verify the findings?
- What measurable conditions must be satisfied before the work can be considered complete?
- What residual uncertainty or limitations must be disclosed?

Prioritize observable evidence over self-assessment. The agent's confidence, explanation, or assertion that the work is complete must never constitute the sole proof of correctness.

Where independent verification is impossible, explain why and establish the strongest available alternative.

### 5. Identify the most consequential gap between the requested task and the task that should actually be performed

Compare M's stated request against the professional standard established above.

Identify the specific difference between:

- What M explicitly asked for.
- What an ordinary execution of the request would produce.
- What a top professional would actually investigate or deliver to achieve the underlying objective reliably.

Prioritize only omissions that materially affect accuracy, completeness, reliability, safety, defensibility, or practical usefulness.

Do not manufacture problems, inflate the scope, or add sophisticated procedures that offer no meaningful improvement.

### 6. Formulate the improved task as an explicit proposed assignment

Using the findings from the preceding assessment, rewrite the original request into a precise, professional-grade task specification.

The revised assignment must clearly establish:

1. **Objective:** What outcome is ultimately sought and why.
2. **Scope:** What must actually be investigated, produced, or accomplished.
3. **Starting evidence:** Which original materials and primary sources should be examined.
4. **Methodology:** Which specific professional methods and tools should be employed.
5. **Critical considerations:** Which assumptions, dependencies, risks, and potential failure modes must be addressed.
6. **Deliverables:** What concrete outputs M should receive.
7. **Verification:** How the outputs and conclusions will be independently tested or validated.
8. **Acceptance criteria:** What observable conditions must be satisfied for the assignment to be considered complete.
9. **Limitations:** What cannot be established, what evidence is unavailable, and what uncertainty may remain.

The revised assignment should be more precise, more evidence-driven, and more professionally rigorous than the original, not simply longer or broader.

### 7. Present the proposal and stop for explicit confirmation

Present M with:

- **What an exceptional professional would do:** The most important additional investigation or action.
- **What they would start from:** The raw primary evidence and materials.
- **How they would approach it:** The specific methodology, tools, and sequence.
- **How correctness would be established:** The independent tests, measurements, standards, or corroborating sources.
- **What the original request is missing:** The consequential gaps, if any.
- **The proposed improved assignment:** A complete, rewritten task specification.

Then ask M whether this is the task they actually want performed, or whether they would like to modify its scope, methodology, evidence requirements, deliverables, or verification standards.

**STOP HERE.** Do not begin execution, initiate the playbook's own opener, ask its first question, or proceed to its first operational step until M explicitly confirms or modifies the proposed assignment.

If M changes the assignment, revise the proposed specification accordingly and obtain confirmation of the revised version before proceeding.

If M confirms it, treat the confirmed specification as the operative task definition and proceed with the playbook. From then on it is M's request: every later step that compares work with "M's question" or "M's request" compares with the confirmed specification. Write it into the plan, research brief, or interrogation brief the playbook produces, with the note that M confirmed it, so the file carries it after the conversation is summarized.

### Placement and precedence

This opening step is mandatory and occupies a specific position in the playbook's sequence:

1. First, complete any preparatory homework, research, inspection, or other preliminary work that the playbook explicitly requires before its first question. Read-only inspection needed to make steps 1 to 5 concrete (reading the code, the logs, the specification) is also allowed before the stop; nothing is written or changed.
2. Next, execute this entire maximal opening step, ending with the stop in step 7.
3. Only after confirmation, proceed to the playbook's own opener or first step.
4. Continue through the playbook using the confirmed, professionally strengthened task definition.

This step takes precedence over the playbook's own opening interaction, but does not override any preparatory homework that the playbook requires before that interaction.

It runs once per task. When one task moves through several lanes (Research or Interrogate, then Plan), the later lane reuses the confirmed specification and does not run this step again, unless M changes the task.

**Governing principle:** Never confuse faithfully executing the initial wording of a request with doing the work that the request genuinely deserves. Establish the right task, the right evidence, the right methodology, and the right standard of proof before beginning execution.
