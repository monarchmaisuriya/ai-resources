# Research rubric

## Contents

- The criteria
- Classify the research before reviewing
- Evidence grading
- How to review research
- Research-specific blocking conditions
- How the ten principles map to these criteria

## The criteria

Research is decision-grade when its conclusion is traceable, reproducible, and no stronger than its evidence.

1. **Question and decision fit:** The research states the question, intended decision, audience, scope, constraints, and what is out of scope.
2. **Method:** It explains how sources or data were found, selected, compared, and bounded. The method is proportionate to the decision's risk.
3. **Source quality:** Primary and official sources anchor factual claims. Independent credible sources test vendor claims. Source authority, recency, relevance, and conflicts of interest are considered.
4. **Coverage and alternatives:** Plausible competing explanations or approaches are included. Search terms, selection rules, or constraints do not quietly exclude an inconvenient option.
5. **Claim traceability:** Every non-obvious factual claim has a citation that supports that exact claim. Quotes, measurements, and calculations can be traced to their origin.
6. **Evidence handling:** Facts, assumptions, interpretation, and opinion are distinct. Conflicting evidence and negative results are reported.
7. **Analysis quality:** Comparisons use consistent criteria. Calculations use valid inputs and units. Correlation is not presented as causation. Small or biased samples are not generalized.
8. **Codebase and system fit:** Technical recommendations account for current architecture, team constraints, operational environment, and existing prior art.
9. **Uncertainty and limits:** Confidence is stated and justified. Missing evidence, stale data, unresolved disagreement, and limits on generalization are explicit.
10. **Recommendation proportionality:** The recommendation follows from the evidence, addresses tradeoffs, and names conditions that would change it. Research may conclude that evidence is insufficient.
11. **Reproducibility:** Another reviewer can repeat the search, calculation, experiment, or comparison using the recorded sources and method.

Do not use source count as a substitute for quality. For any research brief, expect at least 5 credible sources with at least one primary. If fewer are available, require the brief to state that limitation.

## Classify the research before reviewing

Use the shared criteria above, then add the matching method checks:

| Research type | Required method checks |
| --- | --- |
| Documentation or fact lookup | Exact version, publication date, primary source, quoted support, and applicability to the current system. |
| Option comparison | Same decision drivers and constraints applied to every option; comparable evidence depth; status quo included when viable. |
| Vendor or product evaluation | Vendor claims separated from independent evidence; pricing and limits dated; lock-in, exit path, support, security, and operational ownership covered. |
| Benchmark or experiment | Environment, dataset, workload, baseline, units, repetitions, variance, warm-up, confounders, raw results, and reproducible commands recorded. |
| Incident or causal investigation | Timeline, symptom, direct evidence, at least two plausible theories with different predictions, discriminating tests, immediate/systemic/root cause, and counterfactual: the fix would have prevented recurrence. |
| Literature or standards review | Search locations and terms, dates, inclusion/exclusion rules, duplicate handling, source-quality assessment, extraction method, and synthesis method recorded. |
| Quantitative analysis | Data provenance, definitions, missing-data handling, transformations, denominator, uncertainty, sensitivity checks, and calculation reproducibility covered. |
| Qualitative analysis | Sampling, collection method, coding or interpretation method, researcher bias, contradictory cases, and limits on transferability covered. |

Do not apply a method check that does not fit the research type. Explain n/a verdicts.

## Evidence grading

Grade each claim that materially affects the recommendation:

- **strong:** Direct primary evidence or reproducible measurement closely matches the question and current context.
- **moderate:** Credible evidence supports the claim, but transfer, age, sample, or method limits confidence.
- **weak:** Indirect, single-source, vendor-controlled, anecdotal, stale, or non-reproducible evidence.
- **unsupported:** The cited evidence does not establish the claim, or no evidence is provided.
- **conflicted:** Credible sources disagree and the brief has not resolved why.

The overall confidence cannot exceed the weakest evidence supporting a necessary premise. Several weak sources repeating one origin remain one weak evidence chain.

## How to review research

1. Rewrite the research question in falsifiable or decision-relevant terms.
2. Identify every premise required for the conclusion.
3. Trace each premise to its original source or data.
4. Check whether the citation supports the exact wording, population, version, and time period claimed.
5. Search for disconfirming evidence, alternatives, known failure modes, and conflicts of interest.
6. Recompute sampled calculations and inspect raw data where available.
7. Test whether changing a weak assumption changes the recommendation.
8. Confirm that the conclusion answers the stated question and does not generalize beyond the evidence.

## Research-specific blocking conditions

- A recommendation depends on an unsupported, contradicted, or fabricated claim.
- Citations do not support the claims attached to them.
- Options were evaluated under materially different criteria.
- A benchmark cannot be interpreted because the workload, baseline, units, or environment is missing.
- A causal conclusion has no discriminating evidence against plausible alternatives.
- Material uncertainty is hidden or stated more confidently than the evidence allows.

## How the ten principles map to these criteria

Principle 1 is criterion 1; principle 2 is criteria 2 and 11; principle 3 is criteria 3, 5, and 6; principle 4 is criteria 4 and 10; principle 5 is checked under criteria 8 and 10 (what the recommendation costs here); principle 7 is criterion 2 (a method no bigger than the decision needs); principle 9 is criterion 11; principle 10 is criterion 9. Principles 6 and 8 do not apply to a research brief: verdict `n/a`.
