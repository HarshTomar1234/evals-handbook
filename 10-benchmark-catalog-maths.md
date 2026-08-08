# Benchmark Catalog — Maths

> Whether a model can perform accurate symbolic and numerical reasoning — from
> grade-school arithmetic word problems to research-level mathematics — where the final
> answer is usually simply correct or incorrect, leaving little room for a plausible-
> sounding but wrong response.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

---

## Table of Contents

1. [At a Glance](#1-at-a-glance)
2. [FrontierMath](#2-frontiermath)
3. [MathArena](#3-matharena)
4. [OlympiadBench](#4-olympiadbench)
5. [PutnamBench](#5-putnambench)
6. [Soohak](#6-soohak)
7. [AIME](#7-aime)
8. [MGSM](#8-mgsm)
9. [GSM8K](#9-gsm8k)
10. [MATH](#10-math)
11. [TabMWP](#11-tabmwp)
12. [GSM-Hard](#12-gsm-hard)
13. [Omni-MATH](#13-omni-math)

---

## 1. At a Glance

Benchmarks are sorted by status (**active** → **nearing-saturation** → **saturated** →
**deprecated**), alphabetically within each tier.

| Benchmark | Status | Launch Date | Dataset Size | Primary Metric | Contamination Risk |
| --- | --- | --- | --- | --- | --- |
| [FrontierMath](#2-frontiermath) | active | 7 Nov 2024 | 338 | Accuracy — % of problems solved, automated verification | low |
| [MathArena](#3-matharena) | active | 29 May 2025 | Not published (continuously refreshed) | Expected performance across non-deprecated competitions | low |
| [OlympiadBench](#4-olympiadbench) | active | 16 Feb 2024 | 8,476 | Average score across auto-graded and proof items | high |
| [PutnamBench](#5-putnambench) | active | 15 Jul 2024 | 672 (Lean subset) | Problems with a kernel-verified formal proof (count / 672) | medium |
| [Soohak](#6-soohak) | active | 9 May 2026 | 439 | Challenge Avg@3 | low |
| [AIME](#7-aime) | nearing-saturation | 1 Feb 2024 | 30 | Accuracy — exact match on integer answer (000–999) | medium |
| [MGSM](#8-mgsm) | nearing-saturation | 6 Oct 2022 | 250 (per language) | Macro-average exact-answer accuracy across languages | high |
| [GSM8K](#9-gsm8k) | saturated | 27 Oct 2021 | 1,319 | Accuracy — exact match on final numeric answer | high |
| [MATH](#10-math) | saturated | 5 Mar 2021 | 500 (MATH-500) | Accuracy on MATH-500 — math-equivalence on boxed answer | high |
| [TabMWP](#11-tabmwp) | saturated | 29 Sep 2022 | 7,686 | Answer accuracy | high |
| [GSM-Hard](#12-gsm-hard) | deprecated | 17 Jan 2023 | 1,319 | Final-answer accuracy | high |
| [Omni-MATH](#13-omni-math) | deprecated | 20 Sep 2024 | 4,428 | GPT-4o-judged answer accuracy | high |

---

## 2. FrontierMath

*Original, unpublished research-level math problems written by expert mathematicians —
mostly private to resist contamination. Frontier models solved under 2% at launch.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 7 Nov 2024 |
| Dataset Size | 338 |
| Primary Metric | Accuracy — % of problems solved, automated answer verification (%) |
| Human Baseline | Not published |
| Contamination Risk | low |
| License | proprietary (Epoch AI — private held-out set; a small set of sample problems is public) |
| Maintainer | Epoch AI (Glazer et al.), with problems authored by expert mathematicians; evaluations run by Epoch on the private set |

**What it measures.** FrontierMath tests `maths` and `reasoning` at the research frontier:
338 original problems, authored specifically for the benchmark by expert mathematicians
across many advanced fields, that are close to problems a working researcher might spend
hours to days on. Answers are numerical or SymPy-checkable, so grading is automated
equivalence rather than a human judge, and results are reported per tier rather than as one
blended number — a June 2026 v2 release split the set into Tiers 1–3 (295 problems) and a
harder Tier 4 (43 problems), added because progress on the earlier tiers had become
significant.

**Evaluation protocol.** Zero-shot, agentic chain-of-thought, `pass@1`, with tools **on**
(models run with a Python interpreter available). Temperature is not pinned in the public
configuration. As covered in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md),
toggling tools changes what is actually being measured — a FrontierMath number obtained
with Python execution reflects tool-augmented reasoning, not the model's unaided
mathematics. The canonical harness is Epoch AI's own private evaluation pipeline:
[Epoch AI (canonical — runs the private eval)](https://epoch.ai/frontiermath).

**Status and saturation.** Active. At launch, state-of-the-art models solved under 2% of
problems — a benchmark frontier systems almost entirely failed. Reasoning models with
Python tools have since climbed: OpenAI announced o3 at 25.2% (Dec 2024, tools-on), but that
figure is contested — Epoch's own independent reproductions of comparable models came in
far lower, around 11%. The June 2026 v2 release added the harder Tier 4 precisely because
Tiers 1–3 progress had become significant, yet the hardest tier remains far from solved.
There is no `successor`; the only recorded lineage variant is `frontiermath-tier-4`.

**Contamination risk.** The test set is not public (`test_set_public: false`) and the
refresh cycle is static — but "static" here means "deliberately never released," not
"stale." This is the strongest contamination defense in this catalog: the problems are
original and unpublished, only a small sample set is public, and Epoch runs evaluation on a
held-out set that is never released. The privacy is entangled with the funding history — the
dataset's funder, OpenAI, had access to most problems, so Epoch established held-out
subsets the funder cannot see (53 solutions withheld in one version; 20 of 50 Tier 4
problems reserved) as a post-hoc safeguard. In practice, a low-contamination-risk label here
means you can trust that a model has not simply memorized the answer key — but it also means
you cannot independently reproduce the number; you are trusting Epoch's evaluation.

**Why it matters for an AI engineer.** Because the test set is private, do not expect to run
FrontierMath yourself — treat any published score as an Epoch-attributed number, and be
skeptical of vendor-announced figures that Epoch has not itself reproduced (their own o3-mini
check landed at roughly half of OpenAI's announced number). Always note the tier and the
tool/compute setting before comparing two FrontierMath scores — a tools-on, heavy-sampling
run and a tools-off, single-pass run are not the same test. This is currently the top of the
maths-difficulty chain in this catalog (GSM8K → MATH → AIME → FrontierMath), useful for
tracking frontier research-math trajectory rather than for everyday model selection.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Best frontier model (Tiers 1–3, tools-on) | multiple | 7 Nov 2024 | 2% | independent |
| o3-mini high (Epoch independent eval, Tiers 1–3, tools-on) | openai | 28 Feb 2025 | 11% | independent |

> Scores are reported per tier, and the tiers are small (Tier 4 is 43 problems), so per-tier
> figures carry wide confidence intervals — a few problems move the percentage several
> points. Always cite the tier, the tool/compute setting, and that the number is an
> Epoch-run, private-set result.

**Source.** Paper: [FrontierMath: A Benchmark for Evaluating Advanced Mathematical
Reasoning in AI](https://arxiv.org/abs/2411.04872) (Epoch AI, preprint). Dataset:
[epoch.ai/frontiermath](https://epoch.ai/frontiermath). Leaderboard: [Epoch AI (official /
canonical tracker)](https://epoch.ai/frontiermath).

---

## 3. MathArena

*A continuously refreshed platform evaluating models on newly released math competitions,
proofs, research problems, and formalization.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 29 May 2025 |
| Dataset Size | Not published (continuously changing set) |
| Primary Metric | Expected performance across non-deprecated competitions (%) |
| Human Baseline | Not published |
| Contamination Risk | low |
| License | MIT (evaluation repository) |
| Maintainer | ETH Zurich Secure, Reliable, and Intelligent Systems Lab |

**What it measures.** MathArena covers `maths`, `reasoning`, and `vision-multimodal`
secondary capability: it is a live platform, not a fixed dataset, that continuously adds
newly released math competitions, proof-based competitions, research-derived problems, and
formalization tasks. Three metric variants sit under its headline "expected performance"
number: **Final-answer competitions** (four runs per problem scored by competition-specific
exact-answer rules), **Proof-based competitions** (partial credit under competition rubrics
via the platform's own judging pipeline), and **Expected cost** (average API cost per
problem over the current benchmark mix). The headline figure itself is a two-parameter
item-response model fitted to per-run correctness, averaged over questions in the
currently non-deprecated competitions.

**Evaluation protocol.** Zero-shot, agentic, `pass@4` (four sampled runs per problem), tools
off. Temperature is not pinned. The official harness is
[MathArena official harness](https://github.com/eth-sri/matharena), invoked as
`scripts/run.py --comp <competition> --models <model> --n 4`. Because the benchmark's
membership changes continuously, reproducing a specific published number requires pinning
the date and competition mix, not just the run configuration described in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).

**Status and saturation.** Active. The platform continuously adds newly released
competitions, and even as aggregate expected performance rises, fresh proof, visual, and
research tracks preserve measurable headroom. There is no recorded `saturated_date` or
`successor`.

**Contamination risk.** The underlying problems are public (`test_set_public: true`) but the
refresh cycle is continuous: models are evaluated immediately after new competition
problems are released, before they can plausibly enter subsequent training runs. Later
public exposure does not preserve that cleanliness forever — a MathArena score on an older,
now-widely-circulated competition should be read with the same caution as any static public
benchmark for that specific competition.

**Why it matters for an AI engineer.** Because it is independently run (by ETH Zurich, not
self-reported by vendors) and refreshed continuously against genuinely new competitions,
MathArena is one of the more trustworthy live signals of current frontier math ability among
the benchmarks in this catalog. It is less useful as a single, fixed, reproducible number —
always date-stamp any score you cite, since the underlying competition mix (and therefore
the meaning of the aggregate) changes over time.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-5.5 (xhigh, expected performance) | openai | 24 Apr 2026 | 81.3% | independent |
| GPT-5.6-Sol (max, expected performance) | openai | 9 Jul 2026 | 83.1% | independent |

> Each competition is small and benchmark membership changes over time. Use the site's
> uncertainty intervals and date-stamp every aggregate; per-competition scores are more
> interpretable than the cross-platform IRT summary.

**Source.** Paper: [MathArena: Evaluating LLMs on Uncontaminated Math
Competitions](https://arxiv.org/abs/2505.23281) (NeurIPS 2025 Datasets and Benchmarks).
Dataset: [huggingface.co/MathArena](https://huggingface.co/MathArena). Leaderboard:
[Official MathArena platform](https://matharena.ai).

---

## 4. OlympiadBench

*Bilingual Olympiad and Gaokao mathematics and physics problems, including diagrams, open
answers, and proofs.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 16 Feb 2024 |
| Dataset Size | 8,476 |
| Primary Metric | Average score (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT (code repository) |
| Maintainer | OpenBMB |

**What it measures.** OlympiadBench targets `maths` with `reasoning` and
`vision-multimodal` secondary capabilities: 8,476 bilingual (Chinese and English) Olympiad
and Gaokao problems spanning both mathematics and physics, some accompanied by diagrams,
with open-ended answers and full proofs alongside standardized final-answer questions. The
official scoring pipeline separately handles auto-graded, standardized open-ended answers
(checked by an equivalence pipeline) and theorem-proving items (which need qualitative or
sampled assessment rather than final-answer matching).

**Evaluation protocol.** Zero-shot, chain-of-thought, temperature 0, `pass@1`, tools off,
run through the [OlympiadBench official evaluation code](https://github.com/OpenBMB/OlympiadBench)
(`eval` entry point). Because the benchmark blends two grading modes — automated equivalence
for final answers and separate assessment for proofs — a reported "average score" is only
comparable across systems that used the same subset weighting and proof policy.

**Status and saturation.** Active. The official full-benchmark evaluation reports GPT-4o at
25.89% overall, with materially lower physics performance, leaving broad headroom across the
bilingual and multimodal subsets. No `saturated_date` or `successor` is recorded.

**Contamination risk.** The test set is public, refresh cycle static. The authors cleaned and
deduplicated the source exams, but all released questions, images, solutions, and answers
are now public — so nothing structurally prevents future training runs from ingesting the
released dataset. In practice, a rising OlympiadBench score over successive model
generations may partly reflect growing exposure to the public release rather than pure
capability growth.

**Why it matters for an AI engineer.** Given genuine headroom (GPT-4o clears only about a
quarter of the benchmark) but a high contamination risk, OlympiadBench is currently useful
for comparing models on bilingual, multimodal, olympiad-style math and physics reasoning —
but treat dramatic year-over-year gains with some skepticism, and always check the
subject/language/modality slice rather than the blended average, since the aggregate mixes
heterogeneous subjects and grading modes.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4V (full benchmark) | openai | 16 Feb 2024 | 17.97% | independent |
| GPT-4o (full benchmark) | openai | 13 May 2024 | 25.89% | independent |

> The aggregate combines heterogeneous subjects and grading modes. Small score gaps are not
> meaningful unless systems use identical dataset versions, image handling, proof policy, and
> subset weighting.

**Source.** Paper: [OlympiadBench: A Challenging Benchmark for Promoting AGI with
Olympiad-Level Bilingual Multimodal Scientific Problems](https://arxiv.org/abs/2402.14008)
(ACL 2024). Dataset:
[huggingface.co/datasets/Hothan/OlympiadBench](https://huggingface.co/datasets/Hothan/OlympiadBench).
Leaderboard: [Official repository leaderboard](https://github.com/OpenBMB/OlympiadBench).

---

## 5. PutnamBench

*Formal theorem-proving on Putnam problems — models write a complete Lean/Isabelle/Coq
proof, machine-checked by the proof assistant's kernel.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 15 Jul 2024 |
| Dataset Size | 672 (Lean subset, the primary reported configuration) |
| Primary Metric | Problems with a complete, kernel-verified formal proof — Lean subset (count / 672) |
| Human Baseline | Not published |
| Contamination Risk | medium |
| License | Apache-2.0 (Lean 4, Isabelle), MIT (Coq); informal problem statements included with MAA permission |
| Maintainer | George Tsoukalas et al. (UT Austin); repo trishullab/PutnamBench (canonical — leaderboard + results.json) |

**What it measures.** PutnamBench is this catalog's only formal theorem-proving benchmark.
It covers `maths` and `reasoning`, but the grading criterion is categorically different from
every other entry here: not "did you produce the right final number" but "does a proof
assistant's kernel (Lean 4, Isabelle, or Coq) accept a complete formal proof" for a Putnam
competition problem. As of the last update the set totals 1,724 formalizations (672 Lean 4 /
640 Isabelle / 412 Coq) spanning Putnam years 1962–2025 — a living benchmark that grows as
more years are formalized, reported per language and per variant (answer-in-statement vs.
no-answer) rather than blended into one number.

**Evaluation protocol.** Zero-shot, agentic, tools **on**, with `pass@k` left unspecified in
the schema because the budget varies enormously across published results — from a general
model at `pass@1`, to fine-tuned provers sampled at `pass@512`, to special-purpose systems
run for roughly 10 GPU-days per problem. Temperature is not pinned. The canonical harness is
[PutnamBench (official — repo + leaderboard)](https://trishullab.github.io/PutnamBench/leaderboard.html).
As covered in [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md),
sampling budget is one of the biggest levers on a reported score — PutnamBench is the
sharpest illustration of that in this catalog.

**Status and saturation.** Active — the wiki's first formal-proving benchmark, and, at
reasonable budgets, still far from solved. Through most of 2024–2025 the best systems solved
single digits of the Lean set (Goedel-Prover: 7 at `pass@512`; GPT-4o: about 1). General
reasoning models at `pass@1` remain low (GPT-5 with a roughly 10-turn ReAct loop: about
28–42 of ~660, single-digit percent). Heavy-compute systems have since climbed steeply:
ByteDance's Seed-Prover reportedly solved around 581/672 Lean problems at roughly 10
GPU-days per problem, and in January 2026 Logical Intelligence's Aleph claimed 668/672
(99.4%, agentic `pass@1`) — but these use enormous or single-source, unreproduced budgets.
Moving this benchmark to nearing-saturation would require independent reproduction of high
solve rates across languages under disclosed, comparable budgets. Recorded lineage variants:
`minif2f`, `proofnet`.

**Contamination risk.** The test set is public and static, but the contamination profile is
genuinely different from every answer-based maths benchmark in this catalog. The
formalizations are hand-crafted, not scraped text, and the formal proofs themselves are not
published as a solution set the way GSM8K's or MATH's worked solutions are — so the usual
leakage vector is weaker, and the formal-proof requirement makes memorization far less
useful: you cannot shortcut a kernel-checked Lean proof by recalling a memorized informal
argument. Working the other way, the Putnam problem statements are public and decades of
informal solutions circulate widely, which can leak proof strategies. Net effect: medium
risk, toward the low end. In practice this means a PutnamBench solve count is a comparatively
clean capability signal, but not an immaculate one — a model could still have absorbed
proof strategies from public informal solutions.

**Why it matters for an AI engineer.** Never compare a "solved N of 672" figure across
systems without checking the budget behind it — a `pass@1` general model, a `pass@512`
fine-tuned prover, and a 10-GPU-day-per-problem agentic run are not the same achievement,
and the leaderboard itself notes it lacks a good way to signal this. Treat single-source,
unreproduced claims (like Aleph's 99.4%) as pending verification rather than established
fact. This is a genuine signal for formal mathematics and verification capability, not a
general-purpose math leaderboard — use it if formal proof generation is relevant to your
use case, not as a stand-in for ordinary competition math.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o (paper era, Lean, ~1/640) | openai | 15 Jul 2024 | 0.2% | independent |
| Goedel-Prover (Lean, 7/644, pass@512) | princeton | 1 Feb 2025 | 1.1% | independent |
| GPT-5 (Lean, ~42/660, pass@1, ~10-turn ReAct) | openai | 7 Aug 2025 | 6.4% | independent |

> The dominant confound here is not sampling noise but compute/sampling budget: results span
> `pass@10` to beyond `pass@512`, plus GPU-days-per-problem agentic runs, differing by tens
> of percentage points on the same benchmark. A special-purpose system at `pass@512` and a
> general model at `pass@1` solving similar counts are very different achievements.

**Source.** Paper: [PutnamBench: Evaluating Neural Theorem-Provers on the Putnam
Mathematical Competition](https://arxiv.org/abs/2407.11214) (NeurIPS 2024 Datasets &
Benchmarks; ICML 2024 AI4Math Best Paper). Dataset: [trishullab/PutnamBench (GitHub)](https://github.com/trishullab/PutnamBench).
Leaderboard: [PutnamBench leaderboard (official / canonical live tracker)](https://trishullab.github.io/PutnamBench/leaderboard.html).

---

## 6. Soohak

*Fresh research-level mathematics authored by mathematicians, plus unanswerable prompts that
reward justified refusal.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | active |
| Launch Date | 9 May 2026 |
| Dataset Size | 439 |
| Primary Metric | Challenge Avg@3 (%) |
| Human Baseline | Not published |
| Contamination Risk | low |
| License | Not published |
| Maintainer | Soohak organizing team |

**What it measures.** Soohak spans `maths`, `reasoning`, and — unusually for a maths
benchmark — `safety-alignment` as a secondary capability. It has two axes: **Challenge**, 340
fresh research-level math problems authored by mathematicians without AI assistance, graded
by an LLM judge (GPT-5-Mini) and averaged over three samples per item (Avg@3); and
**Refusal**, 99 ill-posed or unanswerable questions that reward a model for correctly
declining to answer rather than confidently guessing. That combination makes Soohak one of
the few maths benchmarks that explicitly scores whether a model knows the limits of what it
can solve. The suite is bilingual (English and Korean).

**Evaluation protocol.** Zero-shot, chain-of-thought, `pass@3`, tools off, temperature not
pinned. Evaluation is not self-serve: the harness is listed as
[Soohak evaluation by request](https://soohak-bench.github.io), meaning scores are obtained
through the organizing team's own submission process rather than run independently against a
public test script.

**Status and saturation.** Active. The official July 2026 tracker reports 64.4% Avg@3 for the
Challenge leader, with 46 of 340 problems still unsolved by any tracked model; Refusal
remains a separate, hard target. No `saturated_date` or `successor` is recorded.

**Contamination risk.** The test set is private (`test_set_public: false`) and static.
Problems were newly authored without AI assistance under confidentiality controls, and the
full collection is embargoed until late 2026, with models evaluated by request rather than
against a public download. That combination — private, embargoed, evaluated by request — is
what earns the low-risk rating: nothing prevents leakage forever, but there is currently no
public copy for a training run to ingest.

**Why it matters for an AI engineer.** Because Soohak is new, private, and evaluated by
request, treat any published score as a snapshot from a specific evaluation run rather than
something you can reproduce on demand — you cannot download the set and score your own model
against it today. It is currently most valuable as a research-level headroom signal and as
one of the only maths benchmarks that scores refusal behavior explicitly; worth watching as
it matures rather than relying on yet for routine model comparisons.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| Gemini 3 Pro (Challenge Avg@3) | google | 9 May 2026 | 30.39% | independent |
| GPT-5.5 high (Challenge Avg@3) | openai | 1 Jul 2026 | 64.4% | independent |

> Challenge has 340 items and Refusal has 99. Three samples reduce decoding noise but do not
> make Avg@3 a single-pass accuracy figure — judge error and subject mix remain material.

**Source.** Paper: [Soohak: A Mathematician-Curated Benchmark for Evaluating Research-level
Math Capabilities of LLMs](https://arxiv.org/abs/2605.09063) (venue not published). Dataset:
[soohak-bench.github.io](https://soohak-bench.github.io). Leaderboard: [Official Soohak
leaderboard](https://soohak-bench.github.io).

---

## 7. AIME

*The MAA's competition exam — 15 integer-answer problems per test, refreshed yearly so each
new exam is briefly contamination-free.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | nearing-saturation |
| Launch Date | 1 Feb 2024 |
| Dataset Size | 30 |
| Primary Metric | Accuracy — every answer is an integer 000–999, exact match (%) |
| Human Baseline | 33 (median AIME score, ≈5 of 15) |
| Contamination Risk | medium |
| License | proprietary (MAA copyright — redistribution restricted) |
| Maintainer | Mathematical Association of America (MAA), administering AIME since 1983; problems compiled and evaluated by the community (MathArena, per-year HuggingFace sets) |

**What it measures.** AIME covers `maths` with `reasoning` as a secondary capability: 15
integer-answer (000–999) problems per exam, 30 across a full year's two sittings, drawn from
the American Invitational Mathematics Examination — combinatorics, geometry, number theory,
and creative pattern recognition at AMC-qualifier competition level, with exact-match grading
against the integer key.

**Evaluation protocol.** Zero-shot, chain-of-thought, `pass@1`, tools off, temperature not
pinned. Because the item count per exam is tiny, headline numbers in practice use `avg@k` or
`maj@64` rather than a single pass — an aggregation detail that matters more here than for
almost any other benchmark in this catalog. Harnesses: [MathArena (canonical live
tracker)](https://matharena.ai) and [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)
(`task_flag: aime`).

**Status and saturation.** Nearing-saturation, with a status genuinely split by the yearly
refresh. Old exams are both saturated and contaminated: reasoning models solved AIME 2024 at
up to ~96.7% (o3), up from GPT-4o's 13.4%. Each fresh exam has a shrinking clean window, but
frontier models now score high even on fresh exams — AIME 2025 sits around 85–91%. So the
rolling benchmark as a whole is nearing-saturation: no single exam discriminates for long, yet
the yearly refresh keeps a briefly-clean signal alive. Recorded lineage variants:
`aime-2024`, `aime-2025`, `aime-2026`, `matharena`.

**Contamination risk.** The test set is public and refreshed yearly. AIME is the clearest
case in this catalog where the refresh cycle *is* the contamination defense: each new exam is
genuinely unseen at release — a real, brand-new competition — giving a brief clean
measurement window. But within months the problems and worked solutions circulate widely
online, and that year's exam becomes contaminated (AIME 2024 is now high-contamination). The
defense is rolling, not permanent, which is why the risk is medium rather than low: fresh
yearly, but decaying fast.

**Why it matters for an AI engineer.** Always check *which year's* exam a reported AIME score
used, and how soon after release it was measured. A high score on an old AIME year mostly
tells you the model has seen that exam; a fresh-year score measured promptly is a more
genuine competition-math signal — though even fresh exams are now cleared at 85–91% by
frontier models, so the discriminating headroom is closing fast. Never compare a small AIME
gap between two models without knowing the sampling protocol (see the statistical note
below) — a one-question difference is noise, not signal.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o (AIME 2024, pass@1) | openai | 12 Sep 2024 | 13.4% | vendor-reported |
| o1 (AIME 2024, pass@1) | openai | 12 Sep 2024 | 74.4% | vendor-reported |
| o3 (AIME 2024, pass@1) | openai | 20 Dec 2024 | 96.7% | vendor-reported |

> This is the sharpest sample-size trap in this catalog. One AIME exam is 15 questions, so
> one question is worth 6.7 points; a full year (30 questions) is 3.3 points per question. A
> "model A beats model B by one question" gap is essentially noise — the entire reason labs
> run heavy sampling (`avg@k` / `maj@64`) instead of a single pass. Never trust a small AIME
> gap, and never compare a single-sample number against an `avg@64` one.

**Source.** No accompanying research paper — AIME's `paper` field is `null` because it is a
competition exam administered by the MAA, not a published research dataset. Dataset:
[matharena.ai](https://matharena.ai). Leaderboards: [MathArena (canonical live
tracker)](https://matharena.ai), [Artificial Analysis (AIME)](https://artificialanalysis.ai/evaluations/aime-2025).

---

## 8. MGSM

*A human-translated 250-problem GSM8K subset for comparing grade-school mathematical
reasoning across ten languages.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | nearing-saturation |
| Launch Date | 6 Oct 2022 |
| Dataset Size | 250 (per language) |
| Primary Metric | Macro-average exact-answer accuracy across languages (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Apache-2.0 (repository) |
| Maintainer | Google Research |

**What it measures.** MGSM applies `maths` and `reasoning` across languages: 250 GSM8K-style
grade-school word problems, human-translated into ten languages (Bengali, Chinese, French,
German, Japanese, Russian, Spanish, Swahili, Telugu, Thai), scored as exact-match accuracy
per language and then macro-averaged. It tests whether grade-school arithmetic reasoning
transfers across languages rather than being an English-only artifact. Two protocol
variants exist: **native-language chain of thought** (question, exemplars, reasoning cue,
and response all in the target language) and **English chain of thought** (the
target-language question solved via English exemplars or English reasoning).

**Evaluation protocol.** Few-shot exemplars were the original protocol, though the
maintained harness also supports zero-shot direct and chain-of-thought variants;
chain-of-thought, temperature 0, `pass@1`, tools off. Harnesses:
[lm-evaluation-harness (native-language CoT)](https://github.com/EleutherAI/lm-evaluation-harness/tree/main/lm_eval/tasks/mgsm)
(`task_flag: mgsm_cot_native`) and the direct variant (`task_flag: mgsm_direct`).

**Status and saturation.** Nearing-saturation. The small, static translations are broadly
handled by modern multilingual models, while large per-language gaps and prompt sensitivity
still make MGSM diagnostically useful; newer perturbation-based successors test robustness
more directly. Parent benchmark: `gsm8k`.

**Contamination risk.** The test set is public and static. Human translation reduces exact
English-string overlap with the source, but the underlying GSM8K questions, all ten
translations, targets, and exemplars are fully public. In practice, a high MGSM score
partly reflects the same public-exposure risk as GSM8K itself, just spread across ten
languages rather than eliminated by translation.

**Why it matters for an AI engineer.** MGSM is genuinely useful for spotting uneven
multilingual training — a model strong on English GSM8K but weak in, say, Swahili or Telugu
reveals a real gap worth knowing about. But because the underlying items are public and
static, do not treat the aggregate score as a clean capability estimate; use the per-language
breakdown rather than the macro-average, and expect some inflation from exposure, the same
way GSM8K's numbers are inflated.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| PaLM 540B (translate-to-English CoT) | google | 6 Dec 2023 | 74.7% | vendor-reported |

> Each language has only 250 paired items: near 50% accuracy, the per-language 95% binomial
> interval is about ±6.2 points. Cross-language errors are also correlated, because every
> language translates the same underlying problems.

**Source.** Paper: [Language Models are Multilingual Chain-of-Thought
Reasoners](https://arxiv.org/abs/2210.03057) (ICLR 2023). Dataset:
[google-research/url-nlp (mgsm)](https://github.com/google-research/url-nlp/tree/main/mgsm).
No leaderboard is listed for this benchmark.

---

## 9. GSM8K

*8.5K grade-school math word problems (2–8 arithmetic steps) — the default elementary-math
benchmark and the classic chain-of-thought demonstration.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated (since 1 Jan 2024) |
| Launch Date | 27 Oct 2021 |
| Dataset Size | 1,319 |
| Primary Metric | Accuracy — exact match on the final numeric answer (%) |
| Human Baseline | Not published (informal target only — see below) |
| Contamination Risk | high |
| License | MIT |
| Maintainer | OpenAI (Cobbe et al.); repo openai/grade-school-math, dataset openai/gsm8k |

**What it measures.** GSM8K covers `maths` with `reasoning` as a secondary capability: 8.5K
grade-school arithmetic word problems (1,319 in the standard test split) requiring 2–8 steps,
which demand converting natural language into a sequence of arithmetic operations. It is the
benchmark most associated with demonstrating that chain-of-thought prompting helps
mathematical reasoning, and remains the default first stop for elementary math evaluation.

**Evaluation protocol.** 5-shot, chain-of-thought, temperature 0, `pass@1`, tools off.
Harnesses: [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)
(`task_flag: gsm8k`) and the [official GSM8K repository](https://github.com/openai/grade-school-math).
This is the reference configuration for the "8-shot, chain-of-thought" example used
throughout [Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md)
to illustrate how prompt construction moves a score.

**Status and saturation.** Saturated since 1 January 2024. Frontier models cleared roughly
95% by 2023–24, and the reasoning-model era pushed scores to about 97–99%, collapsing
headroom. But this is the sharpest "saturated ≠ solved" case in this catalog: GSM-Symbolic
(Apple, 2024) showed that changing only names and numbers — and especially adding one
irrelevant clause (GSM-NoOp) — drops accuracy heavily across models, so the *score* is
saturated while the underlying *capability* (robust arithmetic reasoning) is not. The
discriminating signal moved to MATH, then AIME, then FrontierMath, and to robustness
variants. Recorded lineage variants: `gsm-hard`, `gsm-symbolic`, `gsm-plus`,
`gsm8k-platinum`.

**Contamination risk.** The test set is public and static, with no mitigation. It has been
public since 2021, MIT-licensed, and mirrored across the web complete with worked
natural-language solutions — ideal memorization fodder. This is structurally the same
exposure that undid MMLU, and it is the direct reason the GSM-Symbolic and GSM8K-Platinum
variants exist: to test whether a score survives when the exact public items are perturbed
or cleaned. In practice, a near-100% GSM8K score today reflects both genuine arithmetic
competence and years of public exposure — you cannot separate the two from the score alone.

**Why it matters for an AI engineer.** This is legacy territory for model comparison: do not
use GSM8K to differentiate frontier models, since everyone clusters at 95–99%+. Be
specifically suspicious of a headline GSM8K number as a proxy for "reliable arithmetic
reasoning," given GSM-Symbolic's finding that trivial rewordings swing accuracy by tens of
points on the same model. If grade-school arithmetic robustness matters for your product,
test with a perturbation-style variant or your own eval set instead of citing a GSM8K score.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-3 175B (fine-tuned) | openai | 27 Oct 2021 | 35% | vendor-reported |
| PaLM 540B (8-shot CoT) | google | 28 Jan 2022 | 56.9% | independent |
| PaLM 540B (CoT + self-consistency) | google | 21 Mar 2022 | 74.4% | independent |
| GPT-4 (5-shot CoT) | openai | 14 Mar 2023 | 92% | vendor-reported |

> At 1,319 test problems, the 95% confidence interval near 95% accuracy is only about ±1.2
> points, so at the saturated frontier ordinary model gaps are within noise. But the dominant
> caveat here is not sampling error — it is robustness: GSM-Symbolic shows the same model's
> accuracy swinging by tens of points under trivial rewordings, which overstates reasoning
> reliability far more than the confidence interval suggests. Scores are also not comparable
> across protocols (direct vs. CoT vs. `maj@k`).

**Source.** Paper: [Training Verifiers to Solve Math Word Problems](https://arxiv.org/abs/2110.14168)
(OpenAI, preprint). Dataset:
[huggingface.co/datasets/openai/gsm8k](https://huggingface.co/datasets/openai/gsm8k).
Leaderboard: [Papers with Code (GSM8K)](https://paperswithcode.com/sota/arithmetic-reasoning-on-gsm8k).

---

## 10. MATH

*12,500 AMC/AIME-level competition problems with worked solutions — usually evaluated on its
500-problem MATH-500 subset, the reasoning-model math standard.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated (since 1 Sep 2024) |
| Launch Date | 5 Mar 2021 |
| Dataset Size | 500 (MATH-500 subset) |
| Primary Metric | Accuracy on MATH-500 — math-equivalence match on the boxed answer (%) |
| Human Baseline | 90 (IMO gold medalist reference; range 40–90, see below) |
| Contamination Risk | high |
| License | MIT |
| Maintainer | Dan Hendrycks et al. (UC Berkeley); dataset hendrycks/competition_math. MATH-500 subset & PRM800K: OpenAI (Lightman et al.), repo openai/prm800k |

**What it measures.** MATH covers `maths` with `reasoning` as a secondary capability:
12,500 AMC/AIME-level competition problems across 7 subjects and 5 difficulty levels, each
with a full worked solution. The standard headline configuration is the 500-problem MATH-500
subset, where the model's final `\boxed{}` answer is graded by mathematical equivalence
rather than by grading the reasoning itself.

**Evaluation protocol.** Zero-shot, chain-of-thought, temperature 0, `pass@1`, tools off.
Harnesses: [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)
(`task_flag: hendrycks_math / minerva_math`) and the official
[MATH-500 / PRM800K subset](https://github.com/openai/prm800k).

**Status and saturation.** Saturated since 1 September 2024. MATH resisted far longer than
GSM8K — GPT-4 managed only about 42% on the full set in 2023 — but the reasoning-model era
ended it: o1 reached about 94.8% on MATH-500 later that year, and subsequent models sit in
the 90s, so it no longer discriminates at the frontier. As with GSM8K, saturated does not
mean solved: exact-answer scoring hides whether the reasoning was valid, and headroom has
moved to AIME and FrontierMath. Recorded lineage variants: `math-500`, `prm800k`.

**Contamination risk.** The test set is public and static, with no mitigation, and the
exposure is compounded. MATH has been public since 2021, MIT-licensed, with full worked
solutions mirrored across the web; the 2023 PRM800K release additionally put step-level
solutions to MATH *training* problems into wide circulation as process-supervision data.
MATH-500's existence is itself partly a response to this — it is drawn to avoid the
train/PRM overlap and to keep grading tractable. Near-ceiling scores should be read as
memorization-inclusive.

**Why it matters for an AI engineer.** Like GSM8K, MATH is now legacy for frontier
comparisons — o1-class models were already near 95% by late 2024. It is useful mainly as a
historical reference for how quickly reasoning models closed a gap that had resisted for
three years, not as a current differentiator; for competition-level math discrimination
today, use AIME or FrontierMath instead. Also remember the human baseline here is a *range*,
not a point (a three-time IMO gold medalist scored 90%, a math-averse CS PhD student scored
about 40%) — "human-level" claims relative to MATH need to specify which human.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-3 175B (full MATH) | openai | 5 Mar 2021 | 5.6% | independent |
| GPT-4 (full MATH) | openai | 14 Mar 2023 | 42.5% | vendor-reported |
| o1 (MATH-500) | openai | 12 Sep 2024 | 94.8% | vendor-reported |

> MATH-500 is 500 problems, so the 95% confidence interval near 90% accuracy is about ±2.5
> points. The 7 subjects × 5 difficulty levels enable fine-grained analysis, but any single
> level-by-subject cell is tiny (tens of problems) — never compare models on "Level-5
> Geometry" alone. At the saturated frontier, aggregate gaps under a few points are noise; the
> bigger issue is equivalence-checker disagreement between systems.

**Source.** Paper: [Measuring Mathematical Problem Solving With the MATH Dataset](https://arxiv.org/abs/2103.03874)
(NeurIPS 2021 Datasets & Benchmarks). Dataset:
[huggingface.co/datasets/hendrycks/competition_math](https://huggingface.co/datasets/hendrycks/competition_math).
Leaderboard: [Papers with Code (MATH)](https://paperswithcode.com/sota/math-word-problem-solving-on-math).

---

## 11. TabMWP

*Grade-level math word problems that require combining a question with structured, textual,
or image-rendered tables.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | saturated (since 19 Apr 2023) |
| Launch Date | 29 Sep 2022 |
| Dataset Size | 7,686 |
| Primary Metric | Answer accuracy (%) |
| Human Baseline | 90.22 (official test-set estimate) |
| Contamination Risk | high |
| License | MIT |
| Maintainer | PromptPG authors |

**What it measures.** TabMWP combines `maths` and `reasoning` with `vision-multimodal` as a
secondary capability: grade-level word problems that require reading a semi-structured table
and combining it with a question to arrive at the correct arithmetic answer, across
free-text and multiple-choice answer types. Two input variants isolate different skills:
**Image-table input** renders the table as an image, adding table perception to the
reasoning task, while **Text-GT input** supplies the gold table text directly, isolating pure
reasoning from OCR and table recognition.

**Evaluation protocol.** 2-shot in the original GPT-3 PromptPG result (later leaderboard
protocols vary), chain-of-thought, temperature 0, `pass@1`, tools off. Harness: the
[PromptPG reference implementation](https://github.com/lupantech/PromptPG)
(`task_flag: data/tabmwp`).

**Status and saturation.** Saturated since 19 April 2023. The official leaderboard reports
Chameleon with GPT-4 at 98.78% on text-ground-truth tables, above the published 90.22% human
estimate — the fixed public test set no longer separates frontier tool-using systems. No
`successor` is recorded.

**Contamination risk.** The test set is public and static, with no mitigation beyond the
fixed train/dev/test splits — all tables, solutions, and test answers are public in the
repository. At 98.78%, current top scores likely reflect a mix of genuine table-grounded
arithmetic reasoning and exposure to the fully public test data; the benchmark can no longer
distinguish frontier models on this dimension.

**Why it matters for an AI engineer.** TabMWP is legacy for model comparison — it topped out
above its own human baseline back in 2023, and the test set has been fully public and static
ever since. If table-grounded math reasoning matters for your product (a genuinely useful
real-world skill), a TabMWP score alone will not differentiate current frontier models; look
for a fresher or held-out variant, or build your own eval around your actual table formats.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-3 + PromptPG (2-shot CoT, Text-GT) | openai | 29 Sep 2022 | 68.23% | vendor-reported |
| Chameleon (GPT-4, Text-GT, tools) | openai | 19 Apr 2023 | 98.78% | independent |

> The test split is large, but errors are clustered by source table and answer format; close
> results must use the same table representation, prompt selection, tools, and parsing
> rules.

**Source.** Paper: [Dynamic Prompt Learning via Policy Gradient for Semi-structured
Mathematical Reasoning](https://arxiv.org/abs/2209.14610) (ICLR 2023). Dataset:
[lupantech/PromptPG (tabmwp data)](https://github.com/lupantech/PromptPG/tree/main/data/tabmwp).
Leaderboard: [Official PromptPG leaderboard](https://github.com/lupantech/PromptPG).

---

## 12. GSM-Hard

*GSM8K test problems with unusually large numerical values, designed to separate reasoning
from easy arithmetic.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 17 Jan 2023 |
| Dataset Size | 1,319 |
| Primary Metric | Final-answer accuracy (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | MIT (Hugging Face dataset card) |
| Maintainer | Reasoning Machines |

**What it measures.** GSM-Hard covers `maths` with `reasoning` as a secondary capability: it
takes the 1,319 GSM8K test problems and replaces their numbers with unusually large values,
aiming to separate genuine multi-step reasoning from easy small-number arithmetic that a
model might get right through pattern-matching. It was originally built to demonstrate
program-aided (code-generating) language models under the PAL framework.

**Evaluation protocol.** Few-shot in the original PAL paper, with configuration varying in
later use; agentic chain-of-thought, temperature 0, `pass@1`, tools **on** (models generate
and execute a program rather than reasoning purely in text). Harness: the
[PAL reference repository](https://github.com/reasoning-machines/pal).

**Status and saturation.** Deprecated. The public 1,319-item transformation remains a useful
arithmetic stress test, but automatic number replacement creates implausible and sometimes
invalid questions, so the benchmark should not be used as a standalone reasoning benchmark.
Parent benchmark: `gsm8k`; no successor is recorded.

**Contamination risk.** The test set is public and static, with no mitigation beyond
replacing GSM8K's numbers — the questions, generated programs, and targets are all public.
Because GSM-Hard inherits GSM8K's years of public exposure and adds its own dataset-validity
problems on top, a GSM-Hard score is doubly unreliable as a capability signal.

**Why it matters for an AI engineer.** Treat GSM-Hard as historical. The benchmark's own
status evidence flags that automatic number substitution can produce implausible or invalid
questions, so a model's score conflates genuine reasoning failures with dataset-generation
artifacts. Do not use it for current model comparisons, and do not build a production eval
around it.

**Performance snapshot.** No performance timeline entries are published for this benchmark.

**Source.** Paper: [PAL: Program-aided Language Models](https://arxiv.org/abs/2211.10435)
(ICML 2023). Dataset:
[huggingface.co/datasets/reasoning-machines/gsm-hard](https://huggingface.co/datasets/reasoning-machines/gsm-hard).
No leaderboard is listed for this benchmark.

---

## 13. Omni-MATH

*A 4,428-problem Olympiad mathematics suite spanning more than 33 subdomains and ten
annotated difficulty levels.*

**Quick Facts**

| Field | Value |
| --- | --- |
| Status | deprecated |
| Launch Date | 20 Sep 2024 |
| Dataset Size | 4,428 |
| Primary Metric | GPT-4o-judged answer accuracy (%) |
| Human Baseline | Not published |
| Contamination Risk | high |
| License | Not published |
| Maintainer | Omni-MATH authors |

**What it measures.** Omni-MATH covers `maths` with `reasoning` as a secondary capability:
4,428 Olympiad-level math problems spanning more than 33 subdomains and ten annotated
difficulty levels, graded by an LLM judge (GPT-4o) for binary answer-equivalence, with
breakdowns available by domain and difficulty. The project also ships **Omni-Judge**, an
8B Llama-3-based judge distilled from 21,451 GPT-4o judgments (reported at roughly 95%
agreement with GPT-4o on an internal set), and a later **rule-based evaluation** path for
answers that can be normalized and verified without a language-model judge at all.

**Evaluation protocol.** Zero-shot, chain-of-thought, temperature 0, `pass@1`, tools off.
Harness: the [Omni-MATH official evaluation repository](https://github.com/KbsdJames/Omni-MATH)
(`GPT_eval/get_result.sh`).

**Status and saturation.** Deprecated. A 2026 expert audit found the original Omni-Judge
wrong in 96.4% of disagreement cases and introduced Omni-MATH-2 with a clean, exact-answer
subset — original leaderboard scores should not guide frontier comparisons. No formal
`successor` field is recorded, though the status evidence points to Omni-MATH-2 as the
audited replacement.

**Contamination risk.** The test set is public and static. The authors curated, annotated,
and categorized the competition problems, but the complete questions and answers are public,
and many source competitions predate the benchmark itself. Beyond ordinary public-exposure
risk, Omni-MATH carries a second, arguably larger problem: the judge itself was found wrong
in the large majority of disputed cases, so even a "clean" score may reflect judge error
rather than model capability.

**Why it matters for an AI engineer.** This is the clearest "do not trust a pre-audit score"
case in this catalog. With the original judge found wrong in 96.4% of disagreement cases,
treat any pre-2026-audit Omni-MATH number — including the single figure in this catalog's own
performance snapshot — as unreliable. If Olympiad-breadth coverage like this is what you
need, look for Omni-MATH-2's clean, exact-answer subset rather than the original judged
metric.

**Performance snapshot**

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| OpenAI o1-mini | openai | 10 Oct 2024 | 60.54% | independent |

> Sampling error is smaller than judge error here. Scores depend on final-answer extraction
> and equivalence judgment; the 2026 audit found these errors can erase real model
> differences — a caution that applies directly to the single score above.

**Source.** Paper: [Omni-MATH: A Universal Olympiad Level Mathematic Benchmark For Large
Language Models](https://arxiv.org/abs/2410.07985) (ICLR 2025). Dataset:
[KbsdJames/Omni-MATH](https://github.com/KbsdJames/Omni-MATH). Leaderboard: [Official
Omni-MATH project](https://omni-math.github.io).

---

*Benchmark catalog — Maths domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
