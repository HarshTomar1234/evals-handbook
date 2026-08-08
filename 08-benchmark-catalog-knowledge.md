# Benchmark Catalog — Knowledge

> Whether a model holds accurate factual knowledge and can retrieve well-calibrated
> answers rather than confident guesses — across subjects, without fabricating what it
> does not know.
>
> Source data: [BenchWiki](https://benchwiki.vercel.app) — an actively maintained
> benchmark tracker. This catalog captures a snapshot as of the dates recorded in each
> benchmark's `last_updated` field below; check the live site for the latest.

---

## Table of Contents

1. [At a Glance](#1-at-a-glance)
2. [Humanity's Last Exam](#2-humanitys-last-exam)
3. [SimpleQA](#3-simpleqa)
4. [GPQA](#4-gpqa)
5. [MMLU-Pro](#5-mmlu-pro)
6. [MMLU](#6-mmlu)
7. [MMLU-Redux](#7-mmlu-redux)

---

## 1. At a Glance

Sorted `active` → `nearing-saturation` → `saturated` → `deprecated`, alphabetical within each tier.

| Benchmark | Status | Launch Date | Dataset Size | Primary Metric | Contamination Risk |
| --- | --- | --- | --- | --- | --- |
| [Humanity's Last Exam](#2-humanitys-last-exam) | Active | 24 Jan 2025 | 2,500 questions | accuracy, no tools (%) | Medium |
| [SimpleQA](#3-simpleqa) | Active | 7 Nov 2024 | 4,326 questions | correct (accuracy over all questions, %) | Medium |
| [GPQA](#4-gpqa) | Nearing-Saturation | 20 Nov 2023 | 198 questions (Diamond subset) | accuracy on GPQA Diamond, 4-option MC (%) | Medium |
| [MMLU-Pro](#5-mmlu-pro) | Nearing-Saturation | 3 Jun 2024 | 12,032 questions | accuracy, 10-option multiple choice (%) | Medium |
| [MMLU](#6-mmlu) | Saturated | 7 Sep 2020 | 14,042 questions | accuracy (%) | High |
| [MMLU-Redux](#7-mmlu-redux) | Saturated | 6 Jun 2024 | 5,700 questions | accuracy on re-annotated MMLU questions (%) | High |

---

## 2. Humanity's Last Exam

*~2,500 expert-written questions across 100+ subjects, each filtered to stump frontier
models — designed to be the final closed-ended academic benchmark.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Active |
| Launch Date | 24 Jan 2025 |
| Dataset Size | 2,500 questions |
| Primary Metric | accuracy, no tools (%) |
| Human Baseline | Not published |
| Contamination Risk | Medium |
| License | MIT |
| Maintainer | Center for AI Safety (CAIS) & Scale AI; lastexam.ai, repo `centerforaisafety/hle` |

### What it measures

HLE (capability: knowledge, secondary: reasoning) is built from roughly 2,500 expert-written
questions spanning 100+ subjects — from classics to rocket engineering — with each question
adversarially filtered to stump 2024-era frontier models. The public set was refined down
from an original 3,000 questions after a post-launch review of the answer keys. About 80% of
the questions are short-answer, graded for equivalence to a reference answer by a prompted
GPT-4o judge (`gpt-4o-2024-08-06`, structured decoding); the headline configuration is
closed-book, no tools, no browsing. A second headline number, RMS calibration error, is
derived from the same graded correctness plus each model's stated confidence — it measures
overconfidence rather than knowledge, and launch-era models registered calibration error in
the 80–93% range (highly confident while almost always wrong). The dataset is public but
gated on HuggingFace behind an access agreement.

There is no human baseline, and — uniquely on this catalog — the concept does not apply even
in principle: no single person could sit an exam spanning 100+ expert subjects, so an
aggregate human score is undefined by construction, not merely unmeasured. The only
meaningful human reference is per-question — each was authored and solved by the domain
expert who wrote it — which is the entire premise of the benchmark: testing whether one
model can match the union of many human specialists.

### Evaluation protocol

Reported zero-shot, with chain-of-thought reasoning, at pass@1, and with tools off (no
temperature is specified). This is the strict closed-book configuration described in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md): the
model gets no browsing and no external aids, so the reported accuracy reflects parametric
knowledge and reasoning alone. Tool-augmented and agentic runs exist and score much higher,
but are a different configuration entirely and are not comparable to the no-tools number.
Reference harnesses are the official HLE repo and Inspect (task `hle`). A no-tools frontier
run costs an estimated $10–50 per full pass — the ~2,500 hard questions elicit long
reasoning traces, and every answer needs a separate GPT-4o judge call on top of the model
call itself.

### Status and saturation

Status is **active** — described on the wiki as "the clearest active benchmark on the
wiki." Launch scores were single digits (GPT-4o 3.3%, o1 9.1%, DeepSeek-R1 9.4%, no tools);
the current no-tools frontier is around 38% (Gemini 3 Pro, November 2025) — a steep climb,
but far from solved. There is no successor and no saturation date. Because HLE's premise is
to be the "last" closed-ended academic exam, saturation here would signal that closed-ended
Q&A testing has run out of headroom entirely — pointing future evaluation toward open-ended,
agentic, and interactive formats rather than a still-harder multiple-choice set.

### Contamination risk

Risk is **medium**. The public ~2,500-question set is gated on HuggingFace (an access
agreement, with a request not to re-share), and it is fully static with no refresh cycle —
both facts that would normally push risk toward high. What holds it at medium is the
strongest mitigation catalogued in this domain: CAIS/Scale AI hold a private set of
additional questions, used to compare a model's public-set performance against its
private-set performance and flag benchmark-specific overfitting. In practice this means a
published HLE score is harder to game by memorization alone than most static public
benchmarks, though it is not leak-proof — the public questions are increasingly discussed
online and will keep entering post-2024 training corpora.

> Read HLE scores beside their date and this page's `last_updated`. Reported accuracy has
> moved from single digits to ~38% in under a year — a number that is months old may already
> understate the frontier. Read accuracy beside calibration error, not alone, and never
> compare a no-tools score against a tool-augmented one; the two differ by tens of points on
> the same model.

### Why it matters for an AI engineer

HLE is currently the most defensible active knowledge benchmark to cite for frontier-model
comparisons: it is far from saturated, and its private holdout is a genuine (if partial)
check against benchmark-specific overfitting that most static public sets lack. That said, a
vendor-reported score is only interpretable with its date and its tools setting attached —
this benchmark moves fast, and a "38%" claim from six months ago is stale, while a
tool-augmented number quoted without qualification is comparing apples to oranges against a
no-tools figure. Treat it as a live, moving target rather than a settled reference.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o | OpenAI | 24 Jan 2025 | 3.3% | Independent (paper) |
| o1 | OpenAI | 24 Jan 2025 | 9.1% | Independent (paper) |
| GPT-5 | OpenAI | 7 Aug 2025 | 25.3% | Independent (lastexam.ai) |
| Gemini 3 Pro | Google | 18 Nov 2025 | 38.3% | Independent (lastexam.ai) |

All scores above are on the no-tools axis. Tool-augmented and agentic scores exist and run
substantially higher but are tracked separately and are not plotted here.

### Source

Paper: [Humanity's Last Exam](https://arxiv.org/abs/2501.14249) (CAIS & Scale AI, preprint).
Dataset: [huggingface.co/datasets/cais/hle](https://huggingface.co/datasets/cais/hle).
Leaderboards: [Humanity's Last Exam (official, CAIS)](https://lastexam.ai),
[Scale AI / SEAL](https://scale.com/leaderboard).

---

## 3. SimpleQA

*4,326 short fact-seeking questions chosen to stump GPT-4 — a hallucination test graded
correct / incorrect / not-attempted.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Active |
| Launch Date | 7 Nov 2024 |
| Dataset Size | 4,326 questions |
| Primary Metric | correct (accuracy over all questions, %) |
| Human Baseline | Not published |
| Contamination Risk | Medium |
| License | MIT |
| Maintainer | OpenAI (Wei et al.); repo `github.com/openai/simple-evals` |

### What it measures

SimpleQA (capability: knowledge, no secondary capabilities listed) is a set of 4,326
short-form, fact-seeking questions deliberately selected to be ones GPT-4 got wrong — an
adversarial filter aimed squarely at parametric recall rather than reasoning. Each answer is
classified by a prompted ChatGPT-style grader (GPT-4o in OpenAI's `simple-evals` reference
implementation) into one of three outcomes: correct, incorrect, or not-attempted, rather than
a binary right/wrong. The headline "correct" metric is accuracy over all questions, but two
companion metrics matter just as much: "correct given attempted" (accuracy only among
questions the model chose to answer — rewards knowing without penalizing declining) and
F-score (the harmonic mean of the two, the paper's own summary statistic, and the number
SimpleQA Verified reports as F1). Together these separate a model that is knowledgeable from
one that is merely bold. The dataset is public and ungated.

There is no human baseline, and here too a human number would arguably be beside the point:
the questions were adversarially selected to be obscure, so a closed-book human would mostly
be tested on luck, and an open-book human would mostly be tested on Googling — neither
measures the thing SimpleQA is trying to isolate. The paper does report a 94.4% third-party
annotator agreement rate, but that is a measure of answer-key quality, not human
question-answering ability.

### Evaluation protocol

Reported zero-shot, direct (no chain-of-thought), at temperature 0 and pass@1, with tools
off. As covered in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md), greedy
decoding at temperature 0 is what makes a SimpleQA run reproducible between labs; direct
(non-CoT) prompting keeps the test close to raw recall rather than reasoning-assisted
guessing. Reference harnesses are `simple-evals` (official, task `simpleqa`) and Inspect
(task `simpleqa`). A full pass costs an estimated $3–15 on a frontier model — answers are
short, but every one requires a separate LLM-grader call, which roughly doubles the API
spend relative to the model calls alone.

### Status and saturation

Status is **active**. Frontier "correct" scores remain well below any plausible ceiling:
GPT-4o scored 38% at launch and GPT-4.5 reached about 62% by early 2025 — a real climb, but
nowhere near solved — and reasoning-tuned models do not automatically help (o1-mini scored
only 8%). No successor and no saturation date are recorded. The wiki flags two conditions
that would move it off active: frontier "correct" crossing roughly 85–90% (nearing the
label-quality ceiling), or retrieval-augmented deployment becoming universal enough that
closed-book parametric recall stops being the relevant question. Neither has happened.
`lineage.variants` lists two newer siblings, SimpleQA Verified and Chinese SimpleQA, though
neither has its own catalog page yet.

### Contamination risk

Risk is **medium**. The dataset is public and ungated (`openai/simple-evals`), fully static,
with no refresh cycle. The situation carries a particular irony: a benchmark built to
measure hallucination can itself be memorized. SimpleQA's questions were adversarially
collected against 2024-era models specifically because those models got them wrong, but the
exact question-answer pairs are now discussed in papers, blog posts, and leaderboards, and
increasingly enter post-2024 training corpora — meaning a model can "improve" on SimpleQA by
having seen the answer key rather than by actually knowing more. SimpleQA Verified (2025)
partly re-collects and cleans the set to push back on exactly this.

> A single "correct" percentage hides whether a model is knowledgeable or merely bold. Always
> read "correct" alongside "not attempted" and, where available, "correct given attempted" —
> two models can post the same headline number with very different honesty profiles.

### Why it matters for an AI engineer

Because it is still climbing and not yet near its ceiling, SimpleQA remains a reasonable
current signal for comparing models' closed-book factual recall and, more importantly, their
propensity to bluff versus decline. But a bare "correct" number is an incomplete read — pull
the not-attempted rate too, since a cautious model that declines often can look artificially
weak on "correct" alone while actually being more trustworthy. Given medium and rising
contamination risk, weight recent SimpleQA claims more skeptically than the original
2024-era numbers, and expect SimpleQA Verified to be the more durable reference going
forward.

### Performance snapshot

| Model | Vendor | Date | Score (correct) | Source |
| --- | --- | --- | --- | --- |
| Claude 3 Opus | Anthropic | 7 Nov 2024 | 23.5% | Independent (paper) |
| GPT-4o | OpenAI | 7 Nov 2024 | 38.2% | Vendor-reported |
| o1-preview | OpenAI | 7 Nov 2024 | 42.7% | Vendor-reported |
| GPT-4.5 | OpenAI | 27 Feb 2025 | 62.5% | Vendor-reported |

### Source

Paper: [Measuring Short-Form Factuality in Large Language Models](https://arxiv.org/abs/2411.04368)
(OpenAI, technical report / preprint).
Dataset: [github.com/openai/simple-evals](https://github.com/openai/simple-evals).
Leaderboards: [SimpleQA (official, OpenAI/Kaggle)](https://www.kaggle.com/benchmarks/openai/simpleqa),
[Artificial Analysis / third-party trackers](https://openai.com/index/introducing-simpleqa/).

---

## 4. GPQA

*PhD-written science questions so hard that skilled non-experts with Google score 34% — the
"Google-proof" exam, reported on its 198-question Diamond subset.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Nearing-Saturation |
| Launch Date | 20 Nov 2023 |
| Dataset Size | 198 questions (Diamond subset) |
| Primary Metric | accuracy on GPQA Diamond, 4-option multiple choice (%) |
| Human Baseline | 81.3% (PhD-domain experts, Diamond subset) |
| Contamination Risk | Medium |
| License | CC-BY-4.0 |
| Maintainer | David Rein et al. (NYU, with Cohere and Anthropic-affiliated co-authors); repo `github.com/idavidrein/gpqa` |

### What it measures

GPQA (capability: knowledge, secondary: reasoning) is a set of PhD-level, 4-option
multiple-choice science questions written by domain experts and vetted to be
"Google-proof" — skilled non-expert validators with unrestricted web access scored only
22.1% on the Diamond subset (34% on the larger main set). The wiki's primary reported
configuration is the 198-question Diamond subset, which is the hardest and most heavily
validated slice; the paper also defines larger 448-question main and 546-question extended
sets. Scoring is exact-match multiple choice. The dataset is public but gated on HuggingFace
behind an access agreement.

The human baseline is unusually well-documented and worth reading carefully: PhD-domain
experts scored 81.3% on Diamond (the figure this catalog uses as the reference line, since
GPQA's entire premise is expert-level difficulty), against non-expert web-search accuracy of
just 22.1%. On the larger main set, the headline expert figures are about 65% (74% after
discounting mistakes the experts themselves later flagged) versus about 34% for
non-experts. A further wrinkle: OpenAI, evaluating o1, recruited its own PhD expert pool and
measured only 69.7% on Diamond — so whether a model "beats PhD experts" depends heavily on
which expert pool is used as the yardstick.

### Evaluation protocol

Reported zero-shot with chain-of-thought at pass@1, tools off (no temperature specified).
This is the same closed-book, single-attempt configuration discussed generally in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md): no
external aids, one shot per question, which is what makes the "Google-proof" claim
meaningful. Reference harnesses are the official GPQA repo, Inspect (task `gpqa_diamond`),
and lm-evaluation-harness (task `gpqa_diamond_zeroshot`). A full Diamond pass on a frontier
model costs an estimated $2–15 — reasoning-era models emit long chains of thought per
question, which dominates the cost on only 198 items; the larger main and extended sets
scale up proportionally.

### Status and saturation

Status is **nearing-saturation** — flagged on the wiki as a judgment call and the first
benchmark in that status tier. Frontier reasoning models reach the high 80s on Diamond (Grok
4 at roughly 87%, mid-2025), against a label-noise-adjusted ceiling likely in the low 90s
(the paper documents a few percent of questions with expert-identified errors). Meaningful
headroom remains and top models are only now crossing the PhD-expert line, so it still
discriminates — but the trajectory suggests it is one or two model generations from done.
The named successor is **Humanity's Last Exam**, which supersedes GPQA as the harder,
broader closed-ended academic benchmark. `lineage.variants` lists the three GPQA subsets:
`gpqa-main`, `gpqa-extended`, and `gpqa-diamond`.

### Contamination risk

Risk is **medium**. The dataset is public but gated — HuggingFace access requires accepting
an agreement, and the authors explicitly ask users not to post questions or answers online
"to reduce the risk of leakage into foundation model training corpora." That is a voluntary
pledge, not a technical control, comparable in kind to the opt-in decontamination scheme that
failed for BIG-Bench's canary strings. The set is fully static with no refreshed variant, and
Diamond questions have been discussed and reproduced online since 2023 — which pushes the
effective risk from medium toward high as time passes.

> GPQA Diamond is only 198 questions. At roughly 80% accuracy, that is a 95% confidence
> interval of about ±5.6 points (±7 near 50%). A great many publicized "Model A beats Model B
> by 2 points on GPQA Diamond" claims are statistically indistinguishable from noise — treat
> any Diamond gap under about 6 points as unresolved, and prefer the larger main or extended
> sets when a comparison genuinely needs to be settled.

### Why it matters for an AI engineer

GPQA Diamond scores are still worth citing today, but with two caveats baked in: treat any
gap under roughly 6 points between models as statistical noise given the 198-question sample,
and weight the "Google-proof" framing against rising contamination risk — the questions have
been public and discussed since 2023. Because it is nearing saturation and has a named
successor (Humanity's Last Exam), an engineer building a longer-lived comparison pipeline
should already be tracking HLE alongside it rather than relying on GPQA alone going forward.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4o | OpenAI | 13 May 2024 | 56.1% | Vendor-reported |
| o1 | OpenAI | 12 Sep 2024 | 78.0% | Vendor-reported |
| Grok 4 | xAI | 9 Jul 2025 | 87.0% | Independent (Epoch AI) |

### Source

Paper: [GPQA: A Graduate-Level Google-Proof Q&A Benchmark](https://arxiv.org/abs/2311.12022)
(COLM 2024).
Dataset: [huggingface.co/datasets/Idavidrein/gpqa](https://huggingface.co/datasets/Idavidrein/gpqa).
Leaderboards: [Epoch AI (GPQA Diamond)](https://epoch.ai/benchmarks/gpqa-diamond),
[Artificial Analysis (GPQA Diamond)](https://artificialanalysis.ai/evaluations/gpqa-diamond).

---

## 5. MMLU-Pro

*MMLU rebuilt to fix its flaws: 10 options instead of 4, trivia and label noise filtered
out, and reasoning-heavy questions that reward chain-of-thought.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Nearing-Saturation |
| Launch Date | 3 Jun 2024 |
| Dataset Size | 12,032 questions |
| Primary Metric | accuracy, 10-option multiple choice (%) |
| Human Baseline | Not published |
| Contamination Risk | Medium (trending up) |
| License | CC-BY-4.0 |
| Maintainer | TIGER-Lab (University of Waterloo); repo `TIGER-AI-Lab/MMLU-Pro` |

### What it measures

MMLU-Pro (capability: knowledge, secondary: reasoning) is a direct response to MMLU's known
weaknesses: it widens each question from 4 options to 10 (cutting the random-guess floor from
25% to 10%), filters out trivial and label-noisy questions, and adds reasoning-heavy items
that reward chain-of-thought over pattern matching. It spans 14 disciplines and reports both
an overall mean accuracy across 12,032 questions and per-category breakdowns. The dataset is
public and ungated. Its parent is MMLU, and it is MMLU's own designated successor.

There is no human baseline, and the absence is deliberate rather than an oversight:
MMLU-Pro's difficulty was calibrated by model-side filtering (removing questions strong
models found trivial, adding ones they found hard), not by testing humans. That sets it apart
from its parent MMLU, famous for its 89.8% expert-baseline framing, and from GPQA, built
explicitly around an expert-vs-non-expert gap — MMLU-Pro simply has no human reference line
to compare against.

### Evaluation protocol

Reported 5-shot with chain-of-thought at temperature 0 and pass@1, tools off. Per the run
configuration concepts in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md),
few-shot examples plus CoT reliably lift scores over zero-shot direct answering — this is why
MMLU-Pro's protocol differs from its parent MMLU's 5-shot *direct* setup, and the two numbers
are not interchangeable even though both are "5-shot." Reference harnesses are the official
MMLU-Pro repo and lm-evaluation-harness (task `mmlu_pro`). A full run costs an estimated
$10–40 on a frontier model: 12,032 questions under a 5-shot CoT protocol produce long
reasoning traces per item, several times MMLU's per-question token cost.

### Status and saturation

Status is **nearing-saturation** — the second benchmark in that tier on the wiki. Frontier
models cluster in the high 80s by 2025–26 (leaderboards put the top around 89–90%), but the
cleaner labels and 10-option format push the effective ceiling above MMLU's roughly 90%, so
it still separates strong models where MMLU cannot. The 16–33 point accuracy drop MMLU-Pro
originally induced relative to MMLU is eroding, and the trajectory points toward saturation
within a generation or two of models — hence nearing-saturation rather than active. No
successor is recorded yet, and `lineage.parent` is `mmlu`.

### Contamination risk

Risk is **medium, trending up**. The dataset is public and ungated on HuggingFace, and fully
static with no refreshed variant. Risk is lower than MMLU's mainly because MMLU-Pro launched
in mid-2024 and has had less time in web crawls, but it is rising: many questions are drawn
from public STEM problem sets — exam and textbook sources — that may themselves already
appear in pretraining data independent of MMLU-Pro's own publication.

> As frontier scores compress into the high 80s, top-model gaps of a couple of points are
> again approaching noise, echoing MMLU's late-stage pattern. Per-category splits across the
> 14 disciplines are much smaller than the full set — never compare models on a single
> discipline.

### Why it matters for an AI engineer

MMLU-Pro is currently a reasonable knowledge/reasoning discriminator and a meaningful upgrade
over a saturated MMLU — the 10-option format and cleaner labels give it real headroom that
MMLU no longer has. But the same compression pattern that ended MMLU's usefulness is visibly
underway here too (high-80s clustering by 2025–26), so an engineer should expect MMLU-Pro to
follow the same arc within a model generation or two, and should watch for its own successor
or a label-noise audit (in the spirit of MMLU-Redux) to emerge as scores keep compressing.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-4-Turbo | OpenAI | 9 Apr 2024 | 63.7% | Independent (paper) |
| Claude 3 Opus | Anthropic | 4 Mar 2024 | 68.5% | Independent (paper) |
| Gemini 1.5 Pro | Google | 14 May 2024 | 69.0% | Independent (paper) |
| GPT-4o | OpenAI | 13 May 2024 | 72.6% | Independent (paper) |

### Source

Paper: [MMLU-Pro: A More Robust and Challenging Multi-Task Language Understanding Benchmark](https://arxiv.org/abs/2406.01574)
(NeurIPS 2024 Datasets & Benchmarks, Spotlight).
Dataset: [huggingface.co/datasets/TIGER-Lab/MMLU-Pro](https://huggingface.co/datasets/TIGER-Lab/MMLU-Pro).
Leaderboards: [MMLU-Pro (official, HuggingFace)](https://huggingface.co/spaces/TIGER-Lab/MMLU-Pro),
[Artificial Analysis (MMLU-Pro)](https://artificialanalysis.ai/evaluations/mmlu-pro).

---

## 6. MMLU

*57-subject multiple-choice exam spanning STEM, humanities, and social sciences — the
defining knowledge benchmark of the 2020–2024 era.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Saturated (since 1 Jun 2024) |
| Launch Date | 7 Sep 2020 |
| Dataset Size | 14,042 questions |
| Primary Metric | accuracy (%) |
| Human Baseline | 89.8% (estimated expert-level accuracy) |
| Contamination Risk | High |
| License | MIT |
| Maintainer | Center for AI Safety (CAIS) / Dan Hendrycks |

### What it measures

MMLU (capability: knowledge, no secondary capabilities listed) is a 57-subject
multiple-choice exam covering STEM, humanities, and social sciences, scored by exact-match
over 14,042 questions. The headline number is a macro average across the 57 subjects, though
some reports instead use a micro average over all questions — the two can differ by up to
about a point, so it is worth checking which one a given leaderboard uses before comparing
across sources. The dataset is public and ungated.

The human baseline of 89.8% comes from the original paper's estimate of specialist,
subject-matter expert accuracy (for example, about 87% for a professional-level test-taker on
the morality subject, averaging to roughly 90%). Unspecialized human crowdworkers, by
contrast, scored only 34.5% — a reminder that "human-level" on MMLU means domain-expert
level, not average-person level.

### Evaluation protocol

Reported 5-shot, direct (no chain-of-thought), at temperature 0 and pass@1, with tools off.
As covered in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md), few-shot
prompting without CoT was the standard protocol for multiple-choice knowledge benchmarks
through the 2020–2024 era — the model sees five worked examples establishing the answer
format, then answers directly. Reference harnesses are lm-evaluation-harness (task `mmlu`),
HELM (task `mmlu`), and Inspect (task `mmlu`). A full 14,042-question run costs an estimated
$10–40 on a frontier model — prompts are short and answers are single tokens, so the 5-shot
prompt prefix dominates token count rather than long generations.

### Status and saturation

Status is **saturated**, as of 1 Jun 2024. Frontier models have clustered at 86–92% since
2024, and an estimated 2–6% label-error rate puts the effective ceiling near 90% — meaning
top-model gaps at the frontier are within noise. The named successor is **MMLU-Pro**.
`lineage.variants` lists four descendants: `mmlu-pro`, `mmlu-redux`, `global-mmlu`, and
`mmmlu`.

### Contamination risk

Risk is **high**, with no mitigation recorded. The test set has been fully public on GitHub
and HuggingFace since 2020 and appears verbatim in web-scale crawl data — most modern
pretraining corpora likely contain it despite decontamination attempts by individual labs.
For a benchmark this old and this widely mirrored, a high score is at least partly a
statement about what a model's training data contained, not only about what it can reason
its way to.

> 14,042 questions give tight aggregate confidence intervals (roughly ±0.5 points at 85%
> accuracy), but per-subject splits are small — some subjects have only about 100 questions,
> giving confidence intervals of roughly ±8 points. Never compare models on a single subject,
> and remember that label errors (2–6%) dwarf sampling error at the frontier.

### Why it matters for an AI engineer

MMLU should be treated as a historical/legacy reference at this point, not a live decision
input. Post-2024 vendor-reported MMLU scores clustering at 86–92% are not meaningfully
differentiating models — the gap between two frontier models on MMLU today is smaller than
the benchmark's own label-noise floor. It remains useful for tracking the long-run
2020–2024 capability trajectory (43.9% at GPT-3 to high-80s by 2024) and for sanity-checking
that a new model is "in the right range," but MMLU-Pro or a newer benchmark should carry the
actual comparison.

### Performance snapshot

| Model | Vendor | Date | Score | Source |
| --- | --- | --- | --- | --- |
| GPT-3 175B | OpenAI | 7 Sep 2020 | 43.9% | Independent (paper) |
| GPT-4 | OpenAI | 14 Mar 2023 | 86.4% | Vendor-reported |
| GPT-4o | OpenAI | 13 May 2024 | 88.7% | Vendor-reported |
| Llama 3.1 405B | Meta | 23 Jul 2024 | 88.6% | Vendor-reported |

### Source

Paper: [Measuring Massive Multitask Language Understanding](https://arxiv.org/abs/2009.03300)
(ICLR 2021).
Dataset: [huggingface.co/datasets/cais/mmlu](https://huggingface.co/datasets/cais/mmlu).
Leaderboards: [Papers with Code](https://paperswithcode.com/sota/multi-task-language-understanding-on-mmlu),
[HELM Classic](https://crfm.stanford.edu/helm/classic/latest).

---

## 7. MMLU-Redux

*A manual error audit of MMLU: experts re-annotated thousands of questions and found ~6.5%
are broken — the evidence base for MMLU's label-noise ceiling.*

### Quick Facts

| Field | Value |
| --- | --- |
| Status | Saturated (since 6 Jun 2024, its own launch date) |
| Launch Date | 6 Jun 2024 |
| Dataset Size | 5,700 questions |
| Primary Metric | accuracy on the re-annotated (error-corrected) MMLU questions (%) |
| Human Baseline | Not published |
| Contamination Risk | High |
| License | CC-BY-4.0 |
| Maintainer | Edinburgh DAWG (Gema et al.); repo/dataset `edinburgh-dawg/mmlu-redux-2.0` |

### What it measures

MMLU-Redux (capability: knowledge, no secondary capabilities listed) is not a competition
benchmark but a forensic audit of MMLU: expert annotators manually re-checked MMLU questions
for correctness, and the paper's core analysis compares reported-versus-corrected accuracy
and how model rankings shift per subject once bad labels are fixed. The original audit
covered 3,000 questions across 30 subjects (14 expert annotators); the current, expanded
Redux 2.0 covers 5,700 questions across all 57 subjects. Overall, about 6.5% of MMLU
questions contain errors — but the rate is wildly uneven, near zero in some subjects and as
high as roughly 57% in Virology (already flagged above 25% in the original 30-subject audit).
The dataset is public and ungated.

There is no separate human baseline; MMLU-Redux inherits its parent MMLU's 89.8% expert-
baseline framing rather than establishing its own, because its contribution is orthogonal to
a human-versus-model comparison — it measures how much of MMLU's apparent headroom was
illusory label noise, not how models compare to people.

> The unevenness across subjects is the real finding here, more than the 6.5% headline
> average: a benchmark's *effective* ceiling is set by its worst subjects, not its overall
> error rate. A model's MMLU score on Virology specifically should be read with real
> suspicion given a documented ~25–57% error rate in that subject alone.

### Evaluation protocol

Reported 5-shot, direct (no chain-of-thought), at temperature 0 and pass@1, tools off — the
same protocol as parent MMLU, applied to the corrected question set. Reference harnesses are
the MMLU-Redux dataset itself on HuggingFace and the standard lm-evaluation-harness MMLU
harness, pointed at the corrected set. A full run costs an estimated $1–5 on a frontier
model — 5,700 corrected multiple-choice items under the standard direct-answer protocol is
cheap; the real cost of MMLU-Redux was the human annotation effort, not model inference.

### Status and saturation

Status is **saturated**, with `saturated_date` set to its own launch date (6 Jun 2024) — a
deliberate marker that it described an already-saturated parent from the moment it existed.
As a competition benchmark it inherits MMLU's contamination and frontier convergence and so
does not differentiate top models by the wiki's saturation definition (usefulness as a
model-differentiating eval). Its enduring value is different and non-competitive: it remains
an actively-used forensic reference and audit methodology for measuring label noise in MMLU
and, by extension, in MMLU-derived benchmarks. `lineage.parent` is `mmlu`; there is no
successor.

### Contamination risk

Risk is **high** — inherited completely from MMLU, since MMLU-Redux questions are literally
MMLU questions with corrected labels. They have been public since 2020 (as MMLU), the set is
fully static, and it is ungated. Correcting the labels does nothing to reduce memorization
risk; if anything, the corrected questions are now more discussed, not less.

### Why it matters for an AI engineer

MMLU-Redux is not a benchmark to track model-versus-model, and its own performance timeline
is intentionally empty — labs do not report headline Redux scores over time because it is a
corrected reference set and audit methodology, not a leaderboard. Its practical use for an
engineer is different: as the citation for *why* a given MMLU subject score should be
distrusted (check whether that subject is one of Redux's high-error ones, like Virology
at up to ~57%), and as a template for auditing label quality in any other multiple-choice
benchmark before trusting its ceiling.

### Performance snapshot

No performance timeline is recorded for this benchmark. This is by design, not a data gap:
MMLU-Redux is a corrected reference set and audit methodology rather than a competition
leaderboard, so model vendors do not publish headline Redux scores over time, and plotting a
model-vs-time race would misrepresent what the benchmark is for.

### Source

Paper: [Are We Done with MMLU?](https://arxiv.org/abs/2406.04127) (NAACL 2025).
Dataset: [huggingface.co/datasets/edinburgh-dawg/mmlu-redux-2.0](https://huggingface.co/datasets/edinburgh-dawg/mmlu-redux-2.0).
Leaderboards: none listed.

---

*Benchmark catalog — Knowledge domain. Data sourced from [BenchWiki](https://benchwiki.vercel.app).
Part of the Model Evals deep dive — see also
[Model Evals — Framework & Capability Domains](./06-model-evals-capabilities.md) and
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
