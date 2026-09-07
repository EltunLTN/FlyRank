# W02 — Prompt Ladder

## Track

Machine Learning / AI Engineering

## What this experiment tests

I wanted to improve a weak prompt for turning a machine-learning problem into a clear task definition. The goal was not to make the prompt longer for its own sake, but to change one ingredient at a time and check whether the output actually became more useful.

The example uses the content-decline problem from this portfolio: deciding which content pages should be reviewed first when editorial capacity is limited.

---

# Run 0 — Weak baseline

### Layer added

None. This is the genuinely weak prompt I would have written without thinking about audience, context, format, or evaluation.

### Prompt

> Explain this machine learning problem: we have website content pages and want to find which ones need improvement.

### Representative output

> This is a machine learning problem where you can use classification to predict whether a page needs improvement. You could collect features such as traffic, clicks, impressions, and engagement. Then train models such as logistic regression, decision trees, random forests, or neural networks. You can evaluate the model using accuracy, precision, recall, and F1 score.

### What changed in the prompt

Nothing beyond stating the broad topic.

### What improved in the output

It identified the general ML direction, but almost nothing was tied to the actual decision. The answer was generic enough to fit many website problems.

### What still failed

It did not define who uses the prediction, what action follows it, what "needs improvement" means, or how limited review capacity changes the metric.

### What I would try next

Add a clearer goal so the model has to answer a specific decision question rather than explain ML in general.

---

# Run 1 — Clearer goal

### Layer added

**Clearer goal** — only the goal was changed.

### Prompt

> Help me define an ML problem whose goal is to rank website content pages by which pages should be reviewed first for possible refresh, when the editorial team can only review a limited number of pages each week.

### Representative output

> The ML problem can be framed as ranking pages by their likelihood of needing review. The model should produce a score for each page, and the team can review the highest-scoring pages first. A ranking metric such as Precision@K may be more useful than overall accuracy because only the top K pages can be reviewed each week.

### What changed in the prompt

I replaced the vague request to "find pages that need improvement" with a specific decision: rank pages for limited weekly review capacity.

### What improved in the output

The output stopped treating the task as a generic classification exercise and connected the prediction to a ranked review queue and a capacity limit. Precision@K appeared naturally because the goal is to get the top of the queue right.

### What still failed

It still assumed an unspecified reader and did not know the actual data or the distinction between a proxy label and a future outcome.

### What I would try next

Define the audience so the explanation is written for the person who will use the ML framing.

---

# Run 2 — Defined audience

### Layer added

**Defined audience** — only the intended reader was added.

### Prompt

> Help me define an ML problem whose goal is to rank website content pages by which pages should be reviewed first for possible refresh, when the editorial team can only review a limited number of pages each week. Write the explanation for an ML lead reviewing the problem definition.

### Representative output

> For an ML lead, the problem is best expressed as a decision-support ranking task. Each page receives a risk or priority score, and pages are sorted so the editorial team can inspect the highest-priority pages first. Precision@K is appropriate when the review team has a fixed capacity because it measures how many of the selected top-K pages are relevant.

> The framing should also separate the prediction target from the business action: the model prioritizes pages; an editor makes the final refresh decision.

### What changed in the prompt

I added one audience: an ML lead reviewing the problem definition.

### What improved in the output

The answer became more technical and decision-oriented. It clearly separated the model's role from the editor's role instead of implying that the model itself decides which content to change.

### What still failed

It still had no knowledge of the actual starter dataset, so it could not discuss the available columns, the current proxy label, or leakage risks.

### What I would try next

Add the real project context.

---

# Run 3 — Real context

### Layer added

**Real context** — only the project facts were added.

### Prompt

