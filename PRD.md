# Research Copilot — Product Requirements Document (PRD)

> **Status:** Design phase. This is the north-star specification. It defines *what* the
> system is and *why*, the build milestones, and the open design questions the phased design
> documents (SRS → Architecture → TDD → Evaluation → Roadmap) must resolve. It deliberately
> does **not** pre-decide the detailed engineering — that is left to the design phases.
>
> **`[VERIFY]` tags:** anywhere you see `[VERIFY]`, the claim, source, tool, or benchmark is
> **not** confirmed and must be checked during domain research before it is relied on.

---

## 1. Vision

An **autonomous, multi-agent research copilot** that collaborates with a researcher across the
**entire research lifecycle — from refining an idea to drafting a paper** — while remaining
evidence-grounded, transparent, and continuously self-evaluating.

It behaves like a capable **junior researcher**: it plans before acting, asks when genuinely
uncertain, retrieves and reads the literature, extracts and compares evidence, detects
contradictions and gaps, estimates novelty, generates hypotheses, proposes experiments,
drafts and critiques its own writing, remembers prior work, grounds every claim in a
retrievable source, and explicitly flags what it does not know.

The system is **domain-general by design**: research sources sit behind an adapter interface,
so operating in a new field is a configuration/adapter change, not a redesign.

---

## 2. Target users and problem

**Primary users:** undergraduate and master's researchers doing research-intensive work —
scoping a thesis, orienting in an unfamiliar subfield, building a related-work section, or
moving from a rough idea toward testable hypotheses and a paper.

**Core pain points:**

- The literature is large, fast-moving, and hard to search comprehensively.
- Reading, extracting, and cross-comparing evidence across many papers is slow.
- Moving from "what is known" to "what is contested, what is missing, and what is worth trying
  next" is exactly the hard, high-value part — and the part novices struggle with most.
- Current AI tools frequently **hallucinate citations** or assert claims no source supports,
  which is disqualifying for research use.

**The differentiator across every capability is the same:** *grounded, checkable, transparent*
reasoning. Whether the copilot is summarizing, detecting a gap, or proposing an experiment,
every factual claim is traceable to a span of retrieved text, the process is visible, and the
system's uncertainty is explicit. This grounding discipline is a property of the whole copilot,
not just its retrieval step.

---

## 3. Build milestones (sequencing, not a redefinition of scope)

The full ambition — an autonomous multi-agent research copilot across the whole lifecycle — is
the product. The milestones below are an **implementation order driven by dependency**, so the
system can be built incrementally over ~a year. **Milestones 1–3 together *are* the copilot.**
Milestone 4 is genuinely optional / research-grade. **Every agent is specified in full depth in
the Technical Design regardless of milestone** — milestones govern *build order*, not
*design depth*. The exact merge/split of agent roles within a milestone is a design decision
for the TDD, not fixed here.

### Milestone 1 — Evidence foundation

The grounded substrate every higher capability consumes; first only for that dependency reason.

- **Agents:** Planner · Retrieval (subsuming search-strategy) · Reader · Evidence (extraction +
  synthesis) · Writer · Critic (review + reflection loop).
- **Memory:** conversation, paper (chunked papers + metadata in a vector store), research/task
  (current run state).
- **Tools:** a primary search/metadata source + a full-text path + PDF parsing + embeddings +
  vector DB, all behind the source-adapter interface.
- **Loop:** plan → retrieve → read → extract → synthesize → write → critique →
  (revise or retrieve-more) → deliver, with human-in-the-loop gates at genuine ambiguity and
  weak-evidence decisions.
- **Output:** an evidence-grounded, fully-cited synthesis with a visible reasoning/evidence
  trace.

### Milestone 2 — Analytical reasoning over the corpus

Reasoning *about* the literature, not just summarizing it.

- **Agents:** Contradiction Detector · Gap Detection.
- **State:** a cross-paper **knowledge graph** (entities and relations) supporting contradiction
  and gap reasoning.

### Milestone 3 — Research ideation (the copilot proper)

The defining "junior researcher" behavior — the point of the product.

- **Agents:** Novelty Estimation · Hypothesis Generator · Experiment Design.
- **State:** Hypothesis Tracker · Experiment Planner.
- **Memory:** user memory (preferences, prior projects, standing constraints).

### Milestone 4 — Advanced / genuinely optional

- **Multimodal**, staged: figure/table extraction → chart reading → microscopy/scans.
  (Microscopy/scan interpretation is effectively its own research problem; its own sub-tier.)
- Adaptive orchestration, multi-agent debate, research provenance graph, long-term research
  companion, self-improving evaluation.
- Full production deployment: Redis, object storage, background workers, CI/CD, polished
  frontend.
- Additional source adapters (Semantic Scholar, OpenAlex, Crossref, arXiv, GitHub, Papers With
  Code, Hugging Face). `[VERIFY]` API terms and rate limits per source; several overlap, so
  choose deliberately.

---

## 4. System layers (keep these categories separate)

The original sketch mixed reasoning agents with services and state. Separate four layers:

- **Agents** — reasoning units (the milestone lists above). Each defines: responsibilities,
  inputs, outputs, confidence mechanism, stop criteria, and memory read/write interactions.
- **Services** — stateless capabilities: retrieval, PDF/parse, embedding, citation-verification,
  and a **provider-agnostic LLM gateway** (the cross-provider failover pattern already proven in
  the Patient Journey Simulator).
