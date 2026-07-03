# GLOSSARY.md — Shared terms

> Purpose: the fixed vocabulary of the design documents, so every document uses the same
> word for the same thing. Terms below are fixed by the PRD; later sessions may add terms
> but should not redefine these.

**Agent** — a reasoning unit (e.g. Planner, Retrieval, Reader, Evidence, Writer, Critic,
Contradiction Detector, Gap Detection, Novelty Estimation, Hypothesis Generator, Experiment
Design). Each agent defines its responsibilities, inputs, outputs, confidence mechanism,
stop criteria, and memory read/write interactions. Distinct from services, state stores,
memory, and orchestration (PRD §4).

**Service** — a stateless capability used by agents: retrieval, PDF/parse, embedding,
citation-verification, and the provider-agnostic LLM gateway. Services hold no durable
state of their own.

**State store** — durable data the system persists: paper store, run/task state, knowledge
graph (M2+), hypothesis/experiment stores (M3+), artifact/object store.

**Memory** — contextual recall with explicit per-agent read/write contracts. Four kinds:
conversation, paper (chunked papers + metadata in a vector store), research/task (current
run state), and user (preferences, prior projects, standing constraints; M3+). Memory is
about *recall for reasoning*; state stores are about *durable data* — the two layers are
kept separate.

**Orchestration** — the LangGraph control flow binding agents, services, state, and memory:
the state machine defining which nodes run, which loop, termination and max-iteration caps,
retries, interrupts, and human-in-the-loop gates.

**Groundedness** — the property that every asserted factual claim cites a specific retrieved
span of source text. Applies to every capability, including gap-finding and hypotheses: a
proposed gap or hypothesis must point to the evidence that motivates it. The primary
internal evaluation metric, measurable without external gold data.

**Source adapter** — the interface behind which all research sources sit, making the system
domain-general: operating in a new field is a configuration/adapter change, not a redesign.

**Milestone** — a dependency-ordered build stage (M1 evidence foundation, M2 analytical
reasoning, M3 research ideation, M4 advanced/optional). Milestones govern build order, not
design depth: every agent is designed in full regardless of milestone (see ADR-001 in
`DECISIONS.md`).
