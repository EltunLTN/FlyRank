# FL-01 — Prompt Iteration Log

## Task

Use a real task from my FlyRank ML audit: explain and frame the **Refresh / Content Opportunity Scoring** problem so that an ML lead can understand the decision, target, model output, and evaluation metric.

The underlying work uses a starter dataset of 30,000 content pages and 44 columns. The existing comparison reports Precision@50 of 0.240 for the baseline rules and 0.740 for the random-forest model. The ranked output is intended as a reviewer aid, not an automatic publishing decision.

## Goal of the experiment

I wanted to see how much prompt quality changes when I add one prompting technique at a time. I kept the underlying task constant and changed only one named technique between runs.

---

# Run 0 — Naive baseline

### Technique

None. This is the one-line prompt I would have used before learning prompt engineering.

### Prompt

> Explain this machine learning problem: we have website content pages and want to find which ones need improvement.

### Output

> This is a machine learning problem where you can use classification to predict whether a page needs improvement. You could collect features such as traffic, clicks, impressions, and engagement. Then train models such as logistic regression, decision trees, random forests, or neural networks. You can evaluate the model using accuracy, precision, recall, and F1 score.

### What changed and why

Nothing. This is the baseline.

### Observed output difference

There was no real decision in the answer. It sounded like a generic ML tutorial and suggested several algorithms and metrics without explaining which one fits the actual workflow.

### What still failed

It did not identify the user, business action, data, target, capacity constraint, or limitations.

### Next

Add a **role assignment** so the model answers from the perspective of someone responsible for ML problem framing.

---

# Run 1 — Role assignment

### Technique

**Role assignment**

### Prompt

> Act as an ML problem-framing specialist. Explain this machine learning problem: we have website content pages and want to find which ones need improvement.

### Output

> I would first turn the vague request into a decision problem rather than immediately choosing a model. The likely task is to identify pages that deserve attention and rank them by priority. A useful framing should define the unit, target, model output, and evaluation metric before selecting an algorithm.

### What changed and why

I added only a role: **ML problem-framing specialist**. I wanted the response to focus less on generic model selection and more on defining the problem correctly.

### Observed output difference

The answer became less like a list of algorithms and more like an ML planning response. It introduced the idea that the decision should be defined before the model.

### What still failed

It still did not know that the real task is a limited-capacity content review queue, so the answer remained abstract.

### Next

Add **context and motivation** so the model understands the actual FlyRank task and why the output matters.

---

# Run 2 — Context and motivation

### Technique

**Context and motivation**

### Prompt

> Act as an ML problem-framing specialist. Explain this machine learning problem: we have website content pages and want to find which ones need improvement.
>
> Context: this is a content refresh prioritization task. A content team has more pages to review than it can handle each week, so the goal is to rank pages for review rather than automatically decide what to publish. The starter dataset contains 30,000 pages and 44 columns. The current starter comparison reports Precision@50 of 0.240 for baseline rules and 0.740 for a random forest. Treat those numbers as starter-exercise evidence, not causal or production results.

### Output

> The decision is which pages should be reviewed first when editorial capacity is limited. The model should produce a page-level priority or decline-risk score that can be used to rank the review queue. Because only the top part of the queue can be inspected, Precision@50 is more directly connected to the workflow than a generic accuracy score.
>
> The model should support the editor rather than replace the editor. The 0.740 versus 0.240 comparison is useful as a benchmark on the starter data, but it does not show that refreshing a page will cause traffic recovery.

### What changed and why

I added only the project context and the reason the task exists.

### Observed output difference

This was a large improvement. The response now described the real decision, the limited review capacity, the human-in-the-loop workflow, and why Precision@50 matters.

### What still failed

The target was still underspecified. The answer did not know that the starter `is_declining_label` is derived from current trend information and therefore acts as a proxy.

### Next

Add **few-shot examples** showing the distinction between a weak framing and a good framing.

---

# Run 3 — Few-shot examples

### Technique

**Few-shot examples**

### Prompt

> Act as an ML problem-framing specialist. Explain this machine learning problem: we have website content pages and want to find which ones need improvement.
>
> Context: this is a content refresh prioritization task. A content team has more pages to review than it can handle each week, so the goal is to rank pages for review rather than automatically decide what to publish. The starter dataset contains 30,000 pages and 44 columns. The current starter comparison reports Precision@50 of 0.240 for baseline rules and 0.740 for a random forest. Treat those numbers as starter-exercise evidence, not causal or production results.
>
> Example 1 — weak framing:
> "Use classification to predict which pages need improvement and evaluate it with accuracy."
>
> Example 2 — strong framing:
> "When review capacity is limited, rank pages by decline risk so an editor can inspect the highest-priority pages first. Use a metric such as Precision@K that matches the number of pages the team can actually review."
>
> Use the stronger example's level of specificity.

