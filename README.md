# LLM Evaluations — Study Notes

A structured set of notes on **LLM Evaluations (Evals)** — from core concepts, through
the practical evaluation workflow, to production-grade online monitoring. Each note is
self-contained but builds on the previous one, so the reading order below is recommended
for a first pass.

---

## Reading Order

| # | Notes | What it covers |
| :---: | --- | --- |
| 1 | [What are LLM Evals?](./llm_evals.md) | Definition, evals vs. metrics, and the two domains: **Model Evals** vs. **Application Evals**. |
| 2 | [The Application Evaluation Workflow](./llm_application_evaluation_workflow.md) | The **11-step loop** (Zomato email-router case study) — from defining the task to monitoring and feedback. |
| 3 | [Why Multiple Eval Pipelines](./why-multiple-eval-pipelines.md) | The **failure levels** (component / workflow / application), the K=5 RAG breakdown, and the risk matrix (Quality / Safety / Operations). |
| 4 | [LLM Evaluation Methods](./llm-evaluation-methods.md) | The three methods — **Programmatic**, **Human-based**, **LLM-as-a-Judge** — plus reference-based vs. reference-free and offline vs. online. |
| 5 | [Offline vs. Online Evals](./offline-vs-online-evals.md) | The online evaluation pipeline and a deep dive on **Computed vs. Captured** criteria; the self-improving loop. |

### Model Evals — Deep Dive

Notes 1–5 focus mostly on **Application Evals**. These two expand the **Model Evals** domain
introduced in note 1 (evaluating the base model itself).

| # | Notes | What it covers |
| :---: | --- | --- |
| 6 | [Model Evals — Framework & Capability Domains](./model-evals-capabilities.md) | Why model evals matter, the common eval arc, public benchmark vs. own eval set, and the **eight capability domains** in detail. |
| 7 | [Model Evals — Benchmarks & Harnesses](./model-evals-benchmarks-and-harnesses.md) | The **anatomy of a benchmark** (dataset, run config, scoring, aggregation), the eval loop, eval harnesses, who runs evals, and why benchmarks mislead. |

---

## The Big Picture

The series moves through three stages — understanding *what* evals are, learning *how* to
run them, and applying them *in production*:

```
        WHAT                        HOW                        IN PRODUCTION
   +--------------+        +----------------------+       +--------------------+
   | 1. Concepts  |        | 2. 11-step workflow  |       | 5. Online evals    |
   | Model vs App |  --->  | 3. Why many pipelines| --->  | Computed vs        |
   |              |        | 4. Eval methods      |       | Captured + loop    |
   +--------------+        +----------------------+       +--------------------+
```

**Throughline:** Define clear criteria, build a Golden Dataset, pick an evaluation method,
then run, analyze, improve, and iterate. Once the system meets its threshold, deploy it and
monitor live traffic, feeding real-world failures back into the Golden Dataset. That final
feedback path is the **self-improving loop**.

---

## Folder Structure

```
notes/
├── README.md                               (index — this file)
├── llm_evals.md                            (1) concepts
├── llm_application_evaluation_workflow.md   (2) the 11-step workflow
├── why-multiple-eval-pipelines.md           (3) failure levels + risk matrix
├── llm-evaluation-methods.md                (4) the three evaluation methods
├── offline-vs-online-evals.md               (5) computed vs. captured
├── model-evals-capabilities.md              (6) model evals + 8 capabilities
├── model-evals-benchmarks-and-harnesses.md  (7) benchmarks, loop, harnesses
└── images/
    ├── llm-evaluation-workflow.png          (used by note 2)
    ├── failure-points-target.png            (used by note 3)
    ├── computed-vs-captured.png             (used by note 5)
    ├── online-eval-pipeline.png             (used by note 5)
    └── benchmark-eval-loop.png              (used by note 7)
```

---

## Key Terms Quick Reference

| Term | Meaning |
| --- | --- |
| **Golden Dataset** | Curated 50–500 row test set with ground-truth labels; the backbone of offline evals. |
| **Recall @ K** | Fraction of relevant documents a retriever fetches within its top-K results. |
| **LLM-as-a-Judge** | Using a capable LLM to grade outputs — human-like nuance at program speed and cost. |
| **MAE (Mean Absolute Error)** | Average deviation between LLM grades and human grades; the goal is to drive it toward 0. |
| **Groundedness / Faithfulness** | Whether an answer is strictly supported by the retrieved context. |
| **Captured criteria** | Metrics read directly from the trace (latency, cost, tokens, thumbs, escalation). |
| **Computed criteria** | Metrics produced by an evaluator (faithfulness, toxicity, hallucination, relevance). |
| **Self-Improving Loop** | Live failures added to the Golden Dataset, re-run through offline evals, then redeployed. |

---

*A study series on LLM Evaluations. Start at [note 1](./llm_evals.md).*
