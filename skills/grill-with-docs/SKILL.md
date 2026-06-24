---
name: grill-with-docs
description: Run a one-question-at-a-time research grilling session that sharpens AI research terminology, thesis, hypotheses, experiments, and decisions, then preserves the durable parts as project-owned research docs. Use when the user wants to stress-test a research direction, align vocabulary with an agent, clarify a paper or project thread, scope experiments before committing crew time, or preserve research context, especially for projects run through a Nemo firstmate.
---

# Grill with docs

Run a relentless research interview, then preserve the useful parts as project-owned research context.

The grilling is a conversation with the user; the durable output is research docs (shared vocabulary, the live research thread, and decisions worth not relitigating). How those docs get written depends on where you are running (see "Working with Nemo").

## When to use it

Reach for this when the user wants to:

- pin down what an overloaded term means, so they and the agent stop talking past each other;
- stress-test a thesis, hypothesis, or experiment plan before spending crew time on it;
- clarify a paper or project research thread;
- record a research or evaluation decision so future agents do not reopen it.

A grill is interactive by nature, so it runs in the conversation with the user, never as a fire-and-forget background task.

## Core behavior

Ask one question at a time. For each question:

1. State the ambiguity or weak point.
2. Give your recommended answer or current best guess.
3. Wait for the user's response before moving on.

If a question can be answered by light reading of the codebase, papers, notes, experiment logs, or existing docs, read those instead of asking. If it needs real investigation (reproduce a result, dig through runs, audit code), that is a separate task, not something to do inline (see "Working with Nemo").

Push on:

- overloaded terms and names the user and agent may interpret differently
- the main research claim and what would make it false
- hidden assumptions doing most of the work
- the smallest experiment that could change the decision
- baselines, metrics, datasets, and evaluation artifacts
- whether a result is evidence for the thesis or only a benchmark movement
- missing links between paper claims, code, runs, plots, and qualitative examples

Prefer useful disagreement over agreement. Do not let polished language hide uncertainty.

## Working with Nemo

In a Nemo fleet you are the firstmate: the user's conversational point of contact, read-only over projects, delegating all project changes to crewmates. A grill fits this cleanly, because grilling is a conversation with the captain, which a crewmate can never have. So you run the grill yourself; only the durable writes are delegated.

### Detecting a firstmate home

You are in a firstmate home when `FM_HOME` is set, or when the working directory is a firstmate repo (a `data/`, `state/`, `bin/fm-*` layout with project clones under `projects/`). When unsure, check `FM_HOME` first; fall back to the layout. If neither holds, treat it as a plain project or worktree (see "Running inside a project").

### Running the grill as firstmate

- The grill is a conversation, not project work, so run it directly with the captain. Read under `projects/` only to ground questions; never edit anything there.
- Stage the evolving synthesis in `data/` (the firstmate's holding area for not-yet-committed project knowledge), not in the project. Keep it scratch and current.
- When a grill question needs real investigation rather than light reading, dispatch a **scout** task and fold its report back into the grill instead of digging yourself.

### Persisting research docs

When terms, thesis notes, or a decision stabilize enough to guide future work, the project copy must be written by a crewmate, not by you.

- Dispatch a **ship** task whose deliverable is the changed research docs. Put the durable content in the brief: the exact terms, thesis notes, decisions, and the target files to create or update.
- Have the crewmate also add a one-line pointer to the research docs from the project's `AGENTS.md`, since in Nemo that file is the canonical entry point every future crewmate reads. Research context that nothing links to is an island.
- Route the task through the firstmate's normal delivery flow; that machinery is owned by the firstmate's own instructions, so do not re-specify commands here.

### Turning the grill into Nemo tasks

A good grill produces more than docs. The "Next experiments" and "Open questions" it surfaces are task seeds: each is a candidate scout (find out) or ship (build it) task. On wrap-up, offer them to the captain as queued backlog candidates, scoped enough to dispatch, rather than leaving them as loose prose.

### Running inside a project repo or crewmate worktree

If the working directory is the actual project repo or a crewmate worktree, update the research docs directly, following the project's `AGENTS.md` and local repository conventions.

## Research docs

Create files lazily, only when there is durable content to record. If the project already has a research-note or knowledge convention (including content folded into `AGENTS.md`), follow it instead of opening a second home for the same knowledge.

Default layout when none exists:

```text
docs/research/
  context.md       canonical terms and research objects
  thread.md        current research thread, claims, hypotheses, and evidence state
  decisions/
    0001-slug.md   durable research or evaluation decision
```

### `context.md`

Shared vocabulary, glossary-like, not a scratchpad. Each entry defines what the term means in this project and, when useful, what to avoid.

```md
## Language

**Capability elicitation**:
What this project means by the term in one or two tight sentences.
_Avoid_: using it to mean generic prompt optimization.

**Evaluation episode**:
The unit of interaction that produces one scored outcome.
_Avoid_: run, sample, trajectory when those mean narrower objects in this repo.
```

Only include project-specific terms, constructs, datasets, metrics, methods, and artifacts. Do not add general ML or programming concepts unless the project uses them in a special way.

### `thread.md`

The live research spine. Keep it compact and current, short bullets with links to papers, code, runs, plots, or notes, uncertainty marked explicitly.

```md
# Research thread

## Core question

## Working thesis

## Hypotheses

## Evidence so far

## Falsifiers

## Next experiments

## Open questions
```

### `decisions/`

Record a decision only when it is durable enough that future agents would otherwise relitigate it: choosing the primary benchmark, rejecting a baseline for a non-obvious reason, changing the research question after evidence, deciding a qualitative failure class matters more than an aggregate metric, or committing to a design with real switching cost. Skip decisions that are obvious, cheap to reverse, or only logistical.

```md
# {Short decision title}

{1-3 sentences explaining the context, the decision, and why.}
```

## When to update docs and wrap up

Update docs when a term or decision stabilizes enough to guide future work. Do not persist every speculative thought; during early ambiguity keep the synthesis in the conversation (or in `data/` as firstmate). Persist only what should constrain or orient future agents.

Before finishing, report:

- the sharpest clarified terms
- the current research thread
- any durable decisions recorded, or queued as a ship task for a crewmate to write
- the next experiments and open questions, offered as candidate Nemo tasks
- the next unresolved question
