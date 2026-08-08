# Benchmark Catalog — Reasoning

> Whether a model can chain multiple facts or logical steps together to reach a correct
> conclusion — beyond single-fact recall, and often under adversarial or commonsense
> pressure designed to trip up shallow pattern matching.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

---

## Table of Contents

1. [At a Glance](#1-at-a-glance)
2. [ARC-AGI-2](#2-arc-agi-2)
3. [AGIEval](#3-agieval)
4. [ARC-AGI](#4-arc-agi)
5. [BIG-Bench Hard](#5-big-bench-hard)
6. [CommonsenseQA](#6-commonsenseqa)
7. [GLUE](#7-glue)
8. [HellaSwag](#8-hellaswag)
9. [SuperGLUE](#9-superglue)
10. [WinoGrande](#10-winogrande)
11. [BIG-Bench](#11-big-bench)

---

## 1. At a Glance

| Benchmark | Status | Launch Date | Dataset Size | Primary Metric | Contamination Risk |
| --- | --- | --- | --- | --- | --- |
| [ARC-AGI-2](#2-arc-agi-2) | nearing-saturation | 24 Mar 2025 | 120 | Task success rate on the public evaluation split (%) | Medium |
| [AGIEval](#3-agieval) | saturated | 13 Apr 2023 | 8,062 | Accuracy, macro-averaged over 20 tasks (%) | High |
| [ARC-AGI](#4-arc-agi) | saturated | 5 Nov 2019 | 400 | Task success rate on the evaluation split (%) | High |
| [BIG-Bench Hard](#5-big-bench-hard) | saturated | 17 Oct 2022 | 6,511 | Exact-match accuracy, averaged over 23 tasks (%) | High |
| [CommonsenseQA](#6-commonsenseqa) | saturated | 2 Nov 2018 | 1,140 | Accuracy (%) | High |
| [GLUE](#7-glue) | saturated | 20 Apr 2018 | Not published | GLUE Score (0-100) | High |
| [HellaSwag](#8-hellaswag) | saturated | 19 May 2019 | 10,003 | Normalized accuracy (%) | High |
| [SuperGLUE](#9-superglue) | saturated | 2 May 2019 | Not published | SuperGLUE aggregate score (%) | High |
| [WinoGrande](#10-winogrande) | saturated | 24 Jul 2019 | 1,767 | Accuracy (%) | High |
| [BIG-Bench](#11-big-bench) | deprecated | 9 Jun 2022 | Not published | Normalized preferred-metric aggregate (0–100) | High |

---

## 2. ARC-AGI-2

*The harder static ARC successor: human-calibrated colored-grid abstraction tasks, now pressured by 2026 frontier systems.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | nearing-saturation |
| Launch Date | 24 Mar 2025 |
| Dataset Size | 120 tasks (public evaluation split) |
| Primary Metric | Task success rate on the public evaluation split (%) |
| Human Baseline | 100% (Human Panel, ARC Prize leaderboard) |
| Contamination Risk | Medium |
| License | Apache-2.0 |
| Maintainer | ARC Prize Foundation |

### What it measures

ARC-AGI-2 is the direct successor to the original ARC-AGI: colored-grid abstraction puzzles where a model is shown a handful of input/output grid pairs and must induce the transformation rule, then apply it to a new input. There is no natural-language fluency, world knowledge, or memorized fact that helps here — each task is solved (or not) purely by pattern induction and few-shot rule generalization, which is why the benchmark sits squarely in the reasoning domain with no secondary capability tags. Scoring is exact-match at the grid level: every predicted output grid must match the target's dimensions and every cell, with two output attempts allowed per test input.

### Evaluation protocol

The run configuration is agentic chain-of-thought with few-shot in-task demonstrations (the demonstration pairs are part of each task itself, not external shots), `pass@2` (two attempts per test input), and tools enabled. As covered in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md), toggling tools and the attempt budget can swing a score substantially — reproducing an ARC-AGI-2 number requires matching not just the model but the search/tool budget and attempt count it was run under. The two official harnesses are the [ARC-AGI-2 repository](https://github.com/arcprize/ARC-AGI-2) and [ARC Prize benchmarking](https://github.com/arcprize/arc-agi-benchmarking).

### Status and saturation

ARC-AGI-2 is marked **nearing-saturation**, not fully saturated: the official ARC Prize leaderboard (generated 2026-07-23) lists GPT-5.6 Sol Max at 92.5% and GPT-5.6 Sol xHigh at 90.0% on the semi-private axis, but competition/private-set validation and efficiency constraints still leave real headroom. It has already spawned a successor, **ARC-AGI-3**, and is itself a variant in the ARC lineage descending from the original **ARC-AGI**.

### Contamination risk

Contamination risk is rated **medium**. ARC-AGI-2 keeps its public training and evaluation data available for open research, but adds semi-private and fully-private test sets specifically to reduce leakage for leaderboard and competition scoring. In practice: a score sourced from the public evaluation split should be treated with more caution than one explicitly labeled semi-private or private, since the public split invites repeated tuning against it.

### Why it matters for an AI engineer

A vendor-reported ARC-AGI-2 number is currently one of the more trustworthy reasoning scores available, provided it's sourced from the semi-private or private leaderboard axis rather than the public evaluation split — check which axis before comparing across vendors. Because it is not yet saturated, it remains genuinely useful for differentiating frontier systems today, but it is worth revisiting cost per task alongside the score, since the leaderboard shows an order-of-magnitude spread in dollars per task among models at similar accuracy.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4.5 (pure LLM) | OpenAI | 24 Mar 2025 | 0% | Vendor-reported |
| o3-preview-low (CoT + search/synthesis) | OpenAI | 24 Mar 2025 | 4% | Vendor-reported |
| Claude 4.7 (Max) | Anthropic | 16 Apr 2026 | 75.8% | Vendor-reported |
| GPT-5.6 Sol (Max) | OpenAI | 9 Jul 2026 | 92.5% | Vendor-reported |

> The public evaluation set has only 120 tasks, so one solved task moves the score by 0.83 points. The official leaderboard uses the semi-private axis while Kaggle competitions use a private axis — identify the split, attempt budget, and cost budget before comparing scores.

### Source

Paper: ["ARC-AGI-2: A New Challenge for Frontier AI Reasoning Systems"](https://arxiv.org/abs/2505.11831) (arXiv:2505.11831v2). Dataset: [github.com/arcprize/ARC-AGI-2](https://github.com/arcprize/ARC-AGI-2). Leaderboards: [ARC Prize leaderboard](https://arcprize.org/leaderboard), [ARC Prize 2025 competition](https://arcprize.org/competitions/2025).

---

## 3. AGIEval

*Standardized human exams — SATs, LSATs, China's Gaokao and civil-service tests — repurposed to grade models against real human test-takers.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 1 Jun 2024) |
| Launch Date | 13 Apr 2023 |
| Dataset Size | 8,062 questions across 20 exam sections |
| Primary Metric | Accuracy, macro-averaged over the 20 tasks (%) |
| Human Baseline | 67% (average test-taker); 91% top-percentile ceiling |
| Contamination Risk | High |
| License | MIT |
| Maintainer | Microsoft (Zhong et al.); repo github.com/ruixiangcui/AGIEval |

### What it measures

AGIEval repurposes real, published human exams — SATs, LSATs, China's Gaokao, and Chinese civil-service and bar exams — as a model benchmark, evaluated bilingually in English and Chinese (a secondary knowledge tag reflects the substantial factual load in these exams). Rather than synthetic reasoning puzzles, every question is one a human test-taker actually sat for, which lets scores be compared directly against measured human performance distributions rather than an estimated ceiling. Scoring is exact-match, zero-shot with chain-of-thought, macro-averaged unweighted across the 20 exam sections.

### Evaluation protocol

The headline configuration is zero-shot with chain-of-thought reasoning, greedy decoding (temperature 0), and `pass@1`, no tools. Harnesses: the [AGIEval official repository](https://github.com/ruixiangcui/AGIEval) and [OpenCompass](https://github.com/open-compass/opencompass) (`agieval` task flag).

### Status and saturation

AGIEval is marked **saturated** as of 1 Jun 2024. GPT-4 already cleared the average-human line (67%) on several sections at launch; by 2024, frontier models pushed the 20-task aggregate past the average human and near the top-percentile 91% ceiling, and the human-exam framing lost its discriminative power at the top. It has no recorded successor and no parent in its lineage.

### Contamination risk

Contamination risk is rated **high**, with no mitigation built in. AGIEval draws from real, published exams whose exact questions and worked solutions circulate widely on test-prep sites and are exactly the kind of high-value content web-scale training crawls pick up. There is no held-out or refreshed variant, so any post-2023 score should be treated as memorization-inclusive rather than a clean measure of reasoning.

### Why it matters for an AI engineer

Treat AGIEval as historical: it is saturated and carries a high, unmitigated contamination risk, so a vendor-reported score today tells you more about whether a model has seen SAT/LSAT/Gaokao prep material than about its reasoning ability. It remains useful for understanding how quickly human-exam framings got beaten, but it should not anchor a current model comparison.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| text-davinci-003 | OpenAI | 13 Apr 2023 | 37.4% | Independent |
| GPT-4 | OpenAI | 13 Apr 2023 | 58.4% | Independent |
| GPT-4o (few-shot) | OpenAI | 13 May 2024 | 69% | Vendor-reported |
| DeepSeek-V3 (base, 0-shot) | DeepSeek | 26 Dec 2024 | 79.6% | Vendor-reported |

> 8,062 questions span 20 unequally-sized exam sections; because the aggregate is an unweighted task average, a small section (a few hundred cloze items) moves the headline as much as a large one. Per-section scores — not the 20-task mean — are where the remaining signal lives now that the aggregate is saturated. Note also that the GPT-4o, Qwen2.5, and DeepSeek-V3 points use different eval settings (few-shot or 0-shot without CoT) than the original zero-shot-CoT paper axis, so they are not strictly comparable to the first three points.

### Source

Paper: ["AGIEval: A Human-Centric Benchmark for Evaluating Foundation Models"](https://arxiv.org/abs/2304.06364) (Findings of NAACL 2024). Dataset: [github.com/ruixiangcui/AGIEval](https://github.com/ruixiangcui/AGIEval). Leaderboard: [OpenCompass (AGIEval)](https://opencompass.org.cn).

---

## 4. ARC-AGI

*Colored-grid abstraction tasks that test few-shot rule induction, not memorized facts, language fluency, or school math.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 20 Dec 2024) |
| Launch Date | 5 Nov 2019 |
| Dataset Size | 400 tasks (evaluation split) |
| Primary Metric | Task success rate on the evaluation split (%) |
| Human Baseline | 98% (Human Panel, ARC Prize leaderboard) |
| Contamination Risk | High |
| License | Apache-2.0 |
| Maintainer | Francois Chollet; ARC Prize Foundation |

### What it measures

The original ARC-AGI (from Chollet's "On the Measure of Intelligence") is the same style of colored-grid abstraction task as ARC-AGI-2: a small number of input/output demonstration pairs, then a novel input the model must transform correctly. Success requires exact-match at the grid level across every cell and dimension — there is no partial credit, and no amount of memorized world knowledge substitutes for correctly inducing the rule.

### Evaluation protocol

The configuration is agentic chain-of-thought with few-shot in-task demonstrations, `pass@3`, and tools enabled. Harnesses: the [ARC-AGI repository](https://github.com/fchollet/ARC-AGI) and [ARC Prize benchmarking](https://github.com/arcprize/arc-agi-benchmarking). Cost varies enormously by test-time reasoning budget — the official leaderboard reports modern semi-private runs from cents per task to several dollars, while older o3-preview high-compute runs were far more expensive, a reminder that ARC-AGI scores and their cost are inseparable.

### Status and saturation

ARC-AGI-1 is marked **saturated** as of 20 Dec 2024. The official ARC Prize leaderboard (generated 2026-07-23) lists multiple verified systems above 95% on the semi-private axis, including GPT-5.6 Sol at 97.5%. ARC Prize launched **ARC-AGI-2** as its successor specifically because ARC-AGI-1 had become too brute-forceable and stopped discriminating frontier systems.

### Contamination risk

Contamination risk is rated **high**. The public train and evaluation tasks are fully available on GitHub; ARC Prize later added semi-private and private evaluation sets for leaderboard and competition use, but ARC-AGI-1 still has a long submission history against a fixed, aging private pool. Public-set scores should be treated as high-risk, and even public-leaderboard comparisons need the split and testing policy stated clearly.

### Why it matters for an AI engineer

ARC-AGI-1 should now be treated as historical/legacy for model comparison — it is saturated, and the field itself moved to ARC-AGI-2. Its main current value is as a reference point for how fast frontier reasoning systems closed a gap that took years to open (GPT-4o at 5% in mid-2024 to systems above 95% roughly two years later), not as a live discriminator between today's models.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o | OpenAI | 27 Jun 2024 | 5% | Vendor-reported |
| o3-preview-low (CoT + search/synthesis) | OpenAI | 20 Dec 2024 | 75.7% | Vendor-reported |
| Claude 4.7 (High) | Anthropic | 16 Apr 2026 | 93.5% | Vendor-reported |
| GPT-5.6 Sol (xHigh) | OpenAI | 9 Jul 2026 | 97.5% | Vendor-reported |

### Source

Paper: ["On the Measure of Intelligence"](https://arxiv.org/abs/1911.01547). Dataset: [github.com/fchollet/ARC-AGI](https://github.com/fchollet/ARC-AGI). Leaderboards: [ARC Prize leaderboard](https://arcprize.org/leaderboard), [ARC Prize 2024 competition](https://arcprize.org/competitions/2024).

---

## 5. BIG-Bench Hard

*23 BIG-Bench tasks where models trailed humans — the benchmark that proved chain-of-thought prompting works, then fell to the reasoning-model era.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 1 Jun 2024) |
| Launch Date | 17 Oct 2022 |
| Dataset Size | 6,511 examples across 23 tasks |
| Primary Metric | Exact-match accuracy, averaged over 23 tasks (%) |
| Human Baseline | 67.7% average rater; ~94.4% best (max) rater |
| Contamination Risk | High |
| License | MIT |
| Maintainer | Mirac Suzgun et al. (Google Research); repo suzgunmirac/BIG-Bench-Hard |

### What it measures

BIG-Bench Hard (BBH) is a curated subset of 23 tasks pulled from the sprawling 204-task BIG-Bench where prior language models scored below the average human rater — that is, the tasks BIG-Bench found genuinely hard. The paper that introduced it also demonstrated that chain-of-thought prompting could close much of that human/model gap, making BBH historically significant as evidence for CoT's effectiveness, not just as a reasoning test. Scoring is exact-match, macro-averaged unweighted across the 23 tasks.

### Evaluation protocol

The standard configuration is 3-shot with chain-of-thought, greedy decoding (temperature 0), `pass@1`, no tools. Harnesses: [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) (`bbh_cot_fewshot` task flag) and the [BIG-Bench Hard official repository](https://github.com/suzgunmirac/BIG-Bench-Hard).

### Status and saturation

BBH is marked **saturated** as of 1 Jun 2024. Frontier and reasoning-tuned models now exceed roughly 90% on the 23-task average and are near-perfect on many individual tasks — the reasoning-model era made BBH trivial. Its parent is **BIG-Bench**, and Google DeepMind built the successor **BIG-Bench Extra Hard (BBEH)** in 2025, replacing each task with a harder counterpart precisely because BBH stopped discriminating at the top.

### Contamination risk

Contamination risk is rated **high**. BBH inherits BIG-Bench's public tasks and its widely-ignored canary GUID mechanism; BBH's popularity made it especially likely to appear in pretraining and instruction-tuning data, and there is no refreshed variant. This high contamination risk is a direct reason BBEH was built with fresh tasks rather than reusing BBH's.

### Why it matters for an AI engineer

A BBH score reported today should be read as a saturated, contamination-prone legacy number rather than a live signal — treat it the way you'd treat an old resume line, informative about history but not about current standing. For comparing frontier models today, look to BBH's harder successor (BBEH) instead; BBH is still fine for understanding how CoT prompting changed the field circa 2022–2023.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| PaLM 540B | Google | 17 Oct 2022 | 65.2% | Independent |
| GPT-4 (3-shot CoT) | OpenAI | 14 Mar 2023 | 83.1% | Vendor-reported |
| Claude 3.5 Sonnet (3-shot CoT) | Anthropic | 21 Jun 2024 | 93.1% | Vendor-reported |
| DeepSeek V3 | DeepSeek | 26 Dec 2024 | 87.5% | Vendor-reported |

> 6,511 examples span 23 unequally-sized tasks; because the aggregate is an unweighted task average, a small task moves the number as much as a large one. Now that the mean is saturated, per-task scores — not the 23-task average — are where any remaining signal lives.

### Source

Paper: ["Challenging BIG-Bench Tasks and Whether Chain-of-Thought Can Solve Them"](https://arxiv.org/abs/2210.09261) (ACL Findings 2023). Dataset: [github.com/suzgunmirac/BIG-Bench-Hard](https://github.com/suzgunmirac/BIG-Bench-Hard). Leaderboard: [llm-stats (BBH)](https://llm-stats.com/benchmarks/big-bench-hard).

---

## 6. CommonsenseQA

*Five-choice questions derived from ConceptNet relations, designed to require everyday knowledge beyond a supplied passage.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 6 Dec 2021) |
| Launch Date | 2 Nov 2018 |
| Dataset Size | 1,140 (test set) |
| Primary Metric | Accuracy (%) |
| Human Baseline | 88.9% (majority vote of five crowd workers) |
| Contamination Risk | High |
| License | MIT |
| Maintainer | Tel Aviv University NLP |

### What it measures

CommonsenseQA presents five-choice multiple-choice questions built from ConceptNet relations, deliberately constructed so that the correct answer requires everyday commonsense knowledge rather than information contained in a supplied passage — there is no passage to read, only the question and five candidate answers, with random chance at 20%. As a secondary knowledge capability, it tests whether a model's training has embedded the kind of everyday relational knowledge (a duck lives in a pond, a fork is used to eat) that ConceptNet encodes formally.

### Evaluation protocol

The standard configuration is zero-shot, direct (no chain-of-thought), greedy decoding (temperature 0), `pass@1`, no tools, via [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) (`commonsense_qa` task flag). Official results use the hidden random-split test set, while lm-evaluation-harness instead scores the public validation split — a protocol difference worth checking before comparing two reported numbers.

### Status and saturation

CommonsenseQA is marked **saturated** as of 6 Dec 2021, when Microsoft's KEAR ensemble reached 89.4% on the hidden test set against the original 88.9% human reference. The benchmark's own authors then introduced the adversarially collected **CommonsenseQA 2.0** as a harder variant; there is no formally named successor, only that lineage variant.

### Contamination risk

Contamination risk is rated **high**. Official test answers are hidden, but the training and validation questions are public and static, with no refresh cycle. The recommended mitigation is to use the hidden evaluation server where available, disclose any retrieval or fine-tuning data, and audit overlap with public mirrors — none of which is enforced automatically.

### Why it matters for an AI engineer

Treat CommonsenseQA as a legacy benchmark: it crossed its human baseline in 2021 and the field's own maintainers moved on to CommonsenseQA 2.0 for headroom. A modern vendor-reported score near or above 85% tells you comparatively little given the high contamination risk on the public validation split most harnesses actually use — it's more useful as a sanity check than a comparative signal between today's frontier models.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| BERT-Large | Google | 2 Nov 2018 | 55.9% | Independent |
| KEAR ensemble | Microsoft | 6 Dec 2021 | 89.4% | Vendor-reported |
| CPACE | Independent | 14 May 2023 | 89.8% | Independent |
| GPT-4o | OpenAI | 13 May 2024 | 85.4% | Vendor-reported |

### Source

Paper: ["CommonsenseQA: A Question Answering Challenge Targeting Commonsense Knowledge"](https://arxiv.org/abs/1811.00937) (NAACL-HLT 2019). Dataset: [huggingface.co/datasets/tau/commonsense_qa](https://huggingface.co/datasets/tau/commonsense_qa). Leaderboard: [Official CommonsenseQA leaderboard](https://www.tau-nlp.org/commonsenseqa).

---

## 7. GLUE

*Nine-task English NLU suite combining acceptability, sentiment, similarity, paraphrase, inference and coreference into one score.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 25 Jul 2019) |
| Launch Date | 20 Apr 2018 |
| Dataset Size | Not published (varies per constituent task) |
| Primary Metric | GLUE Score (0-100) |
| Human Baseline | 87.1 (conservative non-expert crowd estimate) |
| Contamination Risk | High |
| License | Not published |
| Maintainer | NYU Machine Learning for Language |

### What it measures

GLUE bundles nine distinct English natural-language-understanding tasks into a single composite score: CoLA (linguistic acceptability), SST-2 (sentiment), MRPC (paraphrase, accuracy and F1 averaged), STS-B (semantic similarity regression, Pearson/Spearman averaged), QQP (duplicate-question detection, accuracy and F1 averaged), MNLI (three-way natural language inference, matched/mismatched averaged), QNLI (question-sentence entailment), RTE (textual entailment), and WNLI (Winograd-derived NLI, with a documented adversarial overlap quirk). A separate GLUE diagnostic set of hand-built NLI examples grouped by linguistic phenomena is analyzed independently and excluded from the official aggregate. Each task's own metric is computed, paired metrics are averaged within a task, and the nine task scores are macro-averaged into the headline GLUE Score.

### Evaluation protocol

The classic protocol is supervised fine-tuning per task against the official platform; [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness/tree/main/lm_eval/tasks/glue) instead defaults to zero-shot validation-split evaluation, direct (no CoT), `pass@1`. Harnesses: the [GLUE official platform](https://gluebenchmark.com/), [GLUE official baseline code](https://github.com/nyu-mll/GLUE-baselines) (`all` task flag), and lm-evaluation-harness (`glue` task flag). These are meaningfully different evaluation regimes — fine-tuned submission-server scores are not directly comparable to zero-shot harness scores on the same task name.

### Status and saturation

GLUE is marked **saturated** as of 25 Jul 2019, when RoBERTa reached 88.5 on the official nine-task leaderboard, above the conservative 87.1 non-expert human aggregate. Its successor, **SuperGLUE**, was introduced specifically to restore headroom once GLUE stopped separating strong systems. It has one lineage variant, the GLUE diagnostic set, which is scored separately from the main aggregate.

### Contamination risk

Contamination risk is rated **high**. Four constituent tasks originally used private test data with hidden official-submission labels, but all training/development data, several test sets, task source corpora, and the diagnostic set are static and widely mirrored, with no refresh cycle.

### Why it matters for an AI engineer

GLUE should be treated as historical — it was saturated by 2019, its own creators built SuperGLUE as the replacement, and pre-transformer-era task suites like this carry a high, unmitigated contamination risk after years of public mirroring. It has essentially no value for comparing today's frontier models; its main relevance now is illustrating how quickly a well-designed 2018 suite can be outrun by architecture progress.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| BERT Large | Google | 11 Oct 2018 | 80.5 | Vendor-reported |
| RoBERTa ensemble | Facebook AI | 25 Jul 2019 | 88.5 | Vendor-reported |
| T5 (ensemble) | Google | 23 Oct 2019 | 90.3 | Vendor-reported |
| DeBERTa (TuringNLRv4) | Microsoft | 6 Jan 2021 | 90.8 | Vendor-reported |

> A suite average hides very different sample sizes, metrics, and uncertainty. Small tasks such as RTE and MRPC have high seed variance, while QQP and MNLI dominate training volume but still receive only one task's weight each in the macro-average.

### Source

Paper: ["GLUE: A Multi-Task Benchmark and Analysis Platform for Natural Language Understanding"](https://aclanthology.org/W18-5446/) (BlackboxNLP at EMNLP 2018). Dataset: [huggingface.co/datasets/nyu-mll/glue](https://huggingface.co/datasets/nyu-mll/glue). Leaderboard: [Official GLUE leaderboard](https://gluebenchmark.com/leaderboard/).

---

## 8. HellaSwag

*Choose the plausible continuation of an everyday event from one real ending and three adversarially filtered machine generations.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 14 Mar 2023) |
| Launch Date | 19 May 2019 |
| Dataset Size | 10,003 (test); 10,042 (public validation split) |
| Primary Metric | Normalized accuracy (%) |
| Human Baseline | 95.6% (overall test accuracy) |
| Contamination Risk | High |
| License | MIT |
| Maintainer | Rowan Zellers / University of Washington |

### What it measures

HellaSwag gives a model the beginning of an everyday-activity description and four possible continuations — one real, three adversarially filtered machine-generated distractors designed to fool models that rely on surface statistics rather than genuine situational reasoning. Random chance among four endings is 25%. Scoring uses log-likelihood: the correct continuation should receive the highest total log-likelihood, with a length-normalized variant (`acc_norm`) as the standard reported metric to correct for token-length bias between candidate continuations.

### Evaluation protocol

The standard configuration is zero-shot, direct, greedy decoding (temperature 0), `pass@1`, no tools, via [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) (`hellaswag` task flag), which reports validation-set `acc_norm`. Because scoring is log-likelihood-based rather than generation-based, HellaSwag numbers are also sensitive to how the harness constructs and length-normalizes each candidate continuation — a detail that can shift scores independent of underlying model quality, as covered generally in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).

### Status and saturation

HellaSwag is marked **saturated** as of 14 Mar 2023, when GPT-4 base reached 95.3% on a private holdout against the original 95.6% human reference. The official leaderboard closed submissions in 2024. It has no recorded successor; its parent in the lineage is **SWAG**, the dataset HellaSwag was adversarially re-filtered from.

### Contamination risk

Contamination risk is rated **high**. Official test labels were withheld and GPT-4's reported score used a private holdout, but the standard harness protocol evaluates the fully public 10,042-item validation split, which carries no held-out-label protection. Any score should be checked against which split (private test vs. public validation) produced it.

### Why it matters for an AI engineer

HellaSwag should be read as historical/legacy: it hit human parity in 2023, its official leaderboard is closed, and the split most harnesses actually run against is fully public and long-mirrored. A high score on the public validation split today is not strong evidence of genuine commonsense continuation ability — it is exactly the kind of number that benchmark contamination inflates without detection.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| BERT-Large | Google | 19 May 2019 | 47.3% | Independent |
| GPT-3 175B (few-shot) | OpenAI | 22 Jul 2020 | 79.3% | Vendor-reported |
| Claude 3 Opus | Anthropic | 4 Mar 2024 | 95.4% | Vendor-reported |
| GPT-4o | OpenAI | 1 Aug 2024 | 95.3% | Vendor-reported |

> On 10,003 independent test items, a score near 50% has an approximate 95% binomial margin of error of +/-1.0 point; near 95% it is about +/-0.4 point. Prompting and length-normalization choices can move results by more than this margin.

### Source

Paper: ["HellaSwag: Can a Machine Really Finish Your Sentence?"](https://arxiv.org/abs/1905.07830) (ACL 2019). Dataset: [huggingface.co/datasets/Rowan/hellaswag](https://huggingface.co/datasets/Rowan/hellaswag). Leaderboard: [Official HellaSwag leaderboard (closed)](https://rowanzellers.com/hellaswag/).

---

## 9. SuperGLUE

*Eight English NLU tasks spanning question answering, inference, causal reasoning, word sense, and coreference.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 29 Dec 2020) |
| Launch Date | 2 May 2019 |
| Dataset Size | Not published (varies per constituent task) |
| Primary Metric | SuperGLUE aggregate score (%) |
| Human Baseline | 89.8 (official aggregate estimate) |
| Contamination Risk | High |
| License | Not published |
| Maintainer | GLUE Benchmark team |

### What it measures

SuperGLUE is GLUE's successor: eight harder English NLU tasks — BoolQ (yes/no QA grounded in a passage), CB (three-way entailment, accuracy and macro-F1 averaged), COPA (plausible cause/effect selection), MultiRC (multi-sentence reading comprehension, F1a/EM averaged), ReCoRD (cloze-style reading comprehension, F1/EM averaged), RTE (binary entailment), WiC (word-in-context sense disambiguation), and WSC (Winograd-style pronoun resolution). Paired metrics are averaged within a task first, then the eight task scores are unweighted-averaged into the aggregate. Its official test labels are held on a private server, so the aggregate score is a genuine held-out-style evaluation when run through official channels.

### Evaluation protocol

Official leaderboard systems are task-fine-tuned; [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness/tree/main/lm_eval/tasks/super_glue) instead defaults to zero-shot, direct, greedy decoding (temperature 0), `pass@1`, no tools — via two prompt variants, an LM Eval v1-compatible set (`super-glue-lm-eval-v1`) and a T5-compatible set (`super-glue-t5-prompt`). As with GLUE, fine-tuned submission-server scores and zero-shot harness scores under the same task name are not directly comparable numbers.

### Status and saturation

SuperGLUE is marked **saturated** as of 29 Dec 2020, when a single DeBERTa 1.5B model reached 89.9, crossing the official aggregate human estimate of 89.8. By 2022 the leaderboard leader (Vega v2) reached 91.3, leaving only a narrow, noisy ceiling above the human line. It has no recorded successor; its parent in the lineage is **GLUE**.

### Contamination risk

Contamination risk is rated **high**. Official test labels were kept private behind the evaluation server, but test inputs, training and development data, source corpora, and benchmark examples are static and public — and modern harnesses normally score the labeled public validation splits, which have no held-out-label protection at all.

### Why it matters for an AI engineer

SuperGLUE should be treated as historical: it was saturated within about 18 months of launch and, like GLUE, has accumulated years of public mirroring on the splits most harnesses actually evaluate. Its main remaining value for an engineer is as a reference point for how narrow the post-human-parity ceiling became (89.8 to 91.3 across roughly two years) before the field moved on to other reasoning suites.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| BERT++ | Benchmark authors | 2 May 2019 | 71.5 | Independent |
| DeBERTa 1.5B (single model) | Microsoft | 29 Dec 2020 | 89.9 | Vendor-reported |
| ST-MoE-32B | Google | 17 Feb 2022 | 91.2 | Vendor-reported |
| Vega v2 | JD Explore Academy | 8 Oct 2022 | 91.3 | Vendor-reported |

> The aggregate is not a sample-size-weighted accuracy and has no single binomial confidence interval. CB, COPA, and WSC have especially small evaluation splits, while metric and prompt implementation differences can exceed small leaderboard gaps.

### Source

Paper: ["SuperGLUE: A Stickier Benchmark for General-Purpose Language Understanding Systems"](https://arxiv.org/abs/1905.00537) (NeurIPS 2019). Dataset: [super.gluebenchmark.com/tasks](https://super.gluebenchmark.com/tasks). Leaderboard: [Official SuperGLUE leaderboard](https://super.gluebenchmark.com/leaderboard).

---

## 10. WinoGrande

*Binary fill-in-the-blank commonsense problems, scaled from Winograd schemas and adversarially filtered to reduce dataset shortcuts.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | saturated (since 17 Feb 2022) |
| Launch Date | 24 Jul 2019 |
| Dataset Size | 1,767 (test); 1,267 (public validation split) |
| Primary Metric | Accuracy (%) |
| Human Baseline | 94% (crowd-worker estimate) |
| Contamination Risk | High |
| License | CC BY (dataset); Apache-2.0 (code) |
| Maintainer | Allen Institute for AI |

### What it measures

WinoGrande scales the classic Winograd Schema Challenge up to thousands of binary fill-in-the-blank sentences, each requiring the model to resolve which of two candidate noun phrases a pronoun or blank refers to — a task humans do effortlessly via commonsense reasoning about the situation described, but which is easy to get wrong via surface pattern matching. The dataset is adversarially filtered (AfLite) specifically to remove annotation artifacts and dataset shortcuts that let earlier models "solve" Winograd-style problems without genuine reasoning. Random chance is 50%. The official protocol keeps test labels hidden and scores via submission; a secondary lm-eval variant instead ranks the two candidate substitutions by conditional log-likelihood on the public validation split.

### Evaluation protocol

lm-evaluation-harness defaults to zero-shot, direct, `pass@1`, no tools; the original paper instead fine-tunes on graduated training subsets (XS/S/M/L/XL) of increasing size. Harnesses: the [WinoGrande official code](https://github.com/allenai/winogrande) (`winogrande` task flag) and [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness/tree/main/lm_eval/tasks/winogrande) (`winogrande` task flag). Fine-tuned-subset scores from the original paper and zero-shot/few-shot scores from modern LLM system cards are measuring genuinely different things despite sharing a benchmark name.

### Status and saturation

WinoGrande is marked **saturated** as of 17 Feb 2022. The original XL fine-tuned result was 79.1% against a 94.0% human estimate — real headroom at launch — but the static public inputs and widespread validation-set use have since made modern scores contamination-sensitive rather than a clean capability signal. It has no recorded successor; its parent is the original **Winograd Schema Challenge**, and it has six size-graduated lineage variants (debiased, XS, S, M, L, XL).

### Contamination risk

Contamination risk is rated **high**. Official test labels remain hidden and lm-evaluation-harness exposes a decontamination query, but the test inputs and the fully labeled validation split are public and unchanged since release, with no refresh cycle.

### Why it matters for an AI engineer

A WinoGrande score should be read with real skepticism about which split produced it: the hidden-label official test protocol is meaningfully more trustworthy than the commonly-run public validation split, and most reported frontier-model numbers (5-shot, vendor system cards) are almost certainly the latter. Given the benchmark is already saturated and high-contamination-risk, it is better used as a historical marker of how quickly adversarial filtering got caught up to than as a live comparison tool.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| BERT-Large (fine-tuned) | Google | 24 Jul 2019 | 64.9% | Independent |
| GPT-4 (5-shot) | OpenAI | 14 Mar 2023 | 87.5% | Vendor-reported |
| Claude 3 Opus (5-shot) | Anthropic | 4 Mar 2024 | 88.5% | Vendor-reported |
| Llama 3.1 405B (5-shot) | Meta | 23 Jul 2024 | 89.2% | Vendor-reported |

> On the 1,267-example validation split, a 50% accuracy estimate has an approximate 95% binomial margin of error of +/-2.8 points; paired predictions are preferable for close model comparisons.

### Source

Paper: ["WinoGrande: An Adversarial Winograd Schema Challenge at Scale"](https://arxiv.org/abs/1907.10641) (AAAI 2020). Dataset: [huggingface.co/datasets/allenai/winogrande](https://huggingface.co/datasets/allenai/winogrande). Leaderboard: [Official WinoGrande leaderboard](https://leaderboard.allenai.org/winogrande/submissions/public).

---

## 11. BIG-Bench

*204-task community-built mega-suite spanning reasoning, knowledge, math, code and social bias — the sprawling proving ground BBH was distilled from.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 9 Jun 2022 |
| Dataset Size | Not published (204 tasks, sizes vary from a handful to thousands of examples each) |
| Primary Metric | Normalized preferred-metric aggregate (0–100) |
| Human Baseline | Not published |
| Contamination Risk | High |
| License | Apache-2.0 |
| Maintainer | Google & the BIG-bench collaboration (~450 authors across 132 institutions); repo google/BIG-bench |

### What it measures

BIG-Bench is a sprawling, community-built mega-suite of 204 tasks spanning reasoning, knowledge, mathematics, coding, and social bias — reflected in its four secondary capability tags alongside its primary reasoning classification. Each task defines its own "preferred metric" (multiple-choice grade, exact-match, BLEU/ROUGE, or a custom scorer), normalized so roughly 0 means poor performance and 100 means expert-rubric performance, then averaged across all 204 tasks into a single composite. It was the sprawling proving ground from which the tighter, harder **BIG-Bench Hard** subset was later distilled.

### Evaluation protocol

Per-task shot count varies (0-shot or few-shot depending on the task), scored direct (no CoT), `pass@1`, no tools. Harnesses: [BIG-bench (official)](https://github.com/google/BIG-bench) and [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) (`bigbench` task flag). Cost is described as highly variable — the full 204-task suite is large and heterogeneous, and most users run the 24-task BIG-bench Lite subset or single tasks rather than the entire suite, which itself changes what any given "BIG-Bench score" actually represents.

### Status and saturation

BIG-Bench is marked **deprecated** rather than saturated: models do not uniformly max it out, it simply stopped being used as an evaluation target. It is no longer maintained as a live benchmark. Its task quality is famously uneven, and its "canary" GUID contamination-prevention mechanism was widely ignored, letting the suite leak into web-scale training corpora anyway. The field moved to distillates instead — its named successor is **BIG-Bench Hard**, and it also has a **BIG-Bench Lite** lineage variant.

### Contamination risk

Contamination risk is rated **high**. Every task file embeds a "canary" GUID string that trainers are asked (voluntarily) not to strip, so they can detect and exclude BIG-Bench from crawled data — in practice the canary was widely ignored, and the suite leaked into training corpora anyway. It stands as the field's most-cited example of a decontamination mechanism that depends on voluntary compliance and consequently failed.

### Why it matters for an AI engineer

BIG-Bench should be treated as purely historical — it is deprecated, its own contamination-prevention mechanism is known to have failed, and there is no clean, metric-consistent aggregate worth comparing across models today. If you need what BIG-Bench was trying to provide, use its distillate **BIG-Bench Hard** (itself now saturated) or a fresher successor rather than the original 204-task suite; a suite-level BIG-Bench number was never reliable for model comparison to begin with, since the task mix and per-task weighting dominate the headline figure regardless of contamination.

### Performance snapshot

No performance timeline is recorded for this benchmark. The suite's normalized aggregate mixes heterogeneous per-task metrics, and no cleanly-sourced, metric-consistent model time series exists to plot on a single composite axis — per-task scores are the meaningful unit for BIG-Bench, so the timeline is intentionally left empty rather than populated with an approximated figure.

> A suite-level number hides enormous per-task variance: tasks range from a handful to thousands of examples, and from trivial to near-impossible. Never compare models on the BIG-Bench aggregate alone — the task mix and weighting dominate the headline figure.

### Source

Paper: ["Beyond the Imitation Game: Quantifying and extrapolating the capabilities of language models"](https://arxiv.org/abs/2206.04615) (TMLR 2023). Dataset: [github.com/google/BIG-bench](https://github.com/google/BIG-bench). No leaderboards recorded.

---

*Benchmark catalog — Reasoning domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
