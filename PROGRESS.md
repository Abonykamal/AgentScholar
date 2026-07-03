# PROGRESS.md — Current state of the Research Copilot design work

> Purpose: the single source of truth for where the design work stands — what's done,
> what's in progress, and what comes next. Updated at the end of every session and after
> each document is written or meaningfully advanced.

## Planned document set and session sequence

Derived from `PRD.md` (design phases: SRS → Architecture → TDD → Evaluation → Roadmap).
Each session produces the named document(s) under `docs/`.

| # | Session | Deliverable(s) | Status |
|---|---------|----------------|--------|
| 0 | Scaffold (this session) | Repo structure; `PROGRESS.md`, `DECISIONS.md`, `OPEN_QUESTIONS.md`, `GLOSSARY.md`; ADR-001 (milestone model) | ✅ Done |
| 1 | SRS | `docs/srs.md` — Software Requirements Specification: functional & non-functional requirements, behavioral contract (PRD §5), non-goals (PRD §6) | ⬜ Not started |
| 2 | Architecture | `docs/architecture.md` — system layers (PRD §4), component topology, orchestration shape, owns open questions 1 & 4 (with TDDs) | ⬜ Not started |
| 3 | TDD: Agents | `docs/tdd-agents.md` — full-depth spec for every agent across all milestones; owns open questions 2 & 3 | ⬜ Not started |
| 4 | TDD: Information Flow + Data Model | `docs/tdd-info-flow-data-model.md` — entities/relationships (paper, chunk, claim, evidence, contradiction, gap, hypothesis, experiment, citation, run); owns open question 7 | ⬜ Not started |
| 5 | TDD: Memory + Tools | `docs/tdd-memory-tools.md` — memory stores & per-agent read/write contracts, source-adapter interface, tool specs; owns open questions 4 (retrieval, with Architecture) & 6 | ⬜ Not started |
| 6 | TDD: Orchestration | `docs/tdd-orchestration.md` — LangGraph state machine: loops, termination, retries, interrupts, human-in-the-loop gates; owns open question 1 (with Architecture) | ⬜ Not started |
| 7 | Evaluation + Observability | `docs/evaluation-observability.md` — metric-by-metric measurement methods (PRD §8), per-run observability (PRD §9); owns open questions 5 & 8 | ⬜ Not started |
| 8 | Risk + Roadmap | `docs/risk-roadmap.md` — risk register, milestone-gated roadmap (PRD §3, §10) | ⬜ Not started |

## Current state

- **Done:** Session 0 (scaffold). Repo tree (`docs/`) and the four control files exist.
  ADR-001 (milestone model) recorded in `DECISIONS.md`. `GLOSSARY.md` seeded with the
  PRD-fixed terms.
- **In progress:** nothing.
- **Next:** Session 1 — write `docs/srs.md`.

## Notes

- Every agent is designed in full depth regardless of milestone (ADR-001); milestones govern
  build order only.
- Open design questions live in PRD §7; each design document owns specific ones (mapped in
  the table above). Anything still open at a session's end goes to `OPEN_QUESTIONS.md`.
