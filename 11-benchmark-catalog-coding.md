# Benchmark Catalog — Coding

> Whether a model can write code that actually works — from isolated functions to
> real repository-level engineering tasks involving multi-file changes, terminal
> workflows, and long-horizon debugging.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

---

## Table of Contents

1. [At a Glance](#1-at-a-glance)
2. [FrontierCode](#2-frontiercode)
3. [SWE-bench Pro](#3-swe-bench-pro)
4. [Terminal-Bench](#4-terminal-bench)
5. [MBPP+](#5-mbpp)
6. [HumanEval](#6-humaneval)
7. [HumanEval+](#7-humaneval-1)
8. [LiveCodeBench](#8-livecodebench)
9. [MBPP](#9-mbpp)
10. [SWE-bench Verified](#10-swe-bench-verified)
11. [CodeContests](#11-codecontests)
12. [CodeXGLUE](#12-codexglue)
13. [CyberSecEval](#13-cyberseceval)
14. [Mercury](#14-mercury)
15. [MultiPL-E](#15-multipl-e)
16. [SWE-bench](#16-swe-bench)
17. [SWE-Lancer](#17-swe-lancer)

---

## 1. At a Glance

| Benchmark | Status | Launch Date | Dataset Size | Primary Metric | Contamination Risk |
| --- | --- | --- | --- | --- | --- |
| [FrontierCode](#2-frontiercode) | active | 8 Jun 2026 | 150 | pass rate on FrontierCode 1.1 Main (%) | low |
| [SWE-bench Pro](#3-swe-bench-pro) | active | 19 Sep 2025 | 731 | resolved instances on the public set (%) | medium |
| [Terminal-Bench](#4-terminal-bench) | active | 7 Nov 2025 | 89 | task accuracy on Terminal-Bench 2.1 (%) | high |
| [MBPP+](#5-mbpp) | nearing-saturation | 24 Nov 2023 | 378 | MBPP+ pass@1 (%) | high |
| [HumanEval](#6-humaneval) | saturated | 7 Jul 2021 | 164 | pass@1 (%) | high |
| [HumanEval+](#7-humaneval-1) | saturated | 2 May 2023 | 164 | HumanEval+ pass@1 (%) | high |
| [LiveCodeBench](#8-livecodebench) | saturated | 12 Mar 2024 | 1,055 | pass@1 on code generation (%) | medium |
| [MBPP](#9-mbpp) | saturated | 16 Aug 2021 | 500 | pass@1 on the 500-task test split (%) | high |
| [SWE-bench Verified](#10-swe-bench-verified) | saturated | 13 Aug 2024 | 500 | resolved instances (%) | high |
| [CodeContests](#11-codecontests) | deprecated | 2 Feb 2022 | 165 | 10@100k solve rate on the 165-problem test split (%) | high |
| [CodeXGLUE](#12-codexglue) | deprecated | 9 Feb 2021 | Not published | overall smoothed BLEU on six-language CodeSearchNet code summarization | medium |
| [CyberSecEval](#13-cyberseceval) | deprecated | 7 Dec 2023 | Not published | average textual prompt-injection success rate (%) — lower is safer | high |
| [Mercury](#14-mercury) | deprecated | 12 Feb 2024 | 256 | Beyond with 5 sampled solutions per task (%) | high |
| [MultiPL-E](#15-multipl-e) | deprecated | 17 Aug 2022 | 2,898 | pass@1 by programming language (%) | high |
| [SWE-bench](#16-swe-bench) | deprecated | 10 Oct 2023 | 2,294 | resolved instances (%) | high |
| [SWE-Lancer](#17-swe-lancer) | deprecated | 17 Feb 2025 | 198 | pass@1 accuracy on the 198-task Diamond offline subset (%) | medium |

---

## 2. FrontierCode

*Private maintainer-authored repository tasks graded for mergeability: correctness, tests, scope discipline, style, and code quality.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 8 Jun 2026 |
| Dataset Size | 150 (Extended); Main subset used for the headline metric is 100 tasks |
| Primary Metric | pass rate on FrontierCode 1.1 Main (%) |
| Human Baseline | Not published |
| Contamination Risk | low |
| License | Not published |
| Maintainer | Cognition |

**What it measures.** FrontierCode is a **coding** benchmark with a secondary **agentic tool-use** dimension: real open-source repository maintainers each write one task brief for their own repository, and a model-driven agent must produce a change that a human reviewer (or rubric) would actually merge. Grading is not just "does it pass tests" — it is a weighted **mergeability** rubric covering correctness, test coverage, scope discipline, coding style, and overall code quality, with any failed "blocker" criterion zeroing the trial. This makes it a repo-level, agentic evaluation format closer to real pull-request review than to isolated function synthesis.

**Evaluation protocol.** Each task is run **agentically** (multi-step, tool-using) with **tools enabled**, one task brief per repository, at **maximum reasoning effort**, averaged over **five trials per task**; temperature and a fixed pass@k are not published. As covered in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md), this kind of agentic, multi-trial protocol trades reproducibility for realism — the harness is Cognition's own hosted evaluation, so replicating a published number outside Cognition's infrastructure is not possible with public tooling.

**Status and saturation.** FrontierCode is **active**: version 1.1 is newly launched and, per the official evidence, Cognition's own results still show "substantial headroom" on both the Main and Extended sets — no saturation date or successor exists yet. Its lineage has no parent; it defines two named variants, **FrontierCode Main** and **FrontierCode Extended**.

**Contamination risk.** Risk is rated **low**: the dataset is gated and not publicly released (`public_test_set: false`), tasks and solutions remain private, and FrontierCode 1.1 actively zeroes out any agent run detected accessing solution-bearing sources during its permitted documentation search. This is a meaningfully stronger contamination posture than almost every other benchmark in this catalog.

**Why it matters for an AI engineer.** Because the test set is private and gated, a reported FrontierCode score is harder to game via memorization than a public benchmark — but it is also harder to independently audit, since the harness lives entirely inside Cognition's infrastructure and the maintainer is itself a coding-agent vendor. Treat vendor-reported rows with more scrutiny than independently-reported ones (the timeline below shows both), and use FrontierCode as one data point on real-world mergeability rather than a sole basis for model selection, especially while it is this new.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| SWE-1.6 | Cognition | 7 Apr 2026 | 9.4 | vendor-reported |
| GPT-5.5 | OpenAI | 23 Apr 2026 | 43.0 | independent |
| Claude Opus 4.8 | Anthropic | 28 May 2026 | 46.5 | independent |
| SWE-1.7 | Cognition | 8 Jul 2026 | 42.3 | vendor-reported |

> Main contains 100 tasks and Extended 150; scores average five trials at the best reasoning effort. Never mix Main pass rate with the weighted mergeability score or the Extended subset — they are separate axes.

**Source.** No paper is published for FrontierCode. Dataset/leaderboard: [Cognition FrontierCode](https://cognition.com/frontiercode) (official leaderboard).

---

## 3. SWE-bench Pro

*Long-horizon repository tasks across public, held-out, and commercial codebases, with a continuing public model leaderboard.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 19 Sep 2025 |
| Dataset Size | 731 (public split) |
| Primary Metric | resolved instances on the public set (%) |
| Human Baseline | Not published |
| Contamination Risk | medium |
| License | MIT (benchmark code/metadata; source repositories retain their own licenses) |
| Maintainer | Scale AI |

**What it measures.** SWE-bench Pro is a **coding** benchmark with secondary **agentic tool-use** and **long-context** dimensions, built as the direct child of SWE-bench (`lineage.parent: swe-bench`). Where the original SWE-bench draws from 12 Python repositories, SWE-bench Pro spans **public, held-out, and commercial** codebases and is explicitly designed around **long-horizon** software-engineering tasks — multi-file changes requiring hours-to-days of professional effort, not single-function patches. It scores **resolved instances** on a 731-task public split under a common SWE-agent scaffold, with a separate 276-task private **commercial** split over proprietary startup repositories.

**Evaluation protocol.** Run **zero-shot**, **agentic**, with **tools enabled** and **pass@1** scoring; temperature is not published. The harness is the official [SWE-bench Pro repository](https://github.com/scaleapi/SWE-bench_Pro-os) (`swe_bench_pro_eval.py`). As with other agentic coding benchmarks, the reported number is only comparable to another number run under the identical agent scaffold, since scaffold choice materially changes what an underlying model can accomplish.

**Status and saturation.** SWE-bench Pro is **active**: it retains a maintained public leaderboard, and the 3 Aug 2026 LLM Stats snapshot shows a substantial score spread (roughly 58–80% across 17 leading models) — evidence of real remaining discrimination, "despite documented task-quality concerns." No saturation date or successor is recorded. Its lineage parent is SWE-bench, and it defines three variants: **public**, **held-out**, and **commercial**.

**Contamination risk.** Risk is rated **medium**: public and held-out OSS repositories use strong-copyleft licenses as a legal (not technical) deterrent against training inclusion, and the commercial split draws on genuinely private proprietary code. Neither mechanism is a hard technical guarantee against leakage, but the private commercial split in particular meaningfully reduces exposure relative to a fully public benchmark.

**Why it matters for an AI engineer.** The benchmark's own `statistical_note` flags that a 2026 audit found roughly a 30% broken-task rate on SWE-bench Pro — this should dominate how much confidence you place in any single score, well beyond ordinary sampling noise. Use SWE-bench Pro's relative ranking across models cautiously, prefer independently-reported numbers over vendor-reported ones where both exist for the same model tier, and treat the August 2026 LLM Stats snapshot scores (a cross-sectional comparison, not launch-day evaluations) as one moment in an evolving leaderboard rather than a permanent ranking.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Qwen3 32B + SWE-agent (public, launch revision) | Alibaba | 19 Sep 2025 | 3.4 | independent |
| GPT-5 + SWE-agent (public, launch revision) | OpenAI | 19 Sep 2025 | 23.3 | independent |
| Claude Fable 5 (LLM Stats snapshot) | Anthropic | 3 Aug 2026 | 80.0 | vendor-reported |
| GLM-5.1 (LLM Stats snapshot) | Zhipu AI | 3 Aug 2026 | 58.4 | vendor-reported |

> The five launch-era points are dated to individual model launches; the 17 newest points share one date (3 Aug 2026) because they are a single cross-model LLM Stats snapshot, not a historical trend — don't read that cluster as simultaneous releases.

**Source.** Paper: ["SWE-Bench Pro: Can AI Agents Solve Long-Horizon Software Engineering Tasks?"](https://arxiv.org/abs/2509.16941). Dataset: [ScaleAI/SWE-bench_Pro](https://huggingface.co/datasets/ScaleAI/SWE-bench_Pro). Leaderboards: [official public](https://labs.scale.com/leaderboard/swe_bench_pro_public), [official commercial](https://labs.scale.com/leaderboard/swe_bench_pro_private), [LLM Stats](https://llm-stats.com/benchmarks/swe-bench-pro).

---

## 4. Terminal-Bench

*Eighty-nine realistic terminal tasks graded in containers; version 2.1 repairs 2.0's dependency, resource, and specification defects.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 7 Nov 2025 |
| Dataset Size | 89 |
| Primary Metric | task accuracy on Terminal-Bench 2.1 (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Apache-2.0 |
| Maintainer | Terminal-Bench team / Harbor Framework |

**What it measures.** Terminal-Bench is a **coding** benchmark with a secondary **agentic tool-use** dimension, purpose-built around the command-line-and-system-administration slice of software engineering: 89 realistic terminal tasks graded inside containers, spanning shell, Python, C, C++, Rust, and JavaScript. It is a direct descendant of the original Terminal-Bench 1 (`lineage.parent: terminal-bench-1`), with version 2.1 fixing dependency, resource, and task-specification defects found in 2.0.

**Evaluation protocol.** Run **zero-shot** and **agentic**, with **tools enabled** and **pass@1** scoring; temperature is not published. The official leaderboard requires **at least five public trials per task**, run through the [Harbor](https://github.com/harbor-framework/harbor) framework. Because scores are reported per **agent/model pair** (e.g. "Claude Code + Fable 5"), the run configuration includes not just the underlying model but the specific coding-agent harness wrapped around it — two rows with the same model but a different agent are not comparable.

**Status and saturation.** Terminal-Bench is **active**: its verified 17-entry leaderboard spans 58.7%–83.8%, real headroom remains, and Terminal-Bench 3.0 is still in development. No saturation date or successor is recorded. Its variants are **Terminal-Bench 2.0** and **Terminal-Bench 2.1**; 2.1 changes 28 of the 89 task definitions relative to 2.0, so cross-version comparisons are explicitly non-comparable.

**Contamination risk.** Risk is rated **high**: task repositories carry a canary string asking training pipelines to exclude the benchmark data, and 2.1 adds continuous dependency and oracle validation — but neither mechanism prevents a model from having memorized the public task definitions and container setups.

**Why it matters for an AI engineer.** Terminal-Bench is one of the few benchmarks in this catalog testing genuine multi-step terminal/system-administration workflows rather than single-shot code generation, which makes it a good complement to SWE-bench-style repository patching. But given the high contamination risk and the agent/model-pair reporting convention, an engineer should compare scores only within the same agent scaffold and treat the leaderboard as a snapshot of that scaffold's ceiling, not a pure measure of the underlying model.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Claude Code + GLM-5.1 (max; TB 2.1) | Z-AI | 1 May 2026 | 58.7 | independent |
| Codex + GPT-5.5 (xhigh; TB 2.1) | OpenAI | 1 May 2026 | 83.1 | independent |
| Claude Code + Fable 5 (xhigh; TB 2.1) | Anthropic | 7 Jun 2026 | 83.8 | independent |
| Codex + GPT-5.6 Terra (max; TB 2.1) | OpenAI | 11 Jul 2026 | 78.4 | independent |

> With only 89 tasks, a single task flip changes accuracy by roughly 1.12 points, and a 50% pass rate carries a nominal 95% interval near ±10.4 points — small score gaps between agent/model pairs are not reliably meaningful.

**Source.** Paper: ["Terminal-Bench: Benchmarking Agents on Hard, Realistic Tasks in Command Line Interfaces"](https://arxiv.org/abs/2601.11868) (ICLR 2026). Dataset: [harbor-framework/terminal-bench-2-1](https://github.com/harbor-framework/terminal-bench-2-1). Leaderboard: [Terminal-Bench 2.1 official](https://www.tbench.ai/leaderboard/terminal-bench/2.1).

---

## 5. MBPP+

*A cleaned 378-task MBPP evaluation with roughly 35 times more tests for stricter Python functional correctness.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | nearing-saturation |
| Launch Date | 24 Nov 2023 |
| Dataset Size | 378 |
| Primary Metric | MBPP+ pass@1 (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Apache-2.0 (EvalPlus repository) |
| Maintainer | EvalPlus team |

**What it measures.** MBPP+ is a **coding** benchmark and a direct child of MBPP (`lineage.parent: mbpp`), produced by the same EvalPlus project responsible for HumanEval+. It re-evaluates a cleaned 378-task subset of MBPP's entry-level Python synthesis problems against roughly **35 times more unit tests** than the original three-assertion protocol, so the task format is still short function-level synthesis — the difference is entirely in test rigor, designed to catch programs that pass MBPP's sparse checks while remaining subtly wrong.

**Evaluation protocol.** Run **3-shot**, **direct** (no chain-of-thought scaffolding), at **temperature 0**, with **pass@1** scoring and **no tools**; the harness is [EvalPlus](https://github.com/evalplus/evalplus). This is a classic closed-ended, deterministic-decoding protocol as described in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md) — greedy decoding for reproducibility, few-shot prompting to fix the output format.

**Status and saturation.** MBPP+ is **nearing-saturation**: the 13 Jul 2026 BenchmarkList snapshot tops out at 80.2% pass@1, and the combination of a small public task set, provider-sourced scoring, and dataset-version sensitivity limits clean frontier discrimination going forward. No saturation date or successor is recorded yet. It has no further variants of its own.

**Contamination risk.** Risk is rated **high**: expanded tests make shallow overfitting harder to fake, but both the inherited MBPP prompts and the MBPP+ tests themselves are public and have been static since release, so a model's training data may well include the exact problems and their reference solutions.

**Why it matters for an AI engineer.** MBPP+ is a stricter re-grading of an old, exposed dataset, and it is now approaching the same fate as MBPP itself — treat vendor- and third-party-reported scores as a rough sanity check that a model can write short, correct Python, not as a differentiator between frontier models. Prefer a benchmark still rated `active` for that purpose.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| DeepSeek-Coder-V2-Instruct | DeepSeek | 17 Jun 2024 | 75.1 | vendor-reported |
| O1 Preview (Sept 2024) | OpenAI | 12 Sep 2024 | 80.2 | vendor-reported |
| Gemini 1.5 Pro 002 | Google | 24 Sep 2024 | 74.6 | vendor-reported |
| Qwen2.5-Coder-32B-Instruct | Alibaba | 12 Nov 2024 | 77.0 | vendor-reported |

> At 90% over 378 tasks the rough 95% binomial interval is about ±3.0 points, and a pre-v0.3.0 cleanup that removed 21 broken tasks shifted expected scores by roughly four points — dataset-version identity matters as much as the model.

**Source.** Paper: ["Is Your Code Generated by ChatGPT Really Correct? Rigorous Evaluation of Large Language Models for Code Generation"](https://arxiv.org/abs/2305.01210) (NeurIPS 2023; EvalPlus framework, MBPP+ added later). Dataset: [evalplus/mbppplus](https://huggingface.co/datasets/evalplus/mbppplus). Leaderboards: [official EvalPlus](https://evalplus.github.io/leaderboard.html), [BenchmarkList snapshot](https://benchmarklist.com/benchmarks/mbpp/).

---

## 6. HumanEval

*OpenAI's 164 hand-written Python function-synthesis tasks, graded by executing generated completions against unit tests.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 7 Jul 2021 |
| Dataset Size | 164 |
| Primary Metric | pass@1 (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT |
| Maintainer | OpenAI |

**What it measures.** HumanEval is a **coding** benchmark and the foundational function-level code-generation test in this catalog: 164 hand-written Python problems, each with a docstring prompt and a hidden set of unit tests, requiring the model to synthesize a complete function body from scratch. It is the parent of both **HumanEval+** and **MultiPL-E** in this catalog's lineage graph, making it the common ancestor of the whole "translate a function-synthesis prompt into working code" family.

**Evaluation protocol.** Run **zero-shot**, **direct**, at **temperature 0.2**, scored as **pass@1** (with pass@10 and pass@100 also defined), and with **no tools**. Harnesses: [OpenAI human-eval](https://github.com/openai/human-eval) and [EvalPlus](https://github.com/evalplus/evalplus). Note the non-zero sampling temperature — unlike the deterministic greedy protocols used by MBPP+ and HumanEval+, HumanEval's original pass@1 protocol samples stochastically, so exact reproduction requires many samples and an unbiased pass@k estimator, not a single greedy run.

**Status and saturation.** HumanEval is **saturated** as of **2 May 2023**: modern coding models routinely cluster near the ceiling on this small public set, and EvalPlus itself showed the original sparse tests accept materially incorrect programs — which is precisely why its named **successor, HumanEval+**, exists. It has no parent; its variants are **HumanEval+** and **MultiPL-E**.

**Contamination risk.** Risk is rated **high**: the authors hand-wrote the problems specifically to avoid direct overlap with contemporaneous training data, but the prompts, tests, and canonical solutions have been fully public since 2021 — five years of exposure by the time of this catalog's snapshot.

**Why it matters for an AI engineer.** A HumanEval pass@1 number today tells you almost nothing about relative model quality — it is a saturated, long-exposed, 164-item benchmark that current frontier and near-frontier models alike ace. Treat any HumanEval score you see quoted as a legacy/marketing artifact rather than a basis for choosing between models; use HumanEval+ or a currently `active` benchmark instead.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Codex-12B (original paper) | OpenAI | 7 Jul 2021 | 28.81 | vendor-reported |
| GPT-4 (EvalPlus Table 3, greedy) | OpenAI | 2 May 2023 | 88.4 | independent |
| Qwen2.5-Coder-32B-Instruct (EvalPlus, greedy) | Alibaba | 12 Nov 2024 | 92.7 | vendor-reported |

> With only 164 binary tasks, a score near 90% carries a rough 95% binomial interval of about ±4.6 points before even accounting for sampling and prompt variance — the gap between two near-ceiling scores is frequently within noise.

**Source.** Paper: ["Evaluating Large Language Models Trained on Code"](https://arxiv.org/abs/2107.03374). Dataset: [openai/human-eval](https://github.com/openai/human-eval). Leaderboard: [EvalPlus](https://evalplus.github.io/leaderboard.html).

---

## 7. HumanEval+

*HumanEval's 164 Python tasks re-evaluated with roughly 80 times more tests to expose plausible but incorrect programs.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 2 May 2023 |
| Dataset Size | 164 |
| Primary Metric | HumanEval+ pass@1 (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Apache-2.0 (EvalPlus repository) |
| Maintainer | EvalPlus team |

**What it measures.** HumanEval+ is a **coding** benchmark and a direct child of HumanEval (`lineage.parent: humaneval`): the same 164 Python function-synthesis prompts, but graded against roughly **80 times more generated tests** than the original, specifically to catch programs that satisfy HumanEval's sparse assertions while still being wrong on edge cases. The task format is unchanged — this is purely a test-suite-rigor upgrade over the same problem set.

**Evaluation protocol.** Run **zero-shot**, **direct**, at **temperature 0** (greedy, unlike the base HumanEval's 0.2), scored as **pass@1**, with **no tools**, via the [EvalPlus](https://github.com/evalplus/evalplus) harness. It also defines a paired **HumanEval base pass@1** variant on the same completions, which is useful for directly measuring how much a model's score drops once the stricter test suite is applied.

**Status and saturation.** HumanEval+ is **saturated** as of **12 Sep 2024**: the official EvalPlus leaderboard reports o1-preview and o1-mini both at 89.0% pass@1, and with only 164 fully public prompts, the remaining score gaps between top systems are too small and too exposure-prone to support a reliable frontier ranking. No successor is recorded (unlike HumanEval, whose successor is HumanEval+ itself). Its only defined variant is **HumanEval+ mini**.

**Contamination risk.** Risk is rated **high**: the additional tests make shallow overfitting harder to fake, but the prompts, canonical solutions, and even the generated plus-tests are public and have been versioned rather than continuously refreshed since 2023.

**Why it matters for an AI engineer.** HumanEval+ was a genuine improvement over HumanEval's test rigor, but the underlying prompt set is the same 164 items — once frontier models cleared 87–89%, as this timeline shows happening by late 2024, the extra test rigor stopped mattering for differentiation. Read HumanEval+ scores as confirmation that a model handles basic Python function synthesis competently, not as a ranking signal among current frontier models.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4 (May 2023) | OpenAI | 2 May 2023 | 79.3 | independent |
| GPT-4-Turbo (Nov 2023) | OpenAI | 6 Nov 2023 | 81.7 | independent |
| o1-preview (Sept 2024) | OpenAI | 12 Sep 2024 | 89.0 | independent |
| Qwen2.5-Coder-32B-Instruct | Alibaba | 12 Nov 2024 | 87.2 | independent |

> Only 164 task-level outcomes are averaged; near 90% pass@1, the rough 95% binomial interval is about ±4.6 points — separate again from evaluator-version and generation variance across the 15 leaderboard rows this timeline draws from.

**Source.** Paper: ["Is Your Code Generated by ChatGPT Really Correct? Rigorous Evaluation of Large Language Models for Code Generation"](https://arxiv.org/abs/2305.01210) (NeurIPS 2023). Dataset: [evalplus/humanevalplus](https://huggingface.co/datasets/evalplus/humanevalplus). Leaderboard: [official EvalPlus](https://evalplus.github.io/leaderboard.html).

---

## 8. LiveCodeBench

*Continuously updated contest problems for time-windowed code generation, execution, test prediction, and self-repair evaluation.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 12 Mar 2024 |
| Dataset Size | 1,055 |
| Primary Metric | pass@1 on code generation (%) |
| Human Baseline | Not published |
| Contamination Risk | medium |
| License | MIT |
| Maintainer | LiveCodeBench team |

**What it measures.** LiveCodeBench is a **coding** benchmark built to be "contamination free" by design: it continuously collects competitive-programming problems from LeetCode, AtCoder, and Codeforces **after** they are published, and lets evaluators restrict comparisons to a date window newer than a given model's training cutoff. Beyond plain **code generation**, its named variants cover **code execution**, **test-output prediction**, and **self-repair** — a broader slice of the coding pipeline than pure function synthesis.

**Evaluation protocol.** Run **zero-shot**, **direct**, at **temperature 0.2**, scored as **pass@1** (pass@5 also reported), with **no tools**, via the [LiveCodeBench runner](https://github.com/LiveCodeBench/LiveCodeBench) (`--scenario codegeneration`). Because the dataset is a **continuous** refresh cycle rather than a static file, scores are only comparable when the leaderboard snapshot, release/date window, scenario, and test-strength settings all match — an unusual amount of protocol detail to pin down relative to a static benchmark.

**Status and saturation.** LiveCodeBench is **saturated** as of **2 Aug 2026**: in that day's leaderboard snapshot, Qwen3.7 Max reaches 91.6%, Qwen3.7 Plus 89.6%, and GLM-4.7 84.9% — a leading cluster too tight to reliably rank frontier systems. No successor is recorded, and it has no parent; its variants are the **code generation**, **code execution**, **test-output prediction**, and **self-repair** scenarios.

**Contamination risk.** Risk is rated **medium** — notably lower than the static function-synthesis benchmarks in this catalog — precisely because problems are collected post-publication and date-window filtering lets an evaluator hold a model to only genuinely unseen problems. That design intent is real, but it depends on evaluators actually applying the date filter correctly.

**Why it matters for an AI engineer.** LiveCodeBench's rolling-collection design was meant to solve the contamination problem that plagues HumanEval and MBPP, and its medium risk rating (versus high elsewhere in this catalog) reflects that it largely succeeded — but competitive-programming skill has simply caught up: the leading cluster is now saturated too. If you use LiveCodeBench, insist on knowing the exact date window and leaderboard snapshot behind any score, and recognize that recency does not by itself guarantee headroom once the whole field converges near the ceiling.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| DeepSeek V3 (open weight) | DeepSeek | 26 Dec 2024 | 37.6 | independent |
| GLM-4.7 (open weight) | Z-AI | 22 Dec 2025 | 84.9 | independent |
| Qwen3.7 Max (closed) | Alibaba | 20 May 2026 | 91.6 | independent |
| Qwen3.7 Plus (closed) | Alibaba | 26 May 2026 | 89.6 | independent |

> All six leaderboard scores in this snapshot belong to the 2 Aug 2026 date, not to each model's individual evaluation date; only the `date` column reflects each model's own launch date.

**Source.** Paper: ["LiveCodeBench: Holistic and Contamination Free Evaluation of Large Language Models for Code"](https://arxiv.org/abs/2403.07974) (ICLR 2025). Dataset: [livecodebench](https://huggingface.co/livecodebench). Leaderboard: [LiveCodeBench official](https://livecodebench.github.io/leaderboard.html).

---

## 9. MBPP

*Mostly Basic Programming Problems: 974 crowd-sourced Python synthesis tasks intended for entry-level programmers.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 16 Aug 2021 |
| Dataset Size | 500 (standard test split; full dataset is 974 tasks) |
| Primary Metric | pass@1 on the 500-task test split (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | CC-BY-4.0 |
| Maintainer | Google Research |

**What it measures.** MBPP is a **coding** benchmark of 974 crowd-sourced, entry-level Python programming problems, each pairing a short natural-language description with a handful of reference assertions. The metric convention that made it famous is the **500-task test split** carved out of the full 974-task set, evaluated with **pass@1**. It is the parent of **MBPP+** in this catalog's lineage graph, and also has an **MBPP-sanitized** variant.

**Evaluation protocol.** The original paper reports **3-shot**, **direct** prompting; modern harnesses vary this. Scoring is **pass@1** with **no tools**; temperature is not published. Harnesses: [Google Research's MBPP data](https://github.com/google-research/google-research/tree/master/mbpp) and [EvalPlus](https://github.com/evalplus/evalplus). The dataset also supports an **any-sample solve rate** scaling-law variant (from the original paper's large-sample analyses) which is *not* pass@1 and must not be conflated with the headline metric.

**Status and saturation.** MBPP is **saturated** as of **24 Nov 2023**: CodeSOTA's 2026 MBPP pass@1 registry clusters multiple frontier and open-code models between 89% and 95%, and the short public tasks plus three-test protocol are too weak for current frontier comparison. Its named **successor is MBPP+**, which raises the test-suite bar on a cleaned subset of the same problems.

**Contamination risk.** Risk is rated **high**: the original paper found little overlap with its own pretraining corpus at launch, but all prompts, reference code, and assertions are now public and static — over four years of exposure by this catalog's snapshot date.

**Why it matters for an AI engineer.** MBPP is legacy at this point: saturated since late 2023, with current frontier models packed into a narrow 89–95% band that reflects the test suite's weakness more than genuine capability differences. Its child, MBPP+, is a somewhat stronger check (still nearing-saturation rather than fully saturated), but neither should anchor a serious frontier-model comparison today.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| DeepSeek-Coder-V2-Instruct | DeepSeek | 14 Jun 2024 | 89.4 | independent |
| o3-mini | OpenAI | 31 Jan 2025 | 93.3 | independent |
| o4-mini | OpenAI | 16 Apr 2025 | 94.9 | independent |
| Claude Opus 4 | Anthropic | 22 May 2025 | 92.0 | independent |

> At 90% over 500 tasks, the rough 95% binomial interval is about ±2.6 points — narrower than the smaller HumanEval-family benchmarks, but the CodeSOTA registry scores still come from provider-sourced snapshots rather than a single controlled run.

**Source.** Paper: ["Program Synthesis with Large Language Models"](https://arxiv.org/abs/2108.07732). Dataset: [google-research-datasets/mbpp](https://huggingface.co/datasets/google-research-datasets/mbpp). Leaderboards: [CodeSOTA](https://www.codesota.com/benchmark/mbpp), [EvalPlus](https://evalplus.github.io/leaderboard.html).

---

## 10. SWE-bench Verified

*A 500-task, engineer-reviewed subset of SWE-bench that became the standard repository-level coding-agent test.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated |
| Launch Date | 13 Aug 2024 |
| Dataset Size | 500 |
| Primary Metric | resolved instances (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT (benchmark code; source repositories retain their own licenses) |
| Maintainer | SWE-bench team / OpenAI Preparedness |

**What it measures.** SWE-bench Verified is a **coding** benchmark, with secondary **agentic tool-use** and **long-context** dimensions, built as a **500-task, human-reviewed subset** of the original SWE-bench (`lineage.parent: swe-bench`) — three professional developers screened each candidate task for issue clarity and test quality before inclusion. The task format is unchanged from SWE-bench: real GitHub issues, solved by producing a patch that must satisfy the repository's own `FAIL_TO_PASS` and `PASS_TO_PASS` test sets, i.e. genuine repository-level bug fixing rather than isolated function synthesis.

**Evaluation protocol.** Run **zero-shot**, **agentic**, with **tools enabled** and **pass@1** scoring; temperature is not published. The harness is the [official SWE-bench harness](https://github.com/SWE-bench/SWE-bench) (`SWE-bench_Verified` task flag). It has no further sub-variants — Verified is itself the "cleaned" variant of the base SWE-bench.

**Status and saturation.** SWE-bench Verified is **saturated** as of **17 Feb 2026**: under the standardized mini-SWE-agent 2.0.0 protocol, the leading results clustered tightly at 76.8%, 75.8%, 75.8%, and 75.6% — not enough separation to discriminate among frontier systems, on top of the contamination and task-quality concerns discussed below.

**Contamination risk.** Risk is rated **high**: despite the human-review pass that produced Verified, the underlying issues, gold patches, and full repository histories remain public and static, so nothing about the curation process prevents training-data exposure.

**Why it matters for an AI engineer.** SWE-bench Verified was, for roughly a year and a half, the closest thing this domain had to a trusted, standardized agentic-coding benchmark — its human-curation step genuinely fixed real problems in the original SWE-bench (underspecified tasks, tests that rejected valid fixes). But as of February 2026 it has joined the saturated tier: use it for coarse tiering (is a model competent at real GitHub-issue resolution at all?) rather than for ranking two frontier models against each other, and prefer SWE-bench Pro or Terminal-Bench, both still `active`, for that finer comparison.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o (Agentless) | OpenAI | 13 Aug 2024 | 33.2 | vendor-reported |
| Claude 3.5 Sonnet (upgraded; Anthropic tools scaffold) | Anthropic | 22 Oct 2024 | 49.0 | vendor-reported |
| Claude 4.5 Opus (high reasoning; mini-SWE-agent 2.0.0) | Anthropic | 17 Feb 2026 | 76.8 | independent |
| Gemini 3 Pro (mini-SWE-agent 2.0.0) | Google | 26 Feb 2026 | 69.6 | independent |

> At 500 items, a 50% resolve rate carries an approximate 95% binomial interval of ±4.4 points; repository clustering, flaky test environments, and known test defects make a simple binomial interval optimistic about true uncertainty.

**Source.** Paper: ["SWE-bench: Can Language Models Resolve Real-World GitHub Issues?"](https://arxiv.org/abs/2310.06770) (ICLR 2024 Oral). Dataset: [SWE-bench/SWE-bench_Verified](https://huggingface.co/datasets/SWE-bench/SWE-bench_Verified). Leaderboard: [official SWE-bench](https://www.swebench.com).

---

## 11. CodeContests

*DeepMind's competitive-programming corpus with temporally split problems, human submissions, and generated correctness tests.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 2 Feb 2022 |
| Dataset Size | 165 |
| Primary Metric | 10@100k solve rate on the 165-problem test split (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | CC-BY-4.0 (dataset) |
| Maintainer | Google DeepMind (repository archived 6 Dec 2024) |

**What it measures.** CodeContests is a **coding** benchmark with a secondary **reasoning** dimension, built by DeepMind for the AlphaCode line of work: competitive-programming problems with a **temporal train/test split**, paired with human submissions and machine-generated correctness tests. Its headline metric, **10@100k**, reflects the AlphaCode pipeline specifically — sample 100,000 candidate programs per problem, filter by example tests, cluster by observed behavior, and submit up to 10 clustered candidates, counting a problem solved if any submission passes.

**Evaluation protocol.** Run **zero-shot**, **direct**, with **pass@10** scoring (in the specific 10-submission-after-massive-sampling sense above) and **no tools**; temperature is not published. The harness is the [official CodeContests evaluator](https://github.com/google-deepmind/code_contests). This heavy-sampling protocol is unusual among the benchmarks in this catalog and is not directly comparable to a simple pass@1 or pass@10 number from a different pipeline.

**Status and saturation.** CodeContests is **deprecated**: its repository was archived on 6 Dec 2024, the temporal holdout has been fully public since 2022, and later AlphaCode systems moved to updated, non-public data — so it no longer supports a clean, maintained frontier comparison. It has no parent and one variant, **CodeContests+**.

**Contamination risk.** Risk is rated **high**: the original temporal split placed training data strictly before the validation/test problems, but the complete released split has been public since 2022, and that clean window has long since expired.

**Why it matters for an AI engineer.** CodeContests is historical at this point — an archived repository with a public, years-old test split and a bespoke, hard-to-reproduce 10@100k sampling pipeline. Its main present-day value is as a case study in how a well-designed temporal split degrades once the holdout itself becomes public; don't use it for current model comparisons.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| AlphaCode 9B (no clustering) | DeepMind | 2 Feb 2022 | 25.8 | vendor-reported |
| AlphaCode 41B (no clustering) | DeepMind | 2 Feb 2022 | 27.7 | vendor-reported |
| AlphaCode 41B + clustering | DeepMind | 2 Feb 2022 | 29.6 | vendor-reported |

> The 165-problem test set gives wide intervals for solve rate, and the 10@100k pipeline itself introduces sampling, filtering, clustering, and execution variance on top of that — the AlphaCode-vs-human comparison in the original paper used an incompatible Codeforces contest-percentile metric (top 54.3%) that cannot be plotted on this solve-rate axis.

**Source.** Paper: ["Competition-Level Code Generation with AlphaCode"](https://arxiv.org/abs/2203.07814) (Science 2022). Dataset: [google-deepmind/code_contests](https://github.com/google-deepmind/code_contests). No leaderboard listed.

---

## 12. CodeXGLUE

*A broad code-intelligence suite spanning understanding, retrieval, completion, translation, repair, generation, and summarization.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 9 Feb 2021 |
| Dataset Size | Not published (suite spans 14 component datasets) |
| Primary Metric | overall smoothed BLEU on six-language CodeSearchNet code summarization |
| Human Baseline | Not published |
| Contamination Risk | medium |
| License | C-UDA for datasets; MIT for repository code |
| Maintainer | Microsoft |

**What it measures.** CodeXGLUE is a **coding** benchmark that is really a **suite**: ten tasks across 14 datasets spanning code understanding, retrieval, completion, translation, repair, generation, and summarization, across a wide span of languages (Python, Java, JavaScript, PHP, Ruby, Go, C#, C, C++, plus English, Chinese, and Japanese natural-language pairings). There is no official common aggregate across the ten tasks; this catalog's one chosen chart axis — **six-language CodeSearchNet code summarization**, scored by smoothed BLEU — is a stable, representative slice, not the whole suite.

**Evaluation protocol.** Run under **task-specific fine-tuning or inference** (there is no single zero-shot protocol across ten heterogeneous tasks), **direct**, with **no tools**; temperature and pass@k are not applicable. The harness is the [official CodeXGLUE repository](https://github.com/microsoft/CodeXGLUE). Individual component tasks are scored with a wide range of metrics — accuracy, F1, MAP, MRR, exact match, BLEU, CodeBLEU, perplexity, and edit similarity — so any cross-task summary requires picking one metric family, as this catalog does.

**Status and saturation.** CodeXGLUE is **deprecated**: the official challenge and repository remain available, but the heterogeneous task-specific leaderboard is no longer a current frontier-code comparison and has no maintained common aggregate. It has no parent and no defined variants.

**Contamination risk.** Risk is rated **medium**: official test labels were withheld for challenge submission (`public_test_set: false`), which is a real mitigation, but most source corpora and all training/dev material are public, and each of the 14 component datasets needs its own separate leakage analysis rather than one blanket answer.

**Why it matters for an AI engineer.** CodeXGLUE predates the current era of frontier LLM coding evaluation and was designed for fine-tuned, task-specific models rather than general-purpose instruction-following models — there is no single number to quote, and the suite has no maintained aggregate to compare against. Treat it as a historical reference for code-intelligence sub-task definitions (translation, repair, summarization) rather than a benchmark to run today.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Seq2Seq | independent | 9 Feb 2021 | 14.32 | independent |
| Transformer | independent | 9 Feb 2021 | 15.56 | independent |
| RoBERTa encoder | Meta | 9 Feb 2021 | 16.57 | independent |
| CodeBERT encoder | Microsoft | 9 Feb 2021 | 17.83 | vendor-reported |

> There is no statistically coherent suite-wide score for CodeXGLUE — compare systems only within the same dataset, split, preprocessing, and metric; the four rows above are all from the same paper table (six-language overall smoothed BLEU) and are not a general-purpose leaderboard.

**Source.** Paper: ["CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation"](https://arxiv.org/abs/2102.04664) (ESEC/FSE 2021). Dataset: [microsoft/CodeXGLUE](https://github.com/microsoft/CodeXGLUE). Leaderboard: [official CodeXGLUE](https://microsoft.github.io/CodeXGLUE/).

---

## 13. CyberSecEval

*Meta's evolving suite for insecure code, cyberattack assistance, prompt injection, exploitation, SOC analysis, and autopatching.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 7 Dec 2023 |
| Dataset Size | Not published |
| Primary Metric | average textual prompt-injection success rate (%) — lower is safer |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT for Purple Llama evals/benchmarks; bundled third-party components may differ |
| Maintainer | Meta Purple Llama, with partner datasets including CrowdStrike CyberSOCEval |

**What it measures.** CyberSecEval is primarily a **coding** benchmark with secondary **safety-alignment**, **agentic tool-use**, and **vision-multimodal** dimensions — an unusually broad reach for one suite. It bundles secure-code-generation checks (does the model produce insecure patterns across instruction and autocomplete contexts?), a **prompt-injection success rate** axis (this catalog's chosen chart metric, where lower is safer), and CyberSOCEval multiple-choice tasks for malware analysis and threat-intelligence reasoning. The task format therefore spans everything from static code review to agentic cyber-range interaction.

**Evaluation protocol.** Run under **sub-benchmark-specific** prompting, **agentic**, with **tools enabled**; temperature and pass@k are not applicable across such a heterogeneous suite. Harnesses: the official [Purple Llama CybersecurityBenchmarks](https://github.com/meta-llama/PurpleLlama/tree/main/CybersecurityBenchmarks) repository and the [Inspect Evals](https://github.com/UKGovernmentBEIS/inspect_evals) `cyberseceval_4` task. Prompt-injection success is graded by an **LLM judge**, so comparability additionally depends on which judge model and guardrail configuration were used — a source of grading variance this catalog's other, unit-test-scored coding benchmarks don't have.

**Status and saturation.** CyberSecEval is **deprecated**: the original standalone benchmark is no longer the recommended version, having been superseded through CyberSecEval 2 and 3 to CyberSecEval 4, with tasks and metrics changing at each step rather than one comparable score axis persisting. Its named **successor is CyberSecEval 4**; further variants include **CyberSecEval 2**, **CyberSecEval 3**, **CyberSOCEval**, and **AutoPatchBench**.

**Contamination risk.** Risk is rated **high**: versioned additions have broadened coverage over time, but most prompts and labels remain public, and while agentic cyber-range tasks add live-environment variation, evaluators still need to version their data carefully and keep any private cases genuinely separate.

**Why it matters for an AI engineer.** If security behavior on code-adjacent tasks matters for your use case, evaluate against **CyberSecEval 4** (the named successor), not the original — the underlying methodology has moved on and the original's tasks and metrics are no longer the recommended comparison point. Remember, too, that lower is better on the prompt-injection axis: a "higher score" here is the opposite of good, unlike almost every other benchmark in this catalog.

**Performance snapshot** *(lower is safer)*

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| gpt-3.5-turbo | OpenAI | 1 Mar 2023 | 39.13 | vendor-reported |
| gpt-4 | OpenAI | 14 Mar 2023 | 19.87 | vendor-reported |
| UN codellama-70b-instruct | Meta | 29 Jan 2024 | 12.93 | vendor-reported |
| llama 3 8b-instruct | Meta | 18 Apr 2024 | 45.27 | vendor-reported |

> This structured axis is BenchmarkList's seven-model textual prompt-injection snapshot sampled 27 May 2026; timeline dates are each model's official launch date, not the evaluation date. Other CyberSecEval modules (secure code generation, CyberSOCEval multiple-choice) are separate axes and must not be averaged with this one.

**Source.** Paper: ["Purple Llama CyberSecEval: A Secure Coding Benchmark for Language Models"](https://arxiv.org/abs/2312.04724) (arXiv:2312.04724). Dataset: [meta-llama/PurpleLlama](https://github.com/meta-llama/PurpleLlama/tree/main/CybersecurityBenchmarks). Leaderboard: [BenchmarkList CyberSecEval](https://benchmarklist.com/benchmarks/cyberseceval/).

---

## 14. Mercury

*Python code-synthesis benchmark that rewards both functional correctness and runtime efficiency against real solution distributions.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 12 Feb 2024 |
| Dataset Size | 256 |
| Primary Metric | Beyond with 5 sampled solutions per task (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Not published |
| Maintainer | Mercury authors (Elfsong/Mercury) |

**What it measures.** Mercury is a **coding** benchmark that adds a dimension most function-synthesis benchmarks in this catalog ignore: **runtime efficiency**, not just correctness. Its 256 LeetCode-derived Python tasks are graded with the **Beyond** metric, which weights a passing solution by where its runtime falls in the percentile distribution of real accepted human submissions — a functionally incorrect program earns zero credit regardless of speed, but a correct-but-slow one scores lower than a correct-and-fast one.

**Evaluation protocol.** Run as **zero-shot code synthesis**, **direct**, scored as **pass@1**-style correctness feeding into the Beyond weighting, with **no tools**; temperature is not published. The harness is the [Mercury official harness](https://github.com/Elfsong/Mercury). Because Beyond depends on runtime percentiles measured against a reference solution distribution, comparability additionally requires matching hardware, sandbox, test generators, timeout policy, and sample count — a heavier reproducibility burden than a pure pass/fail metric.

**Status and saturation.** Mercury is **deprecated**: it is a static 256-task public evaluation with no official maintained leaderboard or refreshed test set. The benchmark's methodology remains useful for efficiency-focused research, but its 2024 launch-era snapshot is no longer a frontier comparison. It has no parent; its variants are **Mercury-train** and **Mercury-eval**.

**Contamination risk.** Risk is rated **high**: the authors deduplicated collected solutions and use generated tests as a mitigation, but the underlying tasks derive from public LeetCode problems, and the released evaluation data are static.

**Why it matters for an AI engineer.** Mercury's efficiency-aware framing is a genuinely useful idea if you care about generated code's runtime, not just its correctness — but with no maintained leaderboard and a static, high-risk public test set, there's no current mechanism to get a comparable, trustworthy score for a model you're evaluating today. Treat it as a source of methodology rather than a live comparison tool.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| CodeLlama-34B base | Meta | 24 Aug 2023 | 42.4 | independent |
| DeepSeek-Coder-33B base | DeepSeek | 2 Nov 2023 | 48.53 | independent |
| StarCoder2-15B base | BigCode | 28 Feb 2024 | 49.17 | independent |
| CodeQwen1.5-7B base | Qwen | 18 Apr 2024 | 47.78 | independent |

**Source.** Paper: ["Mercury: A Code Efficiency Benchmark for Code Large Language Models"](https://arxiv.org/abs/2402.07844) (NeurIPS 2024 Datasets and Benchmarks Track). Dataset: [Elfsong/Mercury](https://huggingface.co/datasets/Elfsong/Mercury). No leaderboard listed.

---

## 15. MultiPL-E

*Parallel HumanEval and MBPP translations for comparing code generation across many programming languages.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 17 Aug 2022 |
| Dataset Size | 2,898 |
| Primary Metric | pass@1 by programming language (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Not published |
| Maintainer | Northeastern Programming Research Laboratory (NUPRL) |

**What it measures.** MultiPL-E is a **coding** benchmark and a direct child of HumanEval (`lineage.parent: humaneval`): it mechanically translates HumanEval and MBPP's Python prompts and tests into many other programming languages, so the same underlying problems can be used to compare a model's code-generation ability **across languages** rather than just within Python. Its variants split the translated sets into **MultiPL-HumanEval** and **MultiPL-MBPP**.

**Evaluation protocol.** Run **zero-shot completion**, **direct**, at **temperature 0.2**, scored as **pass@1** (pass@k also defined), with **no tools**. Harnesses: the [MultiPL-E evaluation framework](https://github.com/nuprl/MultiPL-E) and the [BigCode Evaluation Harness](https://github.com/bigcode-project/bigcode-evaluation-harness) (`multiple-<language>` tasks). Because the item count depends on which source benchmark and which language subset is selected, reporting must specify the source set, language, dataset revision, and sample count — averaging across languages can hide large per-language gaps.

**Status and saturation.** MultiPL-E is **deprecated**: the original MultiPL-HumanEval axis is a static translation of 161 public HumanEval tasks and inherits HumanEval's exposure and ceiling, and the official repository now points readers toward Ag-LiveCodeBench-X for a more challenging multilingual evaluation. No successor is formally recorded in this catalog, but its variants are **MultiPL-HumanEval** and **MultiPL-MBPP**.

**Contamination risk.** Risk is rated **high**: parallel translation reduces direct Python-string overlap with training data, but it preserves the public HumanEval/MBPP problem semantics and tests — a model that memorized the Python original can often solve the translated version too. Held-out or live multilingual tasks give stronger leakage control.

**Why it matters for an AI engineer.** MultiPL-E's core idea — translate a well-known benchmark into many languages to test multilingual coding ability — remains sound methodology, but the specific dataset inherits all of HumanEval's contamination exposure and is now explicitly superseded by the maintainers' own recommendation to use a newer benchmark. If multilingual coding ability matters for your evaluation, look for that successor rather than running MultiPL-E itself.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| InCoder-6.7B (MultiPL-HumanEval Python) | Meta | 17 Aug 2022 | 15.2 | independent |
| code-davinci-002 (MultiPL-HumanEval Python) | OpenAI | 17 Aug 2022 | 47.0 | independent |
| SantaCoder-1.1B (MultiPL-HumanEval Python) | BigCode | 9 Jan 2023 | 18.0 | vendor-reported |

**Source.** Paper: ["MultiPL-E: A Scalable and Extensible Approach to Benchmarking Neural Code Generation"](https://arxiv.org/abs/2208.08227) (IEEE Transactions on Software Engineering, 2023). Dataset: [nuprl/MultiPL-E](https://huggingface.co/datasets/nuprl/MultiPL-E). No leaderboard listed.

---

## 16. SWE-bench

*Real GitHub issues from 12 Python repositories, solved by generating patches that must pass repository tests.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 10 Oct 2023 |
| Dataset Size | 2,294 |
| Primary Metric | resolved instances (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT (benchmark code; source repositories retain their own licenses) |
| Maintainer | SWE-bench team |

**What it measures.** SWE-bench is the original **coding** benchmark in what is now this catalog's largest lineage, with secondary **agentic tool-use** and **long-context** dimensions: 2,294 real GitHub issues drawn from 12 Python repositories, where a model must generate a patch that makes the repository's `FAIL_TO_PASS` tests pass while keeping its `PASS_TO_PASS` tests passing. It established the repository-level, agentic patch-generation task format that **SWE-bench Verified** and **SWE-bench Pro** both build on.

**Evaluation protocol.** Run **zero-shot**, **agentic**, with **tools enabled** and **pass@1** scoring; temperature is not published. The harness is the [official SWE-bench harness](https://github.com/SWE-bench/SWE-bench). Evaluation starts from a historical base commit for each issue, so the agent must operate against a real, frozen codebase state rather than an isolated prompt.

**Status and saturation.** SWE-bench is **deprecated**: SWE-bench Verified superseded the original test set in 2024 after human review found underspecified tasks and tests that rejected otherwise-valid fixes. Its named **successor is SWE-bench Verified**; other variants are **SWE-bench Lite**, **SWE-bench Multimodal**, and **SWE-bench Multilingual**.

**Contamination risk.** Risk is rated **high**: evaluation starts from a historical base commit as a partial mitigation, but the issues, merged pull requests, gold patches, and tests are all public in the repositories' own history.

**Why it matters for an AI engineer.** SWE-bench established the repository-level agentic coding task format that dominates this catalog's active benchmarks, but the original test set itself has known task-quality problems that its own successor was built to fix — don't quote a raw "SWE-bench" score today without checking whether it means the original 2,294-task set or the cleaner Verified subset, and prefer Verified (or, for headroom, SWE-bench Pro) for any current comparison.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Claude 2 (BM25 retrieval) | Anthropic | 10 Oct 2023 | 1.97 | independent |
| Claude 3 Opus (BM25 retrieval) | Anthropic | 4 Mar 2024 | 3.79 | independent |
| GPT-4 Turbo + SWE-agent | OpenAI | 6 May 2024 | 12.47 | independent |

> At 2,294 items, a 50% resolve rate has an approximate 95% binomial interval of ±2.0 points; task and repository clustering makes even that interval optimistic about true uncertainty.

**Source.** Paper: ["SWE-bench: Can Language Models Resolve Real-World GitHub Issues?"](https://arxiv.org/abs/2310.06770) (ICLR 2024 Oral). Dataset: [SWE-bench/SWE-bench](https://huggingface.co/datasets/SWE-bench/SWE-bench). Leaderboard: [official SWE-bench](https://www.swebench.com).

---

## 17. SWE-Lancer

*Current 198-task offline patch benchmark derived from paid Upwork work, with historical mixed implementation and manager-task variants.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 17 Feb 2025 |
| Dataset Size | 198 (current offline Diamond subset) |
| Primary Metric | pass@1 accuracy on the 198-task Diamond offline subset (%) |
| Human Baseline | Not published |
| Contamination Risk | medium |
| License | MIT (evaluation code) |
| Maintainer | OpenAI Frontier Evals |

**What it measures.** SWE-Lancer is a **coding** benchmark with secondary **agentic tool-use** and **reasoning** dimensions, built from real paid Upwork freelance software-engineering work — tasks carry actual historical dollar payouts. The current maintained leaderboard uses a **198-task offline "Diamond" subset** of individual-contributor (IC) tasks, each graded end-to-end by the task's own tests; this is narrower than the original paper's 502-task Diamond set, which additionally mixed in 265 **manager proposal-choice** tasks not present in the current offline leaderboard.

**Evaluation protocol.** Run **zero-shot**, **agentic**, with **tools enabled** and **pass@1** scoring; temperature is not published. The harness is [OpenAI Frontier Evals](https://github.com/openai/preparedness) (`project/swelancer`). A separate **earned payout** variant sums the historical dollar value attached to solved tasks on the same 198-task subset, and an **original Diamond overall accuracy** variant reflects the historical 502-task mixed IC-plus-manager protocol — explicitly not comparable to the current offline leaderboard's accuracy number.

**Status and saturation.** SWE-Lancer is **deprecated**: its maintained leaderboard has been frozen since July 2025, and the underlying dataset overloads "Diamond" across two incompatible protocols (502 mixed tasks in the original paper versus 198 offline IC tasks on the current leaderboard) — an unlabeled Diamond score could mean either one. It has no parent; its variants are **SWE-Lancer Diamond (offline)**, **SWE-Lancer Diamond (original)**, and **SWE-Lancer Full**.

**Contamination risk.** Risk is rated **medium**: the benchmark uses a fixed historical codebase, and since July 2025 it removes live internet dependencies from execution — a real mitigation against certain leakage vectors — but the public Diamond tasks and code are nevertheless exposed.

**Why it matters for an AI engineer.** SWE-Lancer's dollar-denominated framing is a distinctive, economically grounded way to think about coding-agent value, but the frozen July 2025 leaderboard and the 502-vs-198-task Diamond ambiguity mean any score you encounter needs careful labeling before it means anything. If you see a bare "SWE-Lancer Diamond" number, confirm which of the two incompatible protocols it used before trusting a comparison.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o (198-task Diamond offline) | OpenAI | 17 Jul 2025 | 8.1 | vendor-reported |
| o1 (198-task Diamond offline) | OpenAI | 17 Jul 2025 | 28.4 | vendor-reported |

**Source.** Paper: ["SWE-Lancer: Can Frontier LLMs Earn $1 Million from Real-World Freelance Software Engineering?"](https://arxiv.org/abs/2502.12115). Dataset: [openai/preparedness (project/swelancer)](https://github.com/openai/preparedness/tree/main/project/swelancer). Leaderboard: [official SWE-Lancer](https://swelancer.github.io/leaderboard/).

---

*Benchmark catalog — Coding domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