> Help me define an ML problem whose goal is to rank website content pages by which pages should be reviewed first for possible refresh, when the editorial team can only review a limited number of pages each week. Write the explanation for an ML lead reviewing the problem definition.
>
> Context: the starter dataset contains 30,000 content pages and 44 columns. The starter exercise includes `trend_pct` and `trend_direction`, and `is_declining_label` is derived from that trend information. The supplied random-forest comparison reports Precision@50 of 0.740 versus 0.240 for the starter baseline. Treat these as starter-exercise evidence, not production or causal results.

### Representative output

> Frame the task as predicting a page-level decline risk and using that score to rank the 30,000 pages for editorial review. The unit is one content page. Because the team can review only a limited number of pages, Precision@50 is a useful decision metric.
>
> There is an important target-definition issue: if `is_declining_label` is derived from `trend_direction`, which is derived from `trend_pct`, then those variables should not be used as features because they define the target and would leak target information. The current label should therefore be described as a teaching proxy rather than an independent future outcome.
>
> The 0.740 versus 0.240 Precision@50 comparison is useful as a starter benchmark, but it should not be described as proof of production impact or causal improvement.

### What changed in the prompt

I added only the actual dataset and project context.

### What improved in the output

This was the biggest improvement so far. The answer stopped being generic and identified a concrete leakage issue, the page-level unit, the capacity-driven metric, and the correct interpretation of the starter scores.

### What still failed

The information was useful but dense. A reviewer would still need to extract the target, output, metric, and limitation from prose.

### What I would try next

Specify a compact output format.

---

# Run 4 — Output format

### Layer added

**Specified output format** — only the response structure was changed.

### Prompt

> Help me define an ML problem whose goal is to rank website content pages by which pages should be reviewed first for possible refresh, when the editorial team can only review a limited number of pages each week. Write the explanation for an ML lead reviewing the problem definition.
>
> Context: the starter dataset contains 30,000 content pages and 44 columns. The starter exercise includes `trend_pct` and `trend_direction`, and `is_declining_label` is derived from that trend information. The supplied random-forest comparison reports Precision@50 of 0.740 versus 0.240 for the starter baseline. Treat these as starter-exercise evidence, not production or causal results.
>
> Return exactly these five sections: **Decision, Unit, Target, Model Output, Metric & Limitation**. Keep each section to 2–3 sentences.

### Representative output

> **Decision**  
> Which content pages should be reviewed first when editorial capacity is limited?
>
> **Unit**  
> One content page.
>
> **Target**  
> The starter target is `is_declining_label`, derived from current trend information. It is a proxy for the exercise, not an independent future outcome.
>
> **Model Output**  
> A probability or risk score for page decline, used to rank pages from highest to lowest review priority. The model supports prioritization; an editor makes the final action decision.
>
> **Metric & Limitation**  
> Precision@50 matches a fixed top-50 review capacity. The reported 0.740 versus 0.240 starter comparison is not evidence of causal or production impact, and `trend_pct`/`trend_direction` should not be used as features because they define the proxy target.

### What changed in the prompt

I added only a required output format with five named sections and a sentence limit.

### What improved in the output

The important decisions became much easier to scan. The target, model output, metric, and limitation were no longer buried in paragraphs.

### What still failed

The format made the answer concise, but it did not tell the model how to judge whether the framing was actually good. A concise answer could still contain a weak target or unsupported claim.

### What I would try next

Add explicit quality criteria.

---

# Run 5 — Quality criteria

### Layer added

**Quality criteria** — only evaluation criteria were added.

### Prompt

> Help me define an ML problem whose goal is to rank website content pages by which pages should be reviewed first for possible refresh, when the editorial team can only review a limited number of pages each week. Write the explanation for an ML lead reviewing the problem definition.
>
> Context: the starter dataset contains 30,000 content pages and 44 columns. The starter exercise includes `trend_pct` and `trend_direction`, and `is_declining_label` is derived from that trend information. The supplied random-forest comparison reports Precision@50 of 0.740 versus 0.240 for the starter baseline. Treat these as starter-exercise evidence, not production or causal results.
>
> Return exactly these five sections: **Decision, Unit, Target, Model Output, Metric & Limitation**. Keep each section to 2–3 sentences.
>
> Quality criteria: the decision must lead to a real action; the unit must be one clear entity; the target must be observable and must not be confused with the action; the metric must match limited review capacity; identify leakage risks; and do not claim causality or production impact from the starter comparison.

