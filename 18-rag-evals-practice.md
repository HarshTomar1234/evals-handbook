# RAG Evals: Building the Practice

> [Fundamentals](./17-rag-evals-fundamentals.md) covered what to measure: the RAG Triad,
> retrieval-stage IR metrics, and the generation-stage metrics with their formulas. This
> note covers how those metrics get used in practice: where the golden dataset comes from,
> how chunking decisions distort the numbers before you even start measuring, which
> frameworks implement all this, and the specific ways RAG evaluation still breaks on
> harder queries.

---

## Table of Contents

1. [Building a Golden Dataset for RAG](#1-building-a-golden-dataset-for-rag)
2. [Chunking's Effect on Evaluation](#2-chunkings-effect-on-evaluation)
3. [RAG Evaluation Frameworks](#3-rag-evaluation-frameworks)
4. [Multi-Hop and Agentic RAG Evaluation](#4-multi-hop-and-agentic-rag-evaluation)
5. [The Full RAG Eval Pipeline](#5-the-full-rag-eval-pipeline)
6. [Common Pitfalls in RAG Evaluation](#6-common-pitfalls-in-rag-evaluation)

---

## 1. Building a Golden Dataset for RAG

[Note 02](./02-llm_application_evaluation_workflow.md#step-3--build-a-golden-dataset) and
[note 04](./04-llm-evaluation-methods.md) already covered the general shape of a Golden
Dataset: real inputs, hand-labeled, 50 to 500 rows. RAG has a specific twist, because a
question-answer pair by itself is not enough. You also need to know *which chunks in the
corpus* should have been retrieved to answer it, so the retrieval-stage metrics from
[note 17](./17-rag-evals-fundamentals.md#3-retrieval-stage-metrics) have something to check
against.

Writing that by hand for hundreds of questions across a large corpus does not scale, which
is why [RAGAS ships a synthetic test generator](https://docs.ragas.io/en/stable/getstarted/rag_testset_generation/).
The mechanism is worth understanding rather than treating as a black box:

1. **Build a knowledge graph from the source documents.** The generator parses the corpus
   and constructs an internal graph connecting entities, facts, and relationships across
   documents, not just within a single chunk.
2. **Enrich the graph with transformations** that add relationships a plain document split
   would miss, letting the generator reason about connections between documents.
3. **Traverse the graph with different query synthesizers**, each producing a different
   *type* of question: a simple single-fact lookup, a question that requires combining
   information from two nodes, a question phrased ambiguously to test robustness, and so
   on. Each synthesizer walks the graph differently, which is what makes the resulting test
   set diverse rather than 200 variations of the same question shape.
4. **Emit the question, the reference answer, and the source chunk IDs together**, so the
   resulting dataset works for both generation-stage and retrieval-stage metrics out of the
   box.

The practical payoff: a synthetic set built this way naturally includes multi-hop questions
(section 4 below), not just the single-chunk lookups a naive generator would default to,
because the graph structure is what surfaces those connections in the first place.

## 2. Chunking's Effect on Evaluation

A detail easy to miss: your retrieval metrics are not measuring the retriever in a vacuum,
they are measuring the retriever *given a specific chunking strategy*, and that strategy
alone can swing the numbers more than most model or embedding choices.

**Chunk size trade-off.** Reducing chunk size from 1,024 tokens down to 64 tokens improved
fact-based Recall@1 by 10 to 15 percentage points on entity-heavy datasets in one published
comparison, because a smaller chunk has less irrelevant surrounding text diluting the match.
But small chunks lose surrounding context, which hurts narrative-style queries that depend
on a broader passage to answer correctly. There is no single correct chunk size; the right
size depends on the query type your golden dataset actually contains.

**Chunking strategy, not just size.** A comparative evaluation found adaptive chunking
(boundaries chosen based on content, not a fixed token count) reaching precision 0.50 /
recall 0.88 / F1 0.64, against a fixed-size baseline at 0.17 / 0.40 / 0.24 on the same
corpus, roughly triple the F1. Semantic chunking, which aligns chunk boundaries with
discourse structure rather than a raw token count, showed similar precision gains over
naive fixed-size splitting elsewhere in the literature, by avoiding cutting a chunk
mid-thought.

**A ceiling on the other end.** Larger chunks are not free either: beyond roughly 2,500
tokens of retrieved context, generation quality itself starts to degrade, independent of
whether the right information was in there.

**The evaluation implication:** when a retrieval-metric score looks bad, the fix is not
always the embedding model or the query. Run the same retrieval metrics across two or three
chunking strategies before concluding the retriever itself is the problem, since the
chunking decision made upstream of retrieval can be the actual bottleneck.

## 3. RAG Evaluation Frameworks

Four names come up repeatedly, and they are not really competitors so much as tools that
cover different parts of the pipeline from
[note 07's harness concept](./07-model-evals-benchmarks-and-harnesses.md#3-the-need-for-an-eval-harness).

| Framework | What it is | Where it fits |
| --- | --- | --- |
| **RAGAS** | An open-source, reference-free metrics library: the source for every formula in [note 17, section 4](./17-rag-evals-fundamentals.md#4-generation-stage-metrics). | Your default choice when the requirement is retrieval and generation metrics inside a broader eval pipeline. |
| **DeepEval** | A Python-first, pytest-style testing framework. Notably handles multi-turn RAG evaluation through a sliding-window conversational test case, a scenario most other tools still handle poorly. | CI/CD-style testing where RAG evals need to run and fail like any other test suite. |
| **TruLens** | The framework that defined the RAG Triad ([note 17, section 2](./17-rag-evals-fundamentals.md#2-the-rag-triad)), using LLM-as-a-judge scoring for all three legs. | When the Triad's three-question structure is the evaluation model you want, rather than assembling metrics individually. |
| **Arize Phoenix** | An open-source observability platform that doubles as a RAG evaluator. Where RAGAS and DeepEval are primarily offline tools, Phoenix adds production tracing on top. | The online half of the [offline/online split from note 05](./05-offline-vs-online-evals.md), watching a RAG system after it ships. |

The practical pattern in production teams is not picking one of these, it is combining a
metrics framework (RAGAS or DeepEval) with an observability layer (Phoenix or similar) and
wiring both into CI/CD, which mirrors exactly the automated release gates from
[note 05, section 2](./05-offline-vs-online-evals.md#2-offline-evaluations-recap--core-purposes).

## 4. Multi-Hop and Agentic RAG Evaluation

Most of what's in note 17 assumes single-hop retrieval: one query, one relevant chunk (or a
handful), one answer. Two harder cases stretch that assumption, and current research
(2026) still treats both as open problems rather than solved ones.

**Multi-hop retrieval.** Some questions need two or more pieces of information combined,
where each piece looks irrelevant in isolation but is essential once combined with the
other. A chunk about "Company X was founded in 1998" and a separate chunk about "Company
X's founder previously worked at Company Y" are each individually unremarkable; only
together do they answer "Where did the founder of Company X work before founding it?"
Standard retrieval metrics, built around single-relevant-chunk assumptions, do not cleanly
capture whether the *combination* was retrieved correctly.

The evaluation gap compounds because most existing benchmarks provide only the final
question and answer, without the intermediate hop-level questions that connect them, which
makes it hard to tell *at which hop* a RAG system failed. Recent work proposes
context-aware retriever evaluation strategies specifically for this: comparing single-hop
and multi-hop-aware LLM-judge approaches shows that context-aware evaluation outperforms
context-blind evaluation specifically on multi-hop queries, while making little difference
on single-hop ones, evidence that multi-hop evaluation genuinely needs its own method
rather than reusing single-hop metrics.

**Agentic RAG.** When retrieval is not a single fixed step but something an agent decides
to invoke repeatedly, refining its query based on what it finds (closer to
[note 06's Agentic & Tool Use domain](./06-model-evals-capabilities.md#46-agentic--tool-use)
than to classic RAG), the evaluation question shifts from "was the right chunk retrieved"
to "did the sequence of retrieval decisions converge on the right information." Hop-aware
benchmarks built specifically to diagnose *where in a multi-step retrieval chain* an
agentic system goes wrong are an active area of research, not yet a settled, off-the-shelf
metric the way Faithfulness or Context Precision are.

## 5. The Full RAG Eval Pipeline

Putting notes 17 and 18 together, a complete RAG evaluation setup runs three things in
parallel rather than one end-to-end score:

- **Retrieval-stage metrics** (Precision@K, Recall@K, MRR, NDCG) against a golden dataset
  that includes labeled relevant-chunk IDs, most practically produced by the synthetic
  generation process in section 1.
- **Generation-stage metrics** (Faithfulness, Context Precision, Context Recall, Answer
  Relevancy, Answer Correctness) checking the generator's behavior given whatever the
  retriever actually returned.
- **The RAG Triad or an equivalent composite view**, giving a fast, three-number summary of
  whether the system is hallucination-free up to the limits of its knowledge base.

All three feed the same offline-to-online loop from
[note 05](./05-offline-vs-online-evals.md#10-the-self-improving-loop): run them offline
against the golden dataset before shipping, then keep sampling generation-stage metrics
(especially Faithfulness) against live traffic in production, since a chunking change, a
reranker swap, or a corpus update can silently shift retrieval quality in ways an offline
snapshot won't catch until the next full re-evaluation.

## 6. Common Pitfalls in RAG Evaluation

- **Chunking confounds the retrieval score.** Covered in section 2: don't diagnose a "bad
  retriever" without first checking whether the chunking strategy is the actual bottleneck.
- **The precision/recall trade-off moves with K.** A larger K almost always raises recall
  and lowers precision, since you're pulling in more chunks, some of which won't be
  relevant. Report both together, and at a K that matches what the generator is actually
  given, not an arbitrary round number.
- **Judge-model bias and cost.** Every LLM-as-judge metric in note 17 (Faithfulness,
  Context Precision, Context Recall, Answer Relevancy) inherits the judge-reliability
  concerns from [note 04, section 3](./04-llm-evaluation-methods.md#3-the-model-graded-method-llm-as-a-judge):
  a weak or miscalibrated judge model produces a misleading score no matter how sound the
  metric formula is. Track judge-model agreement with human raters (an MAE-style check, as
  in note 04's UPSC case study) before trusting the numbers at scale.
- **Golden dataset contamination.** If the same LLM that generates your synthetic golden
  dataset is also used as the judge scoring your system's answers against it, the judge can
  end up rewarding phrasing that matches its own generation style rather than genuine
  correctness. Where possible, use a different model family for dataset generation than for
  judging, or spot-check a sample against human review.
- **Component metrics passing while the workflow still fails.** The reason this entire
  two-note series exists: a system can score well on every individual retrieval and
  generation metric and still produce a wrong answer through the interaction between them,
  exactly the failure mode [note 03](./03-why-multiple-eval-pipelines.md#b-workflow-level-failures--the-k5-rag-breakdown)
  opened with. Component metrics are necessary, not sufficient; always pair them with an
  end-to-end check on real queries.

---

*Notes on building RAG evaluation in practice: synthetic golden datasets, chunking's effect
on retrieval scores, the major frameworks, multi-hop and agentic evaluation, and the
pitfalls that catch most first attempts. Continues from
[RAG Evals: Fundamentals](./17-rag-evals-fundamentals.md).*
