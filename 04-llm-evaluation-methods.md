# LLM Evaluation Methods — Notes

> An **LLM Evaluation Method** is the actual mechanism and procedure used to determine
> whether an LLM's output is good or bad. It takes a raw system output and produces an
> **objective judgment or score.**

There are **three core execution methods**:

1. **Programmatic / Deterministic** (automated code & heuristics)
2. **Human-based**
3. **Model-graded** (LLM-as-a-Judge)

Plus two important framing distinctions covered at the end:
**Reference-Based vs. Reference-Free**, and **Offline vs. Online**.

---

## Table of Contents

1. [The Programmatic (Deterministic) Method](#1-the-programmatic-deterministic-method)
2. [The Human-Based Method](#2-the-human-based-method)
3. [The Model-Graded Method (LLM-as-a-Judge)](#3-the-model-graded-method-llm-as-a-judge)
4. [Reference-Based vs. Reference-Free Evaluations](#4-reference-based-vs-reference-free-evaluations)
5. [Offline vs. Online Evaluation](#5-offline-vs-online-evaluation)
6. [Quick Comparison of the Three Methods](#6-quick-comparison-of-the-three-methods)

---

## 1. The Programmatic (Deterministic) Method

Relies entirely on **automated code and heuristics** (e.g. Python scripts) to compare
system outputs against a ground-truth dataset. It is **fast, cheap, highly scalable, and
completely objective.**

### Case Study: Evaluating a RAG Retriever Component

- **The Goal:** measure how well a retriever extracts the correct, relevant context
  documents from a Vector Database for a given user query.
- **Success Criteria — Recall @ K:** out of all the correct/relevant documents that
  exist in the database for a query, how many did the retriever fetch in its top **K**
  results?

$$\text{Recall @ K} = \frac{\text{Number of Relevant Documents Retrieved in Top K}}{\text{Total Number of Existing Relevant Documents}}$$

**Numerical example:**

- User asks: *"What are the pre-requisites for the ML course and how long is it?"*
- The vector database contains exactly **two** correct documents: **`1001`** and **`1003`**.
- The retriever is set to **K = 5** and returns 5 documents, but only **`1001`** is among the relevant ones.
- Since only 1 of the 2 relevant documents was retrieved:

$$\text{Recall @ 5} = \frac{1}{2} = 50\%$$

**Evaluation workflow:**

1. A human expert manually tags the correct documents for a **Golden Dataset** of 50–100 sample queries.
2. Queries are run through the retriever, and **Recall @ K** is calculated row-by-row.
3. Individual recall scores are averaged across the dataset → overall **Retriever Recall @ K** (e.g. 67%).

**How to improve low recall:**

- Swap or fine-tune the **embedding model**
- Implement **query expansion** (use an LLM to enrich the query before retrieval)
- Increase the **value of K**
- Introduce a **Re-ranker**

---

## 2. The Human-Based Method

Uses **human evaluators** to assess performance. Critical for **nuanced or subjective
tasks** where code cannot interpret meaning, tone, or helpfulness.

### Case Study: Evaluating Chatbot Helpfulness

- **The Goal:** evaluate the *"Helpfulness"* of an end-to-end customer support chatbot —
  defined as accuracy of content, appropriate tone, and completeness of the answer.
- **Success Criteria — Rubric-based grading:** since there's no simple mathematical
  metric for "helpfulness," define a standardised **1-to-5 rubric**:

| Score | Meaning |
| :---: | --- |
| **5** | Factual, complete, and written in a perfect, polite tone. |
| **3** | Partially helpful but missing some details. |
| **1** | Completely unhelpful or entirely off-topic. |

**Evaluation workflow:**

1. Set up a dataset of 50–100 representative user queries.
2. Run them through the chatbot to generate outputs.
3. Human graders read each query–response pair, assign a rubric score, then average the results.

**The Multi-Grader Strategy:** using multiple graders (Grader A + Grader B) is common. If
they frequently **disagree** on the same answers, that flags **ambiguity in your rubric
instructions** — a signal to refine the criteria.

### The Five Ways Humans Evaluate LLMs

1. **Direct Grading & Rating** — review outputs and assign scores/ranks by rubric.
2. **Red Teaming** — humans systematically and intentionally *attack* the system to find where it breaks or leaks information.
3. **A/B Testing** — deploy two versions to production; live users rate or pick the better experience.
4. **Golden Dataset & Rubric Creation** — design the frameworks, write test cases, establish ground-truth references.
5. **Human-in-the-Loop** — route complex/ambiguous/borderline automated responses to a human for final judgment.

> **Trade-offs:** human evals are **highly reliable and trusted**, but **expensive, slow,
> and impossible to run at massive production scale.**

---

## 3. The Model-Graded Method (LLM-as-a-Judge)

Uses a highly capable LLM (e.g. GPT-4) as an **automated evaluator**. The middle ground —
the **semantic understanding of a human** at the **speed and low cost of a program.**

### Case Study: Evaluator Platform for Subjective UPSC Mock Exams

- **The Business Problem:** a prep channel runs subjective, essay-based UPSC mock tests
  (India's highly competitive civil services exam). With tens of thousands of
  submissions, hiring human subject-matter experts to grade essays is too expensive and slow.
- **The Solution:** an automated platform where a **"Judge LLM"** evaluates student essays using expert human rubrics.
- **Success Criteria:** the platform's automated grading must **closely align with grades given by human experts.**

**Rubric design** — for each question, experts define explicit dimensions. E.g. a 15-mark
essay on ethical governance is broken into **5 dimensions**:

1. Addresses ethical governance and accountability
2. Explains the link between them
3. Provides administrative mechanisms
4. Cites relevant real-world examples
5. Provides a balanced conclusion

**Golden Dataset:** experts manually grade 50–100 historical answers using the rubrics to create a baseline.

**Prompting the Judge LLM:** a structured system prompt instructs it to act as a strict
UPSC grader. It receives the **question, maximum marks, human rubric, and student
answer**. The prompt explicitly tells it to **deny marks for keyword stuffing, verbosity,
or unbacked claims**, and to output a **structured justification alongside the final score.**

**The Metric — Mean Absolute Error (MAE):** measures how well the Judge LLM aligns with
humans across the golden dataset:

$$\text{MAE} = \frac{\sum \lvert \text{Human Mark} - \text{LLM Mark} \rvert}{N}$$

- **Interpretation:** an MAE of **2.3** means the LLM's grade deviates from a human expert by **2.3 marks on average**.
- **The Goal:** drive MAE **as close to 0 as possible** by refining the system prompt, upgrading the judge model, or clarifying the rubric.

---

## 4. Reference-Based vs. Reference-Free Evaluations

| Feature | Reference-Based Evaluations | Reference-Free Evaluations |
| :--- | :--- | :--- |
| **Definition** | Compares outputs against a **predefined correct ground-truth answer** / strict baseline mapped to each test case. | Assesses output quality **on its own terms** using standard criteria or a scale rubric — **no per-item gold answers.** |
| **Data Requirements** | Needs a Golden Dataset with the exact correct answer/document for **every row**. | Needs inputs (e.g. queries) + a general grading rubric, but **no pre-written correct answers.** |
| **Examples** | • **Retriever Recall** — exact correct document IDs are known.<br>• **UPSC Grading** — LLM marks compared directly against known human expert scores. | • **Chatbot Helpfulness** — humans rate a generated answer 1–5 on a subjective quality rubric. |

---

## 5. Offline vs. Online Evaluation

| | **Offline Evaluation** | **Online Evaluation** |
| --- | --- | --- |
| **When** | During **development**, before shipping to production. | Continuously **after deployment**, in production. |
| **How** | Uses the **Golden Dataset** to run repeatable, systematic tests. | Monitors **real user interactions**, identifying live system failures. |
| **Feedback** | Establishes a baseline and compares versions (V1 vs. V2). | Captures live failure cases and **adds them back into the Golden Dataset** for continuous iteration. |

> These two form a loop: offline evals get you production-ready; online evals feed
> real-world failures back into the offline Golden Dataset — the **self-improving loop.**

---

## 6. Quick Comparison of the Three Methods

| | **Programmatic** | **Human-Based** | **LLM-as-a-Judge** |
| --- | --- | --- | --- |
| **How it grades** | Code / heuristics vs. ground truth | Human graders + rubric | A capable LLM grades vs. rubric |
| **Speed** | ⚡ Fastest | 🐢 Slowest | ⚡ Fast |
| **Cost** | 💲 Cheapest | 💰💰 Most expensive | 💰 Low |
| **Scalability** | ✅ Massive | ❌ Poor | ✅ High |
| **Handles nuance/semantics** | ❌ No (exact/heuristic only) | ✅ Best | ✅ Good |
| **Best for** | Classification, retrieval recall, exact-match | Subjective tone/helpfulness, red-teaming, ground-truth creation | Subjective grading at scale (essays, long-form) |
| **Key metric example** | Recall @ K, Accuracy | Rubric score (1–5) | MAE vs. human marks |

---

*Notes on LLM Evaluation Methods — programmatic, human, and LLM-as-a-judge, with
reference-based vs. reference-free and offline vs. online framing.*
