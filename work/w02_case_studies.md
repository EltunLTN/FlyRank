# W02 — Portfolio Case Studies

## Voice card

direct, calm, plain, specific, honest, no buzzwords

## Portfolio audience and action

**Audience:** a hiring manager or ML lead looking for a junior machine-learning engineer who can work with messy real data and explain the limits of the result.

**One action:** contact me to discuss the work.

---

# Case 1 — Content Opportunity Scoring

## The problem

A content team has more pages to review than it can realistically handle each week. The useful question is not simply which pages look old. It is which pages should be reviewed first, and what signal justifies putting them near the top of the queue.

I chose the content opportunity scoring lane because the starter dataset already contained a baseline score, reason codes, and a model comparison. That gave me a real decision to investigate instead of starting with an abstract ML problem.

The starter data contains 30,000 content pages and 44 columns. The existing baseline had Precision@50 of 0.240, while the supplied random-forest comparison reached 0.740 on the same starter data. I treated that gap as a reason to investigate the problem further, not as proof that a production model would automatically improve content performance.

## What I did and decided

I defined the decision as: given limited refresh capacity, which pages should be reviewed first?

The unit of analysis is one content page. A content manager could take the top N pages from a ranked queue and decide whether to refresh, expand, protect, prune, or monitor them.

I also separated the cost of the two main mistakes. A false positive can waste writer time on a page that did not need attention. A false negative can leave a genuinely declining page unnoticed. That made a ranked, decision-oriented approach more useful than simply reporting a generic classification accuracy.

I decided to keep the work in decision-support language. The model can identify patterns associated with past decline in the available data; it cannot prove that refreshing a page will cause recovery.

## What came of it

The lane became a concrete ML problem with a measurable decision metric: Precision@50 for the review queue. The starter comparison also gave me a useful benchmark: 0.240 for the supplied rule baseline versus 0.740 for the supplied random-forest comparison.

The next step is to test the framing more honestly with a future-looking target, leakage checks, and client-aware validation rather than treating the starter proxy as a final production label.

**What I would do differently next time:** define the future outcome and validation design before comparing more models, so the model is judged against the decision it is supposed to support rather than against a convenient current-window label.

---

# Case 2 — Framing Content Decline as an ML Task

## The problem

Once I chose the content opportunity lane, I needed to make the question precise enough to model. "Find pages that need improvement" is too vague. I needed to define what the model predicts, how success is measured, and what someone actually does with the output.

## What I did and decided

I framed the task as binary classification: declining versus not declining.

The intended model output is a probability of decline. I would use that probability as a risk score and rank pages so an editor can review the highest-risk pages first.

For the starter exercise, the target is `is_declining_label`. I checked where that label comes from instead of treating it as ground truth automatically. It is derived from `trend_direction`, which itself comes from `trend_pct`. That means it is a rule-derived teaching proxy, not an independent future outcome.

That distinction changed how I describe the task. I would not use `trend_pct` or `trend_direction` as model features because they define the target and would create leakage.

I chose Precision@K as the main decision metric. If editors can only review K pages, the useful question is how many of those prioritized pages are actually declining. This connects the model directly to the limited review capacity instead of optimizing a metric that is disconnected from the action.

## What came of it

I ended up with a clear chain from model to action:

**historical page signals → predicted decline probability → ranked review queue → human editorial decision**

The framing also made the limitation explicit: the starter target is not the final future-looking label I would want in a production system.

There is no claim here that the model will cause traffic recovery or predict a search engine's decisions. The output is a decision-support signal for prioritizing human review.

**What I would do differently next time:** replace the starter proxy with a genuine future-window outcome and validate it before treating model performance as evidence for a production workflow.

---

# How the cases fit together

The two pieces tell one story rather than presenting unrelated projects.

1. **Content Opportunity Scoring** establishes the real decision: which pages deserve limited editorial attention first.
2. **ML Task Framing** turns that decision into a precise prediction problem with an explicit target, metric, unit of analysis, and limitation.

Together they show how I move from a messy business question to a testable ML problem without hiding uncertainty behind model terminology.

---

# Bio

I build machine-learning systems around real data and practical decisions. I care about making the model useful, checking what the data actually supports, and being clear about where the result stops.

# Contact / CTA

If you are working on an ML problem with messy data and a real decision behind it, contact me to discuss the work.

---

# Before / After

## Generic AI line

> I am a results-driven machine-learning enthusiast who leverages advanced AI and data-driven solutions to transform complex challenges into actionable insights.

## My edited version

> I build machine-learning systems around real data and practical decisions. I care about making the model useful, checking what the data actually supports, and being clear about where the result stops.

## Why I changed it

The first version could describe almost any junior AI portfolio. It uses broad claims without showing what I actually do. The edited version is narrower: real data, practical decisions, useful models, and honest limits. Those are the choices I can support with the work in this repository.

---

# Evidence and limits

- The content opportunity lane and its decision framing are documented in `work/notebooks/w01_research_question.ipynb`.
- The ML task framing is documented in `work/notebooks/w02_ml_task_framing.ipynb`.
- The starter dataset contains 30,000 pages and 44 columns.
- The supplied starter comparison reports Precision@50 = 0.240 for the baseline and 0.740 for the random-forest comparison.
- These figures describe the starter comparison; they are not presented as a production result or causal effect.
- The current-window decline label is treated as a proxy. A stronger version should use a future-window observed outcome and an honest leakage-safe validation design.
