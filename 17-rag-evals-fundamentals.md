# RAG Evals: Fundamentals

> Retrieval-Augmented Generation pairs a retriever with a generator, and each half can
> pass its own evaluation while the pipeline still fails, exactly the workflow-level
> failure the K=5 breakdown in
> [note 03](./03-why-multiple-eval-pipelines.md#b-workflow-level-failures--the-k5-rag-breakdown)
> already walked through. This note is the metrics side of that problem: what to measure
> at the retrieval stage, what to measure at the generation stage, and the formulas behind
> the numbers a RAG eval tool actually prints.

Researched against the current documentation for RAGAS and TruLens, and recent papers on
retrieval evaluation and hallucination detection. Sources are linked throughout rather than
collected at the end, so you can go straight to the primary material behind any claim.

---

## Table of Contents

1. [Why RAG Needs Its Own Evals](#1-why-rag-needs-its-own-evals)
2. [The RAG Triad](#2-the-rag-triad)
3. [Retrieval-Stage Metrics](#3-retrieval-stage-metrics)
4. [Generation-Stage Metrics](#4-generation-stage-metrics)
5. [The Extended RAGAS Metric Catalog](#5-the-extended-ragas-metric-catalog)
6. [How Faithfulness Detection Actually Works](#6-how-faithfulness-detection-actually-works)

---

## 1. Why RAG Needs Its Own Evals

A RAG system has two components that fail independently and combine in ways that hide the
failure. [Note 03](./03-why-multiple-eval-pipelines.md) already worked through the concrete
case: a retriever at K=5 correctly pulls the right document into slot five, the generator
correctly follows its instruction to prioritize higher-ranked chunks, and the two correct
behaviors together produce a wrong answer.

RAG evaluation exists to catch that gap. It splits into two stages that need separate
metrics:

- **Retrieval-stage metrics** ask whether the right chunks were pulled from the vector
  database, independent of what the generator later does with them.
- **Generation-stage metrics** ask whether the generator's answer is actually supported by
  whatever it was given, independent of whether the retrieval was any good.

Scoring only the final answer, the way a generic application eval might, conflates these
two failure modes. A low end-to-end score does not tell you whether to fix the retriever,
the prompt, or the reranker. Splitting the metrics does.

## 2. The RAG Triad

[TruLens](https://www.trulens.org/getting_started/core_concepts/rag_triad/), developed by
the TruEra team, introduced the **RAG Triad** in 2023 as the standard three-part structure
almost every RAG eval framework now follows in some form:

| Leg of the triad | Question it answers | What it checks |
| --- | --- | --- |
| **Context Relevance** | Was the retrieval any good? | Whether each retrieved chunk is actually relevant to the query. |
| **Groundedness** | Is the answer honest about its sources? | Whether every claim in the generated answer is supported by the retrieved context. |
| **Answer Relevance** | Did the answer address the question? | Whether the final response actually answers what the user asked, independent of whether it's grounded. |

Each leg catches a distinct failure. Poor context relevance means the generator never had
a chance, since it was reasoning over the wrong material. Poor groundedness means the
generator ignored or embellished on what it was given, which is the technical definition of
a hallucination inside a RAG system. Poor answer relevance means the generator was
grounded and given good context, yet still didn't address the question.

TruLens computes all three with an LLM-as-a-judge, a carefully prompted evaluator model, so
the triad works without needing a labeled ground-truth dataset. As TruEra puts it: passing
all three "provides confidence that an LLM app is free from hallucination, up to the limit
of its knowledge base." That last clause matters. The triad tells you the system is honest
about its sources, not that its sources are correct.

## 3. Retrieval-Stage Metrics

These come from classical information retrieval, applied to the vector search step of a
RAG pipeline. They split into two families.

### Order-unaware metrics

These treat the retrieved set as a bag, and only ask whether the right items are in it
somewhere in the top K, not where.

| Metric | What it measures |
| --- | --- |
| **Precision@K** | Of the K chunks retrieved, what fraction are actually relevant? `true positives@k / (true positives@k + false positives@k)` |
| **Recall@K** | Of all the relevant chunks that exist in the corpus, what fraction made it into the top K? |
| **Hit Rate** | Across a query set, what fraction of queries had at least one relevant chunk somewhere in the top K? A coarse coverage check. |

### Order-aware metrics

These give credit for ranking the relevant chunk higher, which matters because a generator
reading five chunks tends to weight the earlier ones more heavily, the same dynamic behind
the K=5 breakdown in note 03.

| Metric | What it measures |
| --- | --- |
| **Mean Reciprocal Rank (MRR)** | The average of 1/rank of the first relevant hit across queries. A relevant chunk at rank 1 scores 1.0; at rank 4 it scores 0.25. |
| **Normalized Discounted Cumulative Gain (NDCG)** | Graded relevance (not just relevant/irrelevant) with a discount for lower rank position, normalized against the best possible ordering. |

**Practical guidance:** use MRR and Recall while tuning the retriever itself (embedding
model, index, query expansion). Use NDCG and Hit Rate once a reranker or the full system is
in the loop, since those metrics reward getting the best chunk to the top, which is exactly
what a reranker is for.

## 4. Generation-Stage Metrics

The four metrics below are the ones almost every RAG eval framework implements first, most
directly from [RAGAS](https://docs.ragas.io/en/stable/concepts/metrics/available_metrics/),
the most widely adopted open-source RAG evaluation library. Each formula and worked example
below is pulled from the current RAGAS documentation.

### Faithfulness

> **Formula:** `Faithfulness = (number of claims in the response supported by the retrieved context) / (total number of claims in the response)`

Computed in three steps: the response is decomposed into individual, independently
verifiable claims; each claim is checked against the retrieved context to see whether it
can be logically inferred from it; the supported count is divided by the total.

**Worked example** (from the RAGAS docs): given the context "Einstein was born on 14 March
1879," a response of "Einstein was born in Germany on 20th March 1879" contains two claims.
"Germany" is supported; "20th March 1879" is not (the context says 14 March). Result: 1
supported claim out of 2 total, a faithfulness score of **0.5**.

This is the same metric as the RAG Triad's Groundedness leg, computed a specific way.

### Context Precision

> **Formula:** `Context Precision@K = Σ(Precision@k × v_k) / (total relevant items in top K)`, where `v_k` is a binary relevance indicator at rank k.

This is precision weighted by position: it rewards the retriever for ranking relevant
chunks *higher*, not just including them. Per the RAGAS docs, "if an irrelevant chunk is
present at the second position, context precision remains the same. However, if this
irrelevant chunk is placed at the first position, context precision reduces." An identical
set of retrieved chunks in a different order produces a different score.

### Context Recall

> **Formula:** `Context Recall = (number of claims in the reference answer supported by the retrieved context) / (total number of claims in the reference answer)`

The reference answer (a ground-truth answer written for the golden dataset) is broken into
claims, and each is checked against what was actually retrieved.

**Worked example** (from the RAGAS docs): for the question "Where is the Eiffel Tower
located?" with retrieved context "Paris is the capital of France" and reference answer "The
Eiffel Tower is located in Paris," the single claim ("Eiffel Tower is in Paris") is
supported by the Paris context. Result: a context recall of **1.0**.

Note the asymmetry with Faithfulness: Context Recall needs a ground-truth reference answer,
so it is a reference-based metric in the sense from
[note 04](./04-llm-evaluation-methods.md#4-reference-based-vs-reference-free-evaluations).
Faithfulness does not need one, since it only checks the response against the retrieved
context, not against a golden answer. RAGAS also ships non-LLM and ID-based variants of
context recall that use string comparison or document IDs instead of claim analysis, faster
and cheaper, at the cost of the semantic nuance an LLM judge provides.

### Answer Relevancy and Answer Correctness

- **Answer Relevancy** asks whether the response actually addresses the question, the same
  concept as the RAG Triad's third leg, typically computed by generating candidate
  questions the answer *would* be a good response to, and measuring their similarity to the
  original question.
- **Answer Correctness** measures correctness against a reference answer as a weighted
  combination of factual overlap (an F1-style score) and semantic similarity, so a response
  that is factually right but phrased completely differently from the reference still
  scores well.
- **Semantic Similarity** is a simpler building block underneath Answer Correctness: embed
  both the generated answer and the reference answer, and take the cosine similarity
  between them.

## 5. The Extended RAGAS Metric Catalog

Beyond the core four, RAGAS ships a long tail of more specialized metrics. Two are worth
knowing well because they show up often in RAG-specific work; the rest are listed for
awareness.

- **Context Entity Recall** measures what fraction of the *named entities* in the reference
  answer also appear somewhere in the retrieved context, rather than checking full claims.
  Useful for fact-heavy domains like tourism, historical Q&A, or anything where getting the
  right entity (a name, date, place) matters more than getting the right prose.
- **Noise Sensitivity** measures how often the system produces an incorrect answer when
  given either relevant *or* irrelevant retrieved documents. Unlike almost every other
  metric on this list, **lower is better**: a noise-sensitive system gets thrown off by
  irrelevant context in its window, which is a different failure from simply not finding
  the right chunk.

The rest of the catalog covers territory outside RAG specifically: multimodal faithfulness
and relevance, tool-call accuracy and agent goal accuracy (for agentic systems, closer to
[note 06's Agentic & Tool Use domain](./06-model-evals-capabilities.md#46-agentic--tool-use)),
and a set of classical NLP string metrics (BLEU, ROUGE, chrF, exact match, string presence)
that predate LLM-as-judge scoring and remain useful when you need something fast, cheap, and
fully deterministic rather than semantically aware.

## 6. How Faithfulness Detection Actually Works

The claim-level scoring behind Faithfulness and Context Recall is not a single technique;
it is a small pipeline, and the choice of implementation affects both cost and reliability.

**The standard pipeline**, per current research on hallucination detection:

1. **Claim extraction.** The response is decomposed into atomic, independently checkable
   claims. This step is itself either a rule-based parser, a prompted LLM ("extract all
   factual claims as a numbered list"), or a fine-tuned claim-decomposition model.
2. **Claim verification.** Each claim is checked against the source context to determine
   whether the context *entails* it. Two approaches dominate:
   - **NLI-based verification**, using a natural language inference model (commonly a
     fine-tuned DeBERTa or RoBERTa-MNLI) to classify the context/claim pair as entailment,
     contradiction, or neutral. Fast, cheap, and consistent, at the cost of missing subtler
     entailments a smaller model can't reason through.
   - **LLM-as-judge verification**, prompting a capable LLM with the context and the claim
     and asking it to judge support. More expensive per call, but handles nuance and
     multi-step inference the NLI models miss.
3. **Aggregation.** The verified claims are counted and divided by the total, producing the
   faithfulness or context-recall score shown above.

Current research describes claim decomposition plus NLI entailment as "the most reliable
automated approach" for this kind of checking, precisely because it is fine-grained: a
claim-level score doesn't just say the response failed, it tells you *which specific claim*
was unsupported, which traces back to a retrieval failure, a prompt problem, or the model
inventing something outright. That granularity is the entire practical value of faithfulness
scoring over a single end-to-end pass/fail judgment.

---

*Notes on RAG evaluation fundamentals: the RAG Triad, retrieval-stage IR metrics, the core
RAGAS generation metrics with their exact formulas, and how claim-level faithfulness
checking works under the hood. Continues in
[RAG Evals: Building the Practice](./18-rag-evals-practice.md).*
