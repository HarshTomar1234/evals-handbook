# Benchmark Catalog — Safety & Alignment

> Whether a model can be trusted to behave responsibly — refusing harm, resisting
> adversarial pressure, and staying truthful rather than merely telling users what they
> want to hear.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

**Coverage note.** As of this catalog's snapshot, BenchWiki tracks a single primary
benchmark in the Safety & Alignment domain — TruthfulQA. This is a narrow slice of the
safety evaluation landscape (it does not cover, for example, jailbreak resistance,
toxicity, or bias benchmarks), reflecting the wiki's current coverage rather than the
full space of safety evals.

---

## Table of Contents

1. [TruthfulQA](#1-truthfulqa)

---

## 1. TruthfulQA

*817 adversarial questions probing whether models repeat common human misconceptions — famous for finding that bigger models were often less truthful.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 8 Sep 2021 |
| Dataset Size | 817 |
| Primary Metric | MC2 (normalized probability mass on true answers, %) |
| Human Baseline | 94% (generation task, human validators) |
| Contamination Risk | high |
| License | Apache-2.0 |
| Maintainer | Stephanie Lin / Owain Evans (github.com/sylinrl/TruthfulQA) |

**What it measures.** TruthfulQA is a **safety-alignment** benchmark (it has no
secondary capability tags) built around 817 questions, each written to be
**adversarial**: the questions target areas where a model is likely to reproduce a
**common human misconception** rather than the correct answer. The benchmark is graded
in three distinct ways. **MC1** is a single-true multiple-choice format, scored by
exact match, that checks whether the model assigns the highest probability to the one
correct answer among four to five options. **MC2** — the site's headline metric — is a
multi-true multiple-choice format scored by log-likelihood: the normalized total
probability mass the model places across the *set* of true answers. A third,
**generation**, task is open-ended: the model writes a free-form answer, which is then
graded separately for truthfulness and informativeness. The benchmark's specific
significance, per its own description, is the finding that **larger models were often
*less* truthful** — the opposite of the trend most other capability benchmarks show as
scale increases, which is exactly the failure mode TruthfulQA was designed to expose:
a model that fluently repeats a popular falsehood rather than correcting it.

**Evaluation protocol.** The standard run configuration is **zero-shot**, **direct**
(no chain-of-thought), at **temperature 0**, scored **pass@1**, with **no tools**.
Harnesses: [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)
(`truthfulqa_mc2` task flag) and [HELM](https://crfm.stanford.edu/helm) (`truthful_qa`
task flag) — both covered generally in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).
The MC1 and MC2 variants carry no `judge_model` — they are scored directly from model
log-probabilities. The **generation** variant is different: it is graded by an
**LLM judge**, originally **GPT-judge**, a fine-tuned GPT-3 Curie model that is now
deprecated. Modern evaluations commonly substitute GPT-4-as-judge instead, which means
generation-task truthfulness scores from different eras are not directly comparable to
one another — the judge itself changed, not just the model being judged.

**Status and saturation.** TruthfulQA is marked **saturated** as of **1 Jun 2023**, the
point marking the ChatGPT/GPT-4 era when the earlier inverse-scaling trend (bigger
models being less truthful) reversed. By 2023, post-RLHF models exceeded the 94% human
truthfulness baseline on the generation task — but per the benchmark's own status
evidence, this happened largely because the exact misconceptions TruthfulQA targets are
what alignment tuning trains against, and the dataset itself leaked into alignment
mixes. In other words, the saturation reflects **targeted contamination**, not broadly
truthful models. No successor is recorded, and the benchmark has no parent and no
tracked variants in BenchWiki's lineage graph.

**Contamination risk.** Risk is rated **high**. The test set is fully public
(`public_test_set: true`) with a **static** refresh cycle and **no mitigation** in
place. The benchmark's own contamination notes call this risk uniquely
**self-defeating**: the misconceptions TruthfulQA probes for are exactly what RLHF and
Constitutional-style alignment tuning are trained to avoid, and the dataset itself
appears in some alignment and instruction-tuning mixes. A modern high score on
TruthfulQA therefore partly measures a model's exposure to the benchmark's own patterns
during alignment training, not a general-purpose truthfulness capability.

**Why it matters for an AI engineer.** TruthfulQA remains historically important as the
benchmark that first surfaced inverse scaling on truthfulness, but its current-era
scores should be read with real skepticism: saturation here is documented to trace back
to targeted alignment-tuning exposure rather than a genuine leap in truthful behavior.
Treat a high TruthfulQA score as weak evidence at best, and be careful which metric a
reported number actually uses — MC1, MC2, and generation-truthfulness are three
different axes, and the generation axis additionally depends on which judge model
graded it, since GPT-judge and GPT-4-as-judge are not interchangeable.

> 817 questions → roughly ±3pt across the 60–90% range (95% CI). The 38 category splits
> (some fewer than 20 questions) are far too small for per-category comparison. Metric
> choice (MC1 vs. MC2 vs. generation) moves scores more than most model-to-model gaps
> do.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-3 175B | OpenAI | 8 Sep 2021 | 58 | independent |

BenchWiki's timeline for TruthfulQA is deliberately limited to this single sourced
point, on the generation-truthfulness axis — the only axis with a published human
baseline (94%) to compare against. Later, otherwise-comparable points are omitted
rather than estimated: the GPT-judge deprecation makes cross-year generation scores
non-comparable, and modern MC2 numbers are harness-dependent with no single
authoritative source.

**Source.** Paper:
["TruthfulQA: Measuring How Models Mimic Human Falsehoods"](https://arxiv.org/abs/2109.07958)
(Stephanie Lin, University of Oxford; Jacob Hilton, OpenAI; Owain Evans, University of
Oxford — ACL 2022). Dataset:
[domenicrosati/TruthfulQA on HuggingFace](https://huggingface.co/datasets/domenicrosati/TruthfulQA).
Leaderboard:
[Open LLM Leaderboard (v1, archived)](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard)
(third-party).

---

*Benchmark catalog — Safety & Alignment domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