### Output

> Frame the task as page-level decision support: rank existing content pages by the likelihood that they should receive review attention. The model output should be a probability or priority score, and an editor should make the final refresh, expansion, monitoring, or pruning decision.
>
> The evaluation should focus on the top K pages because the editorial team has finite review capacity. Precision@50 is therefore a useful starter metric when the operational queue is 50 pages.
>
> The current starter target needs care: if the decline label is derived from current trend fields, it should be treated as a proxy rather than a future outcome, and those defining fields should not be used as features.

### What changed and why

I added only two examples: one weak framing and one stronger framing.

### Observed output difference

The answer became more concrete and copied the useful behavior from the example: it separated the prediction from the human action and connected the metric to review capacity. It also surfaced the target-proxy issue more clearly.

### What still failed

The answer was useful, but the important elements were still spread across several paragraphs. A reviewer would have to extract the decision, target, output, and metric manually.

### Next

Add **output structure** to force the same information into predictable sections.

---

# Run 4 — Output structure

### Technique

**Output structure**

### Prompt

> Act as an ML problem-framing specialist. Explain this machine learning problem: we have website content pages and want to find which ones need improvement.
>
> Context: this is a content refresh prioritization task. A content team has more pages to review than it can handle each week, so the goal is to rank pages for review rather than automatically decide what to publish. The starter dataset contains 30,000 pages and 44 columns. The current starter comparison reports Precision@50 of 0.240 for baseline rules and 0.740 for a random forest. Treat those numbers as starter-exercise evidence, not causal or production results.
>
> Example 1 — weak framing:
> "Use classification to predict which pages need improvement and evaluate it with accuracy."
>
> Example 2 — strong framing:
> "When review capacity is limited, rank pages by decline risk so an editor can inspect the highest-priority pages first. Use a metric such as Precision@K that matches the number of pages the team can actually review."
>
> Use the stronger example's level of specificity.
>
> Return exactly five sections:
> 1. Decision
> 2. Unit
> 3. Target
> 4. Model output
> 5. Metric and limitation
>
> Keep each section to 1–2 sentences.

### Output

> **Decision**  
> Which content pages should be reviewed first when editorial capacity is limited?
>
> **Unit**  
> One content page.
>
> **Target**  
> The starter target is `is_declining_label`, but it is derived from current trend information, so it is a teaching proxy rather than an independent future outcome.
>
> **Model output**  
> A predicted decline probability used as a ranking score for the review queue. The score supports human review; it does not automatically decide the editorial action.
>
> **Metric and limitation**  
> Precision@50 matches a top-50 review capacity. The 0.740 versus 0.240 starter comparison is a benchmark, not proof of causal or production impact, and target-defining trend variables should not be used as features.

### What changed and why

I added only a required five-section response structure and a sentence limit.

### Observed output difference

The output became much easier to audit. Every important part of the ML framing appeared in a fixed place, and the distinction between model output and human action was explicit.

### What still failed

The structure improved clarity, but it did not make the reasoning itself more reliable. A well-formatted answer can still contain a weak target or skip a necessary validation step.

### Next

Add **step decomposition** so the model works through the framing in a fixed sequence before presenting the answer.

---

# Run 5 — Step decomposition

### Technique

**Step decomposition**

### Prompt

> Act as an ML problem-framing specialist. Explain this machine learning problem: we have website content pages and want to find which ones need improvement.
>
> Context: this is a content refresh prioritization task. A content team has more pages to review than it can handle each week, so the goal is to rank pages for review rather than automatically decide what to publish. The starter dataset contains 30,000 pages and 44 columns. The current starter comparison reports Precision@50 of 0.240 for baseline rules and 0.740 for a random forest. Treat those numbers as starter-exercise evidence, not causal or production results.
>
> Example 1 — weak framing:
> "Use classification to predict which pages need improvement and evaluate it with accuracy."
>
> Example 2 — strong framing:
> "When review capacity is limited, rank pages by decline risk so an editor can inspect the highest-priority pages first. Use a metric such as Precision@K that matches the number of pages the team can actually review."
>
> Use the stronger example's level of specificity.
>
> Work through these steps in order before writing the final answer:
> 1. State the real decision and human action.
> 2. Define the unit of analysis.
> 3. Define what the model should predict and check whether the starter target is a true future outcome or a proxy.
> 4. Define the model output and how it is used.
> 5. Choose the metric based on the actual review capacity.
> 6. State leakage risks and limits of the available evidence.
>
> Then return exactly five sections: Decision, Unit, Target, Model output, Metric and limitation. Keep each section to 1–2 sentences. Do not invent data, results, or causal effects.

