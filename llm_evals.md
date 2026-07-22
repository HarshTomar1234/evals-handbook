# LLM Evaluations (Evals) — Notes

> A structured guide to what LLM evaluations are, how they differ from plain metrics,
> and the two domains every AI engineer should understand: **Model Evals** and **Application Evals**.

---

## Table of Contents

1. [What are LLM Evaluations?](#1-what-are-llm-evaluations-evals)
2. [LLM Evals vs. Metrics](#2-llm-evals-vs-metrics)
3. [The Two Main Domains](#3-the-two-main-domains-model-evals-vs-application-evals)
   - [A. Model Evals](#a-model-evals)
   - [B. Application Evals](#b-application-evals)
4. [Quick Reference: Model vs. Application Evals](#4-quick-reference-model-vs-application-evals)

---

## 1. What are LLM Evaluations (Evals)?

**LLM Evals** are **systematic, repeatable tests** used to assess an LLM or an
LLM-powered system against a **clear, defined set of criteria**. They apply both to
standalone base models and to applications built on top of them.

Unlike traditional software testing or simple *"vibe checks,"* proper LLM evaluations
rely on **three core characteristics**:

| Characteristic | What it means |
| --- | --- |
| **Systematic Testing** | Moves beyond *"vibe testing"* (asking a few random questions and assuming things work because answers look fine). Instead, build **comprehensive datasets** covering diverse edge cases — e.g., compile 100 real user interactions to test a chatbot. |
| **Repeatability** | Evals must be **repeatable over time**. Even if you change system prompts, models, retrievers, or chunking strategies, you can re-run the *same* dataset to compare versions objectively (V1 vs. V2). |
| **Clear Criteria** | Evaluation needs **explicitly defined parameters** — e.g., answers are factually correct, beginner-friendly, strictly sourced from curriculum materials, and completely safe (no abusive language or aggressive tone). |

---

## 2. LLM Evals vs. Metrics

> ⚠️ **Common misconception:** an "eval" is *not* just a metric (like accuracy,
> precision, or recall in classical ML).

An **LLM Eval represents the entire testing setup**, which includes:

- **The Component** — what specific part is being tested (e.g., the retriever in a RAG chatbot).
- **The Criteria** — the standard of measurement (e.g., retriever accuracy).
- **The Dataset** — the structured test cases prepared for the evaluation.
- **The Environment** — when/where the test runs (offline during development, or online in production).
- **The Tooling** — the frameworks used to run evals (e.g., **Ragas**).

The goal isn't just to output a score, but to **answer production-critical questions**:

- Is this system **safe** for real users?
- Is the **latency** within acceptable limits?
- Did **Prompt V2** perform better than **Prompt V1**?
- Are RAG answers strictly **grounded** in the retrieved context?
- Can this specific **model** handle our target task?
- Is the system **robust** enough to ship to production?

---

## 3. The Two Main Domains (Model Evals vs. Application Evals)

LLM evaluations split into two categories: **Model Evals** and **Application Evals**.
The industry often uses "LLM Evals" as a blanket term for both, but keeping the
distinction in mind is crucial.

### A. Model Evals

**Model Evals** evaluate the **base model itself** — its core intelligence,
capabilities, and limitations. When a new frontier model ships, AI labs run it across
benchmarks and publish leaderboard standings.

**Eight major capability categories:**

| # | Capability | Description |
| --- | --- | --- |
| 1 | **Reasoning** | Thinking step-by-step to solve complex problems. |
| 2 | **Knowledge** | General world knowledge up to the model's cut-off date. |
| 3 | **Basic Maths** | Arithmetic and mathematical word problems. |
| 4 | **Coding** | Writing, debugging, and understanding code. |
| 5 | **Instruction Following** | Executing sequential rules/instructions accurately. |
| 6 | **Long Context Handling** | Retrieving details from massive inputs (*"Needle in a Haystack"*). |
| 7 | **Multimodal Understanding** | Processing/generating images, text, audio, etc. |
| 8 | **Tool Use** | Integrating with and using external tools/APIs. |

**Standardised benchmarks:**

| Benchmark | What it measures |
| --- | --- |
| **MMLU** (Massive Multitask Language Understanding) | General knowledge & reasoning across law, history, medicine, science. |
| **GSM8K** | Grade-school maths problem-solving. |
| **SWE-bench** | Real-world GitHub issues across an entire repository. |
| **HumanEval** | Isolated function-completion / code generation. |
| **IFEval** | Instruction-following precision. |
| **Needle in a Haystack** | Retrieval accuracy within long-context windows. |
| **MMMU** | Multimodal comprehension. |

> 💡 **Note:** *SWE-bench* and *HumanEval* both test coding but differ in scope —
> HumanEval is small, isolated function completion; SWE-bench is real GitHub issues
> spanning a whole repo.

**Relevance for AI Engineers:**
You'll rarely design or run Model Evals yourself — that's the domain of frontier AI
research labs. But **being able to read and interpret these benchmarks is essential**.
This literacy lets you make informed decisions when selecting the right LLM (OpenAI,
Anthropic, open-source, etc.) to power your application.

### B. Application Evals

**Application Evals** assess the **performance, behavior, and safety of the entire
LLM-powered application** (or specific subsystems) — not just the model. This is the
**most critical domain for AI engineers**, who are responsible for building and shipping
complete products.

**The "Brain" vs. the System** — In production, the LLM is just one component (the
brain). A functional application relies on a large surrounding architecture:

- User Interface (UI)
- System Prompts
- Orchestration Code (control flows, branching, parallel execution)
- Guardrails and Output Parsers
- Memory systems, Vector Databases, Retrieval pipelines, Embedding Models
- Monitoring setups and post-deployment feedback loops

**🔧 The Smartphone Analogy:**
A powerful mobile processor (Snapdragon, MediaTek) might score incredibly high on raw
synthetic benchmarks — but a great processor doesn't automatically make a great
smartphone. A stellar phone also depends on its OS, battery, camera, screen, sound, and
graphics. Each must be tested individually. Likewise, even with a top-tier LLM, you must
evaluate how your prompts, retrievers, databases, and guardrails work **together**.

**Core Focus:**
While Model Evals ask *"Can the model do this?"*, Application Evals ask
**"Will our product work reliably in the real world?"** They answer questions like:

- Was the user's question **resolved correctly**?
- Did the system **retrieve** and use the correct reference materials?
- Was the response **faithful** to the source, or did it **hallucinate**?
- Is the explanation **tone** appropriate and safe?
- Is **latency** fast enough to prevent user frustration?

---

## 4. Quick Reference: Model vs. Application Evals

| Aspect | Model Evals | Application Evals |
| --- | --- | --- |
| **What's tested** | The base model itself | The entire LLM-powered system |
| **Core question** | *"Can the model do this?"* | *"Will our product work in the real world?"* |
| **Who runs them** | Frontier AI research labs | AI engineers building the product |
| **Examples** | MMLU, GSM8K, SWE-bench, HumanEval, IFEval, MMMU | Faithfulness, retrieval accuracy, latency, safety, tone |
| **Engineer's role** | Read & interpret benchmarks to pick a model | Design, build, and run the evals directly |

---

*Notes on LLM Evaluations — foundational concepts.*