- **State stores** — durable data: paper store, run/task state, knowledge graph (M2+),
  hypothesis/experiment stores (M3+), artifact/object store.
- **Memory** — contextual recall with explicit per-agent read/write contracts: conversation,
  paper, research/task, user (M3+).
- **Orchestration** — the LangGraph control flow binding the above.

---

## 5. Behavioral contract (product-level requirements)

Non-negotiable properties of the output, across *every* capability:

- **Groundedness.** Every asserted factual claim cites a specific retrieved span. No uncited
  factual claims. Applies to gap-finding and hypotheses too — a proposed gap or hypothesis must
  point to the evidence that motivates it.
- **No fabricated citations.** Never invent a paper, DOI, quote, or statistic. If a claim can't
  be grounded, say so or omit it.
- **Transparency.** The full trace — agent steps, tool calls, retrieved evidence, decisions — is
  inspectable.
- **Calibrated uncertainty.** Surface confidence; abstain or flag when evidence is weak or
  conflicting, rather than asserting.
- **Reproducibility.** A run is reconstructable from logs (accepting LLM nondeterminism); all
  steps are logged.

---

## 6. Non-goals (early milestones)

- Not a general-purpose chatbot.
- Not a **clinical decision-support or medical-advice** tool. If a biomedical domain is used, the
  system must not be positioned or usable as patient-facing medical guidance — state this in the
  SRS and surface it in the product.
- Not executing experiments; it *designs* them (M3), it does not run them.
- Not real-time; asynchronous/batch research runs are expected.

---

## 7. Open design questions the phases must resolve

Deliberately unresolved here; each design document owns specific ones. Anything still open at a
session's end goes to `OPEN_QUESTIONS.md`.

1. **Orchestration topology & termination** (Architecture + TDD Orchestration). Reconcile the
   linear lifecycle with the loops into one explicit LangGraph state machine: which nodes loop,
   loop-termination and max-iteration caps, retry policy, interrupts, and human-in-the-loop
   gates.
2. **Agent boundaries** (TDD Agents). Confirm the M1 role set; decide which agents merge per
   milestone.
3. **Confidence mechanism** (TDD Agents). How confidence is *computed* — not LLM self-report.
   Candidate signals: evidence count, retrieval scores, fresh-context verifier agreement.
4. **Retrieval strategy** (Architecture + TDD Tools). Hybrid retrieval: BM25 + dense +
   reciprocal-rank fusion + reranking + query expansion; precision/recall targets. (Connects
   directly to the Production-Grade RAG curriculum, Phase 3.)
5. **Citation-grounding verification** (TDD + Evaluation). How each claim is checked against its
   cited span — exact/semantic match or entailment. This makes groundedness measurable.
6. **Memory coherence** (TDD Memory). Consistency across stores: deduplication, staleness,
   conflict resolution.
7. **Data model** (TDD Data Model). Entities/relationships for: paper, chunk, claim, evidence,
   contradiction, gap, hypothesis, experiment, citation, run.
8. **Evaluating the generative capabilities** (Evaluation). Novelty, hypothesis quality, and
   experiment-design quality are far harder to score than retrieval or groundedness. How are
   they judged (rubric + human, LLM-judge with a rubric, or held-out proxy)? Flag honestly that
   this is an open research-y problem, not a solved metric.

---

## 8. Evaluation philosophy (full design in the Evaluation Plan)

Metrics without a measurement method are decoration. The Evaluation Plan must specify **how each
metric is measured and against what**:

- **Groundedness / citation faithfulness** — the primary internal metric; *measurable without
  external gold data* by verifying each cited claim against its retrieved span (see open
  question 5). Automatable.
- **Retrieval precision/recall** — needs relevance judgments; define how they are obtained.
- **Generative-capability quality** (synthesis, gap-finding, novelty, hypotheses, experiments) —
  needs rubrics and likely human or LLM-judge scoring; see open question 8. Do not pretend a
  single number captures this.
- **A concrete evaluation domain.** Evaluation needs at least one domain with known-answer
  benchmarks to be measurable at all. Biomedical is the pragmatic first choice (structured
  evidence, existing benchmarks) — this is an *evaluation anchor*, not the product's identity or
  ceiling. Candidate benchmarks to assess for fit, licensing, and current availability during
  Phase 10 `[VERIFY each]`: PubMedQA, BioASQ, BixBench. Assume none is suitable until checked.
  *(If the project drops the biomedical anchor for a fully domain-general evaluation, this
  section and the candidate list change accordingly.)*
- **Operational** — hallucination rate, latency, token cost, loop count, tool usage.

## 9. Observability (design in the Evaluation Plan)

Per-run: agent timeline, tool calls, token usage and cost, memory accesses, loop iterations,
failure points, confidence values — enough to reconstruct and debug any run.

---

## 10. Constraints and context

- **Team:** solo foundation-builder now; teammate handoff later. Docs are written to be read and
  implemented incrementally over ~a year.
- **Stack bias:** provider-agnostic LLM layer; LangGraph; FastAPI. Infrastructure is **milestone-
  gated** — Redis / object storage / background workers / full CI/CD are Milestone 4, not early
  requirements.
- **Honesty discipline (applies to these docs too):** do not invent paper titles, URLs, DOIs,
  benchmark names, or statistics. Mark anything unconfirmed `[VERIFY]`. "Source to be confirmed
  during domain research" is correct; a fabricated source is a failure.
