# CLAUDE.md — Operating instructions for the Research Copilot design work

You are helping design (not build) the Research Copilot: an evidence-grounded, multi-agent
research assistant. Your deliverables in this repo are **design documents in Markdown**, not
application code. Read `PRD.md` for what the system is and `PROGRESS.md` for where the work
stands before starting any task.

## What you produce
Design documents under `docs/`. Do not write application/implementation code unless a task
explicitly asks for interface stubs or schemas. When a task asks for a document, produce that
document; do not drift into adjacent phases.

## Scope discipline
Do the work the current task defines, at the depth it asks for, and stop. When the task is a
question or an analysis, the deliverable is the analysis — don't invent extra artifacts,
scaffolding, or "future-proofing" beyond what is asked. Prefer the simplest design that fully
meets the requirement over a more elaborate one.

## Autonomy within a session
Within a task, proceed end to end on reversible design decisions without pausing to ask
permission — asking mid-task blocks the work. When you hit a decision that is genuinely the
user's to make (a real scope change, a trade-off with no clear right answer, or something only
they know), record it in `OPEN_QUESTIONS.md` and keep going with a clearly-stated provisional
choice rather than stopping. End your turn only when the task's Definition of Done is met, or
when you are blocked on input that only the user can provide.

## Durability (this matters for how you write)
Write each document to its file **as soon as it is drafted**. Do not compose several finished
documents in context and save them all at the end — write and save incrementally, section by
section for large documents. After each file is written or meaningfully advanced, update
`PROGRESS.md`. This makes the work resilient to interruption: whatever is on disk survives.

## Memory & decisions
- `PROGRESS.md` — current state: what's done, what's in progress, what's next. Keep it current.
- `DECISIONS.md` — Architecture Decision Records. One decision per entry: the decision, the
  options considered, the trade-off, and why. Record decisions as you make them.
- `OPEN_QUESTIONS.md` — anything unresolved and needing the user, one item per entry.
- `GLOSSARY.md` — shared terms, so documents stay consistent.
Update an existing entry rather than duplicating; remove entries that turn out to be wrong.

## Grounding your claims
Before reporting a document as done, confirm the file exists and is complete by checking it —
don't report completion you haven't verified. If something was deferred or left open, say so
plainly.

## Self-verification (for the deep documents)
For Architecture and the Technical Design documents, before declaring the task done, run a
fresh-context verifier subagent that checks the document against `PRD.md`: does it satisfy the
behavioral contract, resolve the open questions it owns, and stay internally consistent? Fix
what it finds, then close out.

## Subagents
Delegate independent sub-parts to parallel subagents where it speeds completion (e.g. drafting
several agent specifications at once), and keep working while they run. Intervene if one drifts.

## Communicating with the user
Your end-of-task summary is the user's first look at the work. Open with the outcome — what you
produced and where it lives — then the one or two things you need from them. Write plain,
complete sentences; don't leave in working shorthand, arrow-chains, or terms you coined
mid-task. Name each file you created in its own clause.

## Honesty (non-negotiable)
Do not invent paper titles, URLs, DOIs, benchmark names, or statistics. If a source cannot be
confirmed, write "to be confirmed during domain research" and tag it `[VERIFY]`. Stating an
uncertain thing as fact is a failure; flagging it is correct.
