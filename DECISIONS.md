# DECISIONS.md — Architecture Decision Records

> Purpose: one entry per design decision — the decision, the options considered, the
> trade-off, and why. Recorded as decisions are made; updated (not duplicated) if a
> decision is revisited.

---

## ADR-001 — Milestones sequence the build; they do not cut the design

**Status:** Accepted (Session 0, 2026-07-03). Source: PRD §3.

**Decision.** The Research Copilot is built in three dependency-ordered milestones that
*together* constitute the product, plus one genuinely optional fourth:

1. **Milestone 1 — Evidence foundation:** Planner, Retrieval (subsuming search-strategy),
   Reader, Evidence (extraction + synthesis), Writer, Critic agents; conversation, paper,
   and research/task memory; the core plan → retrieve → read → extract → synthesize →
   write → critique loop with human-in-the-loop gates.
2. **Milestone 2 — Analytical reasoning:** Contradiction Detector and Gap Detection agents;
   the cross-paper knowledge graph.
3. **Milestone 3 — Research ideation (the copilot proper):** Novelty Estimation, Hypothesis
   Generator, Experiment Design agents; Hypothesis Tracker and Experiment Planner state;
   user memory.
4. **Milestone 4 — Advanced / optional:** multimodal (staged), adaptive orchestration,
   debate, provenance graph, full production infrastructure, additional source adapters.

Milestones 1–3 are the copilot; milestone 4 is optional/research-grade. **Every agent is
specified in full depth in the Technical Design regardless of its milestone** — milestones
govern *build order*, not *design depth*. The exact merge/split of agent roles within a
milestone is a TDD decision (PRD open question 2), not fixed here.

**Options considered.**

- **(a) Cut scope:** define a smaller product (e.g. only the evidence foundation) and treat
  the rest as speculative future work, designing it shallowly or not at all.
- **(b) Sequence scope (chosen):** keep the full ambition as the product, order the build by
  dependency, and design everything at full depth now.
- **(c) Design breadth-first at milestone-matched depth:** design M1 deeply and later
  milestones only as sketches, deepening each when its build begins.

**Trade-off and why.** Cutting (a) would misrepresent the product: the differentiating value
is milestone 3 (ideation — the "junior researcher" behavior), which is unreachable without
the M1/M2 substrate; a design cut at M1 would optimize the substrate and lose the point.
Milestone-matched depth (c) risks M1 design decisions (data model, memory contracts,
orchestration topology) that quietly foreclose M2/M3 needs, forcing redesign mid-build — the
knowledge graph, hypothesis stores, and confidence machinery all impose requirements on the
foundation. Sequencing (b) costs more design effort up front but is the right fit for a solo
builder over ~a year with a later teammate handoff (PRD §10): the docs must let the system be
built incrementally without re-litigating the design at each milestone boundary. Dependency
order (evidence → reasoning-over-evidence → ideation) also mirrors the grounding discipline:
each layer consumes the checkable substrate beneath it.

**Consequences.** The TDD specifies all agents across M1–M3 (and M4's staged multimodal
tiers) in full depth. Infrastructure is milestone-gated (Redis, object storage, workers,
CI/CD are M4). Roadmap and risk documents track milestone boundaries, not scope reductions.
