# Model Evals — Framework & Capability Domains

> **Model Eval** is the systematic process of measuring an underlying model's
> capabilities, behaviour, reliability, and operational characteristics under
> **controlled conditions**. Where Application Evals ask *"does our product work?"*,
> Model Evals ask *"what can this base model actually do?"*

This note covers **why** engineers care about model evals, the **common arc** every model
eval follows, the two sources of test data, and a detailed breakdown of the **eight
capability domains** frontier labs measure.

---

## Table of Contents

1. [Why AI Engineers Need Model Evals](#1-why-ai-engineers-need-model-evals)
2. [The Common Arc of a Model Eval](#2-the-common-arc-of-a-model-eval)
3. [Two Sources of Tests: Public Benchmark vs. Your Own Eval Set](#3-two-sources-of-tests-public-benchmark-vs-your-own-eval-set)
4. [The Eight Capability Domains](#4-the-eight-capability-domains)
   - [1. Knowledge & Reasoning](#41-knowledge--reasoning)
   - [2. Coding & Software Engineering](#42-coding--software-engineering)
   - [3. Mathematics](#43-mathematics)
   - [4. Long Context](#44-long-context)
   - [5. Vision & Multimodal](#45-vision--multimodal)
   - [6. Agentic & Tool Use](#46-agentic--tool-use)
   - [7. Safety & Alignment](#47-safety--alignment)
   - [8. Instruction Following](#48-instruction-following)

---

## 1. Why AI Engineers Need Model Evals

Even though frontier labs run the heavyweight benchmarks, an application engineer relies on
model evals for four practical decisions:

- **To compare different models fairly** — put candidates on identical footing before choosing one.
- **To track whether newer models are actually improving** — a new release is not automatically better *for your task*.
- **To decide whether a model is safe for release** — verify safety behaviour before exposing it to users.
- **Build-versus-buy decisions** — should you use a hosted frontier API, or self-host an open model? Evals give you the evidence.

---

## 2. The Common Arc of a Model Eval

At the highest level, every model eval follows the same four-stage arc:

1. **Decide what to measure** — reasoning? coding? safety? instruction-following? You cannot evaluate everything at once; name the capability.
2. **Get a test for it** — either a public benchmark or an eval set you assemble yourself (see section 3).
3. **Run the model under a fixed protocol** — same prompts, same conditions for every model, so results are **comparable**. Comparability is the whole point; change the protocol and the numbers stop meaning anything.
4. **Score and interpret** — turn outputs into a number, then work out what that number actually tells you. A score without interpretation is just a statistic.

---

## 3. Two Sources of Tests: Public Benchmark vs. Your Own Eval Set

| | **Public Benchmark** | **Your Own Eval Set** |
| --- | --- | --- |
| **What it is** | A standardized, shared test like **MMLU** or **SWE-bench**. | Data you assemble from your **actual task**. |
| **Strength** | Everyone runs the same test → excellent for **comparing models on common ground**. | Measures what **you specifically care about**, not what is generically useful. |
| **Use it when** | You want to place a model against the field. | You want to know if a model works for *your* product. |

The two are complementary: the public benchmark tells you a model is generally capable; your
own eval set tells you it is capable *at the job you are hiring it for*.

---

## 4. The Eight Capability Domains

Frontier labs evaluate models across eight broad domains. For each, the same four questions
apply: **what it measures**, **what comes under it**, **why labs evaluate it separately**, and
its **real-world relevance**.

### 4.1 Knowledge & Reasoning

**What it measures.** Two things: how much **factual knowledge** the model learned during
training, and whether it can **connect multiple pieces of information** and reason through
them to reach the correct conclusion.

**What comes under it.**
- **Factual recall across subjects** — whether the model knows facts from areas such as biology, physics, chemistry, history, law, medicine, economics, and philosophy. The most basic test of what the model has learned.
- **Multi-step logical reasoning** — whether the model can combine several facts in the correct sequence, following a chain of reasoning rather than answering from a single fact.

**Why labs evaluate it separately.** This domain is commonly used as a proxy for *"How
intelligent is this model?"* It is also the capability most discussed by the media and the
general public, so labs need to show their models match or outperform competitors on widely
recognized knowledge and reasoning benchmarks.

**Real-world relevance.** Analyzing research papers, answering complex customer questions,
writing technically accurate documents, and assisting professionals in law, medicine,
science, and finance.

### 4.2 Coding & Software Engineering

**What it measures.** Whether a model can write code that **actually works**, and whether it
can operate inside **real software engineering environments** — large codebases, multiple
files, development tools, repositories, and complex workflows.

**What comes under it.**
- **Function-level code generation** — writing individual functions from natural-language instructions, handling edge cases, validating inputs, and following the required interface.
- **Real-world bug fixing in existing codebases** — given a real GitHub issue and a repository, identifying the root cause and creating a correct patch without breaking other parts of the software.
- **Multi-file, long-horizon engineering tasks** — changes across several files, understanding cross-component dependencies, refactoring, and adding features.
- **Command-line and system administration** — multi-step terminal tasks such as installing packages, configuring servers, running diagnostics, and combining tools.
- **API and function calling** — making correct API calls with the right parameters, handling errors, and processing returned results.

**Why labs evaluate it separately.** Better coding performance directly improves developer
productivity and creates significant business value. Developers are an important customer
group and a major source of revenue, so labs compete heavily here.

**Real-world relevance.** Code generation, debugging, code review, CI/CD automation, and
software maintenance. Coding evals often reveal some of the **largest performance gaps**
between leading and weaker models.

### 4.3 Mathematics

**What it measures.** Whether a model can perform accurate **symbolic and numerical
reasoning**. Mathematics is especially useful because the final answer is usually simply
**correct or incorrect** — a response cannot succeed just by sounding convincing.

**What comes under it.**
- **Grade-school mathematics and basic arithmetic reasoning** — multi-step word problems that require converting natural language into mathematical expressions and carrying out the correct sequence of calculations.
- **Competition-level problem solving** — problems from competitions such as **AIME**, requiring creative reasoning, combinatorics, geometry, number theory, and pattern recognition.
- **Undergraduate and graduate-level mathematics** — advanced problems from real analysis, abstract algebra, topology, probability theory, and differential equations, often needing long reasoning chains.
- **Research-level mathematical reasoning** — problems close to the frontier of research, where even expert mathematicians may need several days.

**Why labs evaluate it separately.** Mathematics is one of the **cleanest tests of genuine
reasoning** — there is little room for a plausible-sounding but wrong answer. It checks
whether a model performs formal, verifiable reasoning rather than relying only on pattern
matching.

**Real-world relevance.** Scientific computing, financial modelling, engineering
simulations, data analysis, and any application requiring precise numerical results.

### 4.4 Long Context

**What it measures.** Whether a model can effectively **use information from very long
inputs**, sometimes containing hundreds of thousands of tokens.

**What comes under it.**
- **Single-fact retrieval from long contexts** — finding one specific piece of information hidden inside a very long document (the classic "needle in a haystack").
- **Multi-fact co-reference across long texts** — tracking the same person, entity, concept, or constraint across mentions that appear far apart.
- **Aggregation over long content** — summarizing, counting, comparing, or identifying patterns using information spread across a large amount of text.
- **Code repository understanding** — navigating a large codebase and reasoning about cross-file dependencies.

**Why labs evaluate it separately.** Context-window size has become an important marketing
number (**128K → 200K → 1M tokens**), but a large advertised window does **not** guarantee
the model can use all of that information effectively. Labs must demonstrate the model
genuinely uses the capacity it claims.

**Real-world relevance.** Reviewing legal contracts, analyzing academic papers, maintaining
large codebases, handling long customer-support conversations, and working with large
document collections.

### 4.5 Vision & Multimodal

**What it measures.** Whether a model can **understand and reason about visual information**
alongside text.

**What comes under it.**
- **Scientific chart and graph understanding** — reading axes, legends, data points, and trends; extracting exact values and explaining broader meaning.
- **Document and form understanding** — processing scanned documents, invoices, tables, and forms, understanding both the words and how information is arranged on the page.
- **GUI and screen understanding** — interpreting computer or mobile screens: buttons, menus, dialog boxes, input fields, and the current application state.

**Why labs evaluate it separately.** Vision enables tasks text-only systems cannot perform.
It opens new use cases and has become an important area of differentiation now that many
text benchmarks are becoming **saturated**.

**Real-world relevance.** Processing invoices, analyzing charts and research figures,
navigating software interfaces, reading medical images, understanding engineering diagrams,
and working with visually complex documents.

### 4.6 Agentic & Tool Use

**What it measures.** Whether a model can **take actions**, not only generate text. An
agentic model may browse the internet, call APIs, manipulate files, execute code, operate
software, and complete complex multi-step tasks — which requires it to **plan actions,
choose the right tools, track progress, and recover when something goes wrong**. This is
widely considered one of the next major frontiers of AI evaluation.

**What comes under it.**
- **Web browsing and persistent research** — navigating multiple websites, following links, searching, and combining findings from many sources; some tasks require visiting dozens or hundreds of pages.
- **Structured tool calling and API interaction** — selecting the right function, passing valid parameters, handling responses, chaining calls, and recovering from errors.
- **Desktop and computer use** — operating real applications through visual interaction: clicking, typing, and navigating word processors, spreadsheets, email clients, and file managers.
- **Multi-turn task execution with planning** — breaking a large goal into smaller steps, completing them in order, checking intermediate results, and adjusting the plan on failure.

**Why labs evaluate it separately.** A model may **know** a lot yet be unable to **use** that
knowledge to complete a practical task. Agentic evaluation measures the gap between *what the
model knows* and *what the model can actually do* — a gap that remains large even among
frontier models.

**Real-world relevance.** Coding agents, research assistants, customer-service bots,
data-entry automation, personal assistants, and any workflow where the AI must **perform
actions** rather than simply provide an answer.

### 4.7 Safety & Alignment

**What it measures.** Whether a model can be **trusted to behave responsibly** — refusing
harmful requests, avoiding deception and sycophancy, resisting adversarial attacks, and
continuing to follow safety principles even under user pressure.

**What comes under it.**
- **Refusal of harmful content generation** — appropriately refusing requests involving cybercrime, violence, harassment, misinformation, and illegal activities, while **not becoming so cautious** that it refuses harmless, legitimate requests.
- **Resistance to adversarial attacks and jailbreaks** — maintaining safety rules against prompt injection, role-playing, encoded instructions, and other adversarial methods.
- **Truthfulness versus sycophancy** — giving the correct answer even when it disagrees with the user's beliefs or expectations; a reliable model does not simply tell users what they want to hear.
- **Cybersecurity capabilities** — evaluating advanced tasks such as cryptography, reverse engineering, digital forensics, and exploitation, which is important for understanding potentially dangerous autonomous capabilities.

**Why labs evaluate it separately.** Safety is both a **regulatory requirement** and a major
**reputational concern**.

**Real-world relevance.** Any organization deploying a model publicly must verify its safety
controls work. This domain directly affects legal liability, regulatory compliance, brand
reputation, user trust, and responsible deployment.

### 4.8 Instruction Following

**What it measures.** Whether a model does **exactly what the user asked — no more and no
less**. Instructions may concern format, length, tone, style, structure, or content. A model
with excellent knowledge can still be frustrating if it repeatedly ignores simple
instructions.

**What comes under it.**
- **Verifiable constraint satisfaction** — following instructions that can be checked automatically: staying within a word limit, using bullet points, producing valid JSON, following a markdown structure, or using specific section headings.
- **Handling ambiguous or incomplete instructions** — inferring what the user probably wants when a request is underspecified, asking a clarifying question when necessary, and choosing reasonable defaults when appropriate.

**Why labs evaluate it separately.** Instruction following strongly affects whether a model
feels **helpful or frustrating** in everyday use. It is one of the capabilities most directly
connected to user satisfaction in real production environments.

**Real-world relevance.** Almost every practical use depends on it: generating reports in a
required format, writing code to specific style rules, producing structured JSON, following
brand-tone requirements, and maintaining coherent multi-turn conversations.

---

## Summary: The Eight Domains at a Glance

| # | Domain | Core question | Example benchmark |
| :---: | --- | --- | --- |
| 1 | **Knowledge & Reasoning** | How much does it know, and can it reason across facts? | MMLU |
| 2 | **Coding & Software Engineering** | Can it write working code in real repos? | SWE-bench, HumanEval |
| 3 | **Mathematics** | Can it reason to a verifiably correct answer? | GSM8K, AIME |
| 4 | **Long Context** | Can it use very long inputs effectively? | Needle in a Haystack |
| 5 | **Vision & Multimodal** | Can it understand images with text? | MMMU |
| 6 | **Agentic & Tool Use** | Can it take actions, not just answer? | Web/agent benchmarks |
| 7 | **Safety & Alignment** | Can it be trusted to behave responsibly? | Jailbreak / refusal suites |
| 8 | **Instruction Following** | Does it do exactly what was asked? | IFEval |

---

*Notes on the Model Evals framework and the eight capability domains. Continues in
[Model Evals — Benchmarks & Harnesses](./07-model-evals-benchmarks-and-harnesses.md).*