### Output

> **Decision**  
> Which content pages should be reviewed first when editorial capacity is limited? The editor uses the ranked queue to decide whether to refresh, expand, protect, prune, or monitor a page.
>
> **Unit**  
> One content page (`content_hash_id`).
>
> **Target**  
> The starter `is_declining_label` is derived from current trend information, so it is a rule-derived teaching proxy rather than a genuine future-window outcome. A stronger production target would measure an observed future decline or recovery window.
>
> **Model output**  
> A predicted probability of decline used to rank pages by review priority. The model provides decision support; the editor makes the final content action.
>
> **Metric and limitation**  
> Precision@50 fits a top-50 review queue; the starter comparison is 0.740 for the random forest versus 0.240 for baseline rules. These are starter-data benchmark results, not causal or production evidence, and `trend_pct` / `trend_direction` should not be used as features because they define the proxy target.

### What changed and why

I added only **step decomposition**: a fixed sequence for reasoning through the task before producing the final structured answer.

### Observed output difference

The final answer was the most complete and internally consistent. It connected the decision to the human action, explicitly checked target quality, identified leakage, and tied the metric to the actual queue size.

### What still failed

Step decomposition did not solve the underlying data limitation. The starter label is still a proxy, so no prompting technique can turn it into a genuine future outcome without new data or a different target construction.

### Next

Use the final prompt as the reusable template.

---

# Cross-model comparison — final prompt

## ChatGPT

The final prompt produces a structured, technical answer that is strong on explicit task framing. It tends to surface the target-proxy and leakage issue because those checks are stated directly in the prompt. The result is concise enough for an ML review while still preserving the decision, unit, target, output, metric, and limitation.

## Claude

I did not have access to a Claude execution environment in this session, so I am **not fabricating a Claude output**. The final prompt is designed to be directly runnable in Claude, but a genuine cross-model comparison requires pasting the actual Claude response here.

## Honest comparison

| Dimension | ChatGPT run | Claude run |
|---|---|---|
| Tone | Technical and direct; focuses on decision framing | Not executed in this environment |
| Accuracy | Preserves the known dataset facts and avoids causal claims | Not executed in this environment |
| Structure | Follows the five requested sections closely | Not executed in this environment |
| Failure points | Cannot repair the proxy target without better data | Not measured |

The important limitation is methodological: I can compare the ChatGPT output observed here, but I cannot honestly claim how Claude behaved without running the same final prompt there. That missing run should be completed before treating this section as a true two-model experiment.

---

# Final reusable prompt template

```text
Act as an ML problem-framing specialist.

Task:
[Describe the real ML task in one sentence.]

Context and motivation:
[Explain who needs the result, what decision they need to make, what capacity/cost constraint exists, and why the task matters.]

Data context:
[Describe the dataset, unit of analysis, important fields, known target-definition issues, and any measured benchmark results.]

Few-shot examples:
Weak example:
[Insert a common but vague answer.]

Strong example:
[Insert a concrete answer that correctly connects prediction to action and evaluation.]

Use the stronger example's level of specificity.

Work through these steps in order before writing the final answer:
1. State the real decision and human action.
2. Define the unit of analysis.
3. Define what the model should predict and check whether the target is an observed future outcome or a proxy.
4. Define the model output and how a human uses it.
5. Choose an evaluation metric that matches the actual operational capacity.
6. Identify leakage risks and state what the available evidence does and does not support.

Return exactly five sections:
1. Decision
2. Unit
3. Target
4. Model output
5. Metric and limitation

Keep each section to 1–2 sentences.
Do not invent data, results, business impact, or causal effects.
```

## Why the final template is reusable

A stranger can replace the bracketed fields with their own ML task and still get a useful framing. The template does not depend on the FlyRank dataset, page IDs, or the specific content-refresh problem. It preserves the techniques that produced the strongest output: role assignment, context and motivation, few-shot examples, output structure, and step decomposition.

---

# What I learned

The biggest change was not simply making the prompt longer. The output improved when the prompt supplied the model with the missing information and gave it a clear way to reason about the decision. Context made the answer specific; examples demonstrated the desired level of specificity; structure made it auditable; and step decomposition reduced omissions.

The experiment also exposed a hard boundary: prompt engineering cannot manufacture missing evidence. In this task, the current decline label is still a proxy, so the next real improvement is a better future-looking target, not another layer of wording.
