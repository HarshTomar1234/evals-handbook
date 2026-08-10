# LLM Leaderboards — Fundamentals

> An **LLM leaderboard** is a public ranking or comparison table that shows how different
> LLMs perform on a common set of evaluations. Where notes 06–14 covered *individual*
> benchmarks in isolation, this note covers what happens once those benchmark scores get
> **aggregated, published, and compared side by side** — and why that step introduces its
> own, separate set of failure modes.

This continues from
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md) and
the [Benchmark Catalog](./14-benchmark-catalog-overview.md). It is followed by
[LLM Leaderboards — Platforms](./16-llm-leaderboards-platforms.md), which profiles the
real leaderboards named throughout this note.

---

## Table of Contents

1. [Why Leaderboards Exist](#1-why-leaderboards-exist)
2. [Who Needs Them, and for What](#2-who-needs-them-and-for-what)
3. [The Four Types of LLM Leaderboards](#3-the-four-types-of-llm-leaderboards)
4. [Why You Can't Always Trust LLM Leaderboards](#4-why-you-cant-always-trust-llm-leaderboards)
5. [How to Read LLM Leaderboards as an AI Engineer](#5-how-to-read-llm-leaderboards-as-an-ai-engineer)

---

## 1. Why Leaderboards Exist

A leaderboard's job is to answer, at a glance, a question that would otherwise require
running dozens of benchmarks yourself. Four concrete reasons they exist:

- **Compare models across labs with a common reference.** OpenAI, Anthropic, Google,
  Meta, and every open-weight team score models differently in their own launch posts. A
  leaderboard puts them on one shared table, under one protocol — which also **provides
  trust**: a number a lab did not compute itself is harder to dismiss as marketing.
- **Guide model selection when you don't have resources to run evals yourself.** Not every
  team can afford to run 40 benchmarks against 15 candidate models before picking one. A
  leaderboard does that work once, for everyone.
- **Detect saturation.** When every model at the top scores 95%+ on a benchmark, that's the
  visible signal (see [note 07, section 5](./07-model-evals-benchmarks-and-harnesses.md#5-why-benchmarks-can-be-misleading))
  that it's time for a new, harder benchmark — a pattern the [Benchmark Catalog](./14-benchmark-catalog-overview.md)
  tracks explicitly via its status labels.
- **Discover new models.** A leaderboard is often the first place a new model — especially
  from a small or unfamiliar lab — becomes visible at all.

## 2. Who Needs Them, and for What

The same leaderboard serves very different audiences, each reading it for a different
reason:

| Audience | What they use it for |
| --- | --- |
| **AI engineers** | Shortlisting — narrowing 100 candidate models down to the 5 they'll actually test themselves. |
| **Frontier labs** | Competitive positioning and release gating; leaderboard standing is simultaneously a marketing asset and an internal go/no-go signal (see [note 07, section 4.1](./07-model-evals-benchmarks-and-harnesses.md#41-frontier-labs-the-model-builders)). |
| **Researchers** | Measuring whether a new technique actually helps, against a shared baseline. |
| **Policymakers and safety institutes** | Tracking capability trends for forecasting and regulation. |
| **The open-weight ecosystem** | Discovery. A three-person lab with no marketing budget can appear next to a frontier model on the same table — genuinely one of the most underrated functions leaderboards serve. |

## 3. The Four Types of LLM Leaderboards

Leaderboards differ in *what they measure* and *how they measure it*. Four recurring
types:

### 3.1 Benchmark-Specific Leaderboards

Rank models using the results of **one particular benchmark** — for example, a leaderboard
comparing models only on MMLU, only on HumanEval, only on GSM8K, or only on GPQA.

**The question it answers:** *Which model performs best on this benchmark?*

Easy to understand, but a narrow view — a single-benchmark leaderboard inherits every
limitation of that one benchmark (see [note 07, section 5](./07-model-evals-benchmarks-and-harnesses.md#5-why-benchmarks-can-be-misleading)
and the per-benchmark status/contamination data in the [Benchmark Catalog](./14-benchmark-catalog-overview.md)).

### 3.2 Multi-Benchmark Leaderboards

Compare models across **multiple benchmarks and evaluation dimensions** at once —
knowledge, reasoning, mathematics, coding, instruction-following, data analysis, and
similar. Many also fold in **practical deployment factors**: cost per token or query,
response latency, output speed, context-window size, model availability, and
price-to-performance ratio. Results may be shown as separate per-dimension scores or
combined into one overall ranking.

**The question it answers:** *Which model provides the strongest overall combination of
capability, cost, and performance?*

### 3.3 Human-Preference-Based Leaderboards

Rank models using **human votes**. A user is shown two anonymous responses — one from
Model A, one from Model B — and picks which is better. After enough comparisons, models
are ranked by how often their responses are preferred.

**The question it answers:** *Which model's responses do users prefer?*

Useful for measuring qualities that are hard to grade automatically: helpfulness, clarity,
writing quality, creativity, tone, and conversational ability. The limitation: the
preferred answer is not always the most factually correct one — people sometimes prefer an
answer because it *sounds* confident, detailed, or polished, independent of whether it is
right (this is expanded in section 4.6 below).

### 3.4 Application-Specific Leaderboards

Evaluate models on **one particular real-world task or application** — fixing software
bugs, calling APIs and tools, generating SQL queries, answering medical questions,
completing agentic tasks, or working with long documents.

**The question it answers:** *Which model or AI system performs best for this specific
application?*

These sit closest to an [own eval set](./06-model-evals-capabilities.md#3-two-sources-of-tests-public-benchmark-vs-your-own-eval-set)
in spirit — narrower and more directly actionable than a general-capability board, but
still a third party's task definition, not yours.

---

## 4. Why You Can't Always Trust LLM Leaderboards

Seven distinct failure modes, each independent of the others — a leaderboard can be
completely honest about its methodology and still mislead you for any of these reasons.

### 4.1 Benchmark performance may not transfer to real applications

Benchmark questions are usually clean, well-defined, and self-contained. Real applications
involve ambiguous requests, missing information, company-specific data, multiple
conversation turns, tool failures, and unusual edge cases. A model that performs well on
academic benchmarks may still fail inside a customer-support bot, coding assistant, or RAG
system.

> Leaderboards are useful for **shortlisting** models, but they cannot replace evaluation
> on your own use case.

### 4.2 Benchmark contamination can inflate scores

Many benchmark questions are publicly available through papers, repositories, tutorials,
and websites, and may enter a model's training data. The model can then answer correctly
because it has already seen the question — not because it can independently solve it. A
high score may therefore reflect genuine capability, memorization, or mere familiarity
with similar questions, and there is often no way to tell which from the score alone. This
is one of the biggest threats to benchmark reliability — and exactly why every entry in the
[Benchmark Catalog](./14-benchmark-catalog-overview.md) carries an explicit contamination-risk
field (see [note 14, section 4](./14-benchmark-catalog-overview.md#4-the-contamination-risk-scale)).

### 4.3 Models can be over-optimised for the leaderboard

Once a leaderboard becomes influential, developers begin optimising specifically for it:
submit a model, study its weak areas, modify the model, prompt, or agent, and resubmit.
Repeated enough times, the system becomes very good at *that leaderboard* without becoming
equally better on unseen tasks — a pattern known as **leaderboard overfitting**.

This is a specific instance of **Goodhart's Law**:

> *"When a measure becomes a target, it becomes less useful as a measure."*

### 4.4 Overall rankings depend on subjective design choices

Multi-benchmark leaderboards must decide which benchmarks to include, which capabilities to
exclude, how scores are normalised, how much weight each benchmark receives, and whether
cost and latency should affect the ranking. Changing any of these choices can change the
final ranking — a coding-heavy leaderboard may favour one model, while a reasoning-heavy
leaderboard favours another, even using the exact same underlying model scores.

### 4.5 Small score differences may not be meaningful

Leaderboards often display exact ranks even when the gap between models is tiny — Model A
at 84.3 vs. Model B at 84.1. That difference may be nothing more than prompt variation, a
small test set, evaluation noise, or an automatic-grading error. A model ranked third may
not be meaningfully better than the model ranked fifth. Rankings become especially
unstable when confidence intervals or uncertainty estimates are not shown at all — which,
in practice, is most of the time.

### 4.6 Human-preference leaderboards have human biases

Human voting is valuable for measuring helpfulness, tone, and writing quality, but people
tend to prefer answers that are longer, more confident, better formatted, more agreeable,
or more entertaining — independent of whether the answer is accurate, safe, or well
reasoned. The voting population also may not represent your users. Human leaderboards can
further be affected by coordinated voting or attempts to identify and favour particular
models.

### 4.7 Scores may be stale, incomplete, or self-reported

A leaderboard may contain results from older model versions, be missing newly released
models, still list discontinued models, or carry self-reported results that were never
independently verified. Staleness compounds with every other issue on this list — a
contaminated, overfit, or noisy score that is also months out of date is the worst-case
combination.

---

## 5. How to Read LLM Leaderboards as an AI Engineer

> **Leaderboards are a filtering tool, not a decision tool.** Every step below exists to
> stop a leaderboard's ranking from silently becoming your decision.

### Step 1 — Write your requirements down before opening any board

Task type, latency budget, cost ceiling, context needs, and deployment constraints
(on-prem? open weights? data retention?). Doing this **first** is what stops you
rationalizing toward whatever happens to be ranked #1. If there is one habit to take away
from this note, it is this one.

### Step 2 — Pick the board that matches your task, not the famous one

| Your task | Board to check |
| --- | --- |
| Agentic coding / computer use | SWE-bench Pro, Terminal-Bench, OSWorld |
| Tool use / function calling | Berkeley Function-Calling Leaderboard (BFCL) |
| Conversational quality | The matching sub-arena on LMArena |
| Retrieval / embeddings (RAG) | MTEB |
| Budget-constrained deployment | A board that carries price and throughput, e.g. Artificial Analysis |

### Step 3 — Read the board correctly

Before trusting any number on it, answer:

- **What exactly is scored?** Answer key, test suite, human vote, or LLM judge?
- **Who ran the model?** Independent evaluator or vendor self-report (see
  [note 07, section 4](./07-model-evals-benchmarks-and-harnesses.md#4-who-runs-model-evals)
  on why this distinction matters)?
- **At what inference compute?** Reasoning-effort settings change both the score and your
  bill.
- **How old is the eval set?** Static and public means assume contamination pressure (see
  section 4.2 above).
- **Is it saturated?** If the top ten sit within a couple of points, it's effectively over
  as a differentiator.
- **Where are the confidence intervals?** No CI shown means treat adjacent ranks as tied.
- **If composite — what are the weights?** Unpublished weights mean an unpublished opinion.

### Step 4 — Cut to 3–5 candidates

Apply the hard filters first: license, price ceiling, latency, availability in your
region. This is a filtering step, not a ranking step — anything that fails a hard
constraint is out, regardless of how well it scores.

### Step 5 — Run your own eval

This is where the actual decision happens. Fifty to a few hundred examples from your real
traffic beats any public benchmark. Score on **quality, cost, latency, and failure
behaviour** — the same four dimensions a leaderboard shows you, but measured on the task
you actually care about, using the methods covered in
[LLM Evaluation Methods](./04-llm-evaluation-methods.md) and the
[application evaluation workflow](./02-llm_application_evaluation_workflow.md).

---

*Notes on LLM Leaderboards — why they exist, the four types, why they can mislead, and how
to read them. Continues in
[LLM Leaderboards — Platforms](./16-llm-leaderboards-platforms.md).*
