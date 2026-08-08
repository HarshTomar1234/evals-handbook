# Benchmark Catalog — Agentic & Tool Use

> Whether a model can plan and take multi-step actions in an environment — not just
> generate text — using tools, adapting to feedback, and recovering when a step fails.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

**Coverage note.** As of this catalog's snapshot, BenchWiki tracks a single primary
benchmark in the Agentic & Tool Use domain — ARC-AGI-3. This is a narrow and very new
slice of the agentic evaluation landscape (it does not yet cover, for example,
web-browsing agents, computer-use benchmarks, or structured API-calling suites),
reflecting the wiki's current coverage rather than the full space of agentic evals.
Several benchmarks in other domains carry `agentic-tool-use` as a *secondary*
capability (for example SWE-bench Verified and SWE-bench Pro, catalogued under Coding),
which is where most agentic evaluation activity currently sits.

---

## Table of Contents

1. [ARC-AGI-3](#1-arc-agi-3)

---

## 1. ARC-AGI-3

*Interactive ARC environments that score agents by human-relative action efficiency,
planning, exploration, and adaptation.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 17 Apr 2026 |
| Dataset Size | 135 |
| Primary Metric | Relative Human Action Efficiency (RHAE) on interactive environments (%) |
| Human Baseline | 100% (environments are defined as 100% human-solvable; the score treats 100% as beating every game as efficiently as humans) |
| Contamination Risk | medium |
| License | Not published |
| Maintainer | ARC Prize Foundation |

**What it measures.** ARC-AGI-3 is primarily an **agentic-tool-use** benchmark with a
**reasoning** secondary capability tag — it evaluates agents inside 135 interactive ARC
environments rather than asking a model to answer a single static question. The dataset
is **gated** and has no public test set: it splits into 25 easier public demo
environments plus 55 semi-private and 55 fully-private environments that form the main
evaluation sets. The task format is not question-answering but multi-step interaction —
an agent must explore each environment, plan a sequence of actions, and adapt as it
receives feedback, with success judged on whether it completes levels within configured
action and time budgets. The headline metric, **RHAE (Relative Human Action
Efficiency)**, is a **composite** score: per-level action efficiency relative to human
baselines, normalized per environment and aggregated across all environments, where a
100% score means the agent beats every game as efficiently as a human would. Two
scoring variants sit underneath this: an **environment completion** check
(exact-match — did the agent finish the level within budget) and a **cost-normalized
leaderboard view** (composite — plotting performance against total run cost, since
interactive exploration can burn very different amounts of compute for different
agents).

**Evaluation protocol.** Agents get only a **first exposure to each interactive
environment** — there is no repeated-shot warm-up on a given environment. Evaluation is
explicitly **agentic** rather than a simple direct-answer or chain-of-thought format,
and **tools are enabled** (`tools: true`). Temperature and pass@k are not published in
the source data. Harnesses: the **ARC-AGI-3 public game set** and the **ARC-AGI-3 docs
and SDK** (docs.arcprize.org), as covered generally in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).
Neither harness entry carries a specific task flag in the source data.

**Status and saturation.** ARC-AGI-3 is marked **active**, with no saturation date and
no recorded successor. Its status evidence notes that the benchmark is newly
introduced: the paper reports humans solving 100% of environments while frontier AI
systems scored below 1% as of March 2026, and the official leaderboard (generated
2026-07-23) still shows low semi-private scores despite visible progress. In BenchWiki's
lineage graph, ARC-AGI-3's parent is **ARC-AGI-2** — it is the latest entry in the ARC
Prize's line of interactive/abstraction benchmarks — and it currently has no tracked
variants of its own.

**Contamination risk.** Risk is rated **medium**. The test set is not public
(`public_test_set: false`) and the refresh cycle is **static**. Mitigation comes from
the dataset's structure itself: the 25 public demo environments are deliberately easier
and do not comprehensively represent the private mechanics, while the 55 semi-private
and 55 fully-private environments carry the real evaluation weight — the fully-private
set is reserved for tightly guarded competition use. In practice this means a model
that looks strong on the public demo set may still be encountering genuinely novel
mechanics on the semi-private and fully-private splits, which is exactly what keeps the
risk at medium rather than low or high.

**Why it matters for an AI engineer.** ARC-AGI-3 currently separates agents by a very
wide margin — from roughly 0.4-0.5% RHAE in late 2025/early 2026 to 7.78% by July 2026 —
so it is still an unsaturated, discriminative benchmark for agentic planning and
exploration rather than a solved capability check. Because scoring is human-relative
efficiency rather than raw accuracy, and because the official leaderboard plots results
against run cost, comparing two models' scores without also comparing their interaction
budgets is misleading: the source data notes semi-private runs on the leaderboard
ranging from roughly $2.3k to more than $25k in total cost. Treat this as an early,
narrow signal on multi-step interactive agency, not a general-purpose agentic-tool-use
verdict.

> ARC-AGI-3 scores are not exact-match grid accuracies. They are human-relative
> efficiency scores over interactive environments. Public-demo, semi-private, and
> fully-private results should not be mixed without naming the split and budget.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Anthropic Opus 4.6 (Max) | Anthropic | 17 Dec 2025 | 0.51% | vendor-reported |
| GPT-5.6 Sol (Max) | OpenAI | 9 Jul 2026 | 7.78% | vendor-reported |

BenchWiki's timeline records five points in total; shown here are the earliest and
latest. Both are semi-private leaderboard results, and per the benchmark's own
changelog, source decimals were converted to percentages when this catalog's data was
compiled — do not re-scale these figures.

**Source.** Paper:
["ARC-AGI-3: A New Challenge for Frontier Agentic Intelligence"](https://arxiv.org/abs/2603.24621)
(arXiv:2603.24621v2; ARC Prize Foundation Development team, Hunter Henry, David Wexler,
Derek Smith, Francois Chollet — ARC Prize). Dataset:
[arcprize.org/arc-agi/3](https://arcprize.org/arc-agi/3). Leaderboards:
[ARC Prize leaderboard](https://arcprize.org/leaderboard) (official) and
[ARC-AGI-3 competition leaderboard on Kaggle](https://www.kaggle.com/competitions/arc-prize-2026-arc-agi-3/leaderboard)
(official).

---

*Benchmark catalog — Agentic & Tool Use domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