### Representative output

> **Decision**  
> Which content pages should be reviewed first when editorial capacity is limited?
>
> **Unit**  
> One content page (`content_hash_id`).
>
> **Target**  
> The starter target is `is_declining_label`, but it is derived from `trend_direction`/`trend_pct`, so it is a rule-derived teaching proxy rather than a future observed outcome. A production-ready version should replace it with a future-window outcome.
>
> **Model Output**  
> A predicted probability of decline used as a ranking score. The score prioritizes pages for human review; it does not automatically decide whether a page should be refreshed.
>
> **Metric & Limitation**  
> Precision@50 is appropriate when the team can inspect 50 pages because it measures the fraction of the selected top 50 that are actually positive under the chosen label. The 0.740 versus 0.240 starter comparison is a benchmark, not proof of causal or production impact; `trend_pct` and `trend_direction` must be excluded from features because they define the proxy target.

### What changed in the prompt

I added only explicit quality criteria for a good ML framing.

### What improved in the output

The output became more disciplined: it distinguished the prediction from the editorial action, identified the proxy-target problem, stated the future-window improvement, and tied Precision@50 to the actual review capacity.

### What still failed

**This did not magically solve the target problem.** The prompt can force the model to point out that the current label is weak, but it cannot create a genuine future outcome that is not present in the data. The final framing is therefore still a starter exercise, not a production-ready specification.

### What I would try next

With the ladder complete, I would keep the five layers that materially improved the answer and use the final prompt below as a reusable template. In a real project I would also replace the proxy target with an observed future-window outcome before trusting model performance.

---

# Final reusable prompt

```text
Help me define an ML problem for a real decision.

Decision context:
[Describe who has to decide what, and what capacity or cost limits the decision.]

Project context:
[Give the dataset size, unit of analysis, important fields, existing baseline/model results, and any known target-definition issues.]

Write the explanation for an ML lead reviewing the problem definition.

Return exactly these five sections:
1. Decision
2. Unit
3. Target
4. Model Output
5. Metric & Limitation

Keep each section to 2–3 sentences.

Quality criteria:
- The decision must lead to a real action.
- The unit must be one clear entity.
- Separate what the model predicts from what the human does with the prediction.
- Prefer an observed future outcome for the target; if the available target is a proxy, say so explicitly.
- Identify variables that would leak the target and explain why.
- Choose a metric that matches the actual decision and capacity constraint.
- Treat benchmark results as evidence from the stated evaluation setup, not as production or causal claims.
- Do not invent data, results, or business impact.
```

## Why this final prompt is reusable

A stranger can use it without knowing the original project because the prompt tells them what project context to provide, who the explanation is for, exactly how the answer should be structured, and what quality checks the answer must pass. It is also deliberately strict about leakage, proxy targets, unsupported claims, and invented results.

## Ladder summary

| Run | Single layer added | Main output improvement |
|---|---|---|
| 0 | None | Exposed how generic the original answer was |
| 1 | Clearer goal | Connected ML to a ranked review decision |
| 2 | Defined audience | Made the explanation more technical and decision-oriented |
| 3 | Real context | Added concrete data, leakage, proxy-target, and benchmark interpretation |
| 4 | Output format | Made the important framing elements easy to scan |
| 5 | Quality criteria | Forced explicit checks for leakage, target quality, action separation, and unsupported claims |

## Bottom line

The largest practical improvement came from adding real context, not from simply making the prompt longer. The later layers made the answer easier to evaluate and reuse, but they could not compensate for missing evidence in the underlying dataset. That is the main lesson from the ladder: prompt engineering can improve reasoning and presentation, but it cannot manufacture a better target or stronger evidence.
