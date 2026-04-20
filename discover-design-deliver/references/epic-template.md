# Epic Template (Jira)

Follows the unified artifact template (shared core plus Epic-specific tails). Use this for any epic. The same Problem, Desired End State, Principles, Out of Scope, and Why This Matters content should be mirrored from the parent Idea (in roadmap project) and the implementing PRD (in Confluence).

## Purpose

An epic coordinates engineering execution. Its audience is the platform team (engineers, scrum master, EM) and the executives who review progress. The Epic owns:

- How the work is sequenced
- Which decisions are locked and should not be revisited
- Which domain areas need coverage
- How upstream and downstream surfaces interact with this work
- The suggested story ordering

The Epic does not own the strategic narrative — that's the Idea's job. The Epic does not own the design reference — that's the PRD's job.

---

## Header (links to Idea and PRD)

Start the epic description with two links, before the first heading:

```
**Parent Idea:** [ROADMAP-XX Title](https://tstllc.jira.com/browse/ROADMAP-XX)
**PRD:** [PRD Title](https://tstllc.jira.com/wiki/...)
```

This makes the strategic chain scannable from the Jira epic view.

---

## Shared Core (mirror from Idea and PRD)

The first five sections are shared verbatim with the parent Idea and the PRD.

### Problem

Same content as Idea and PRD.

### Desired End State

Same content as Idea and PRD.

### Principles

Short list with a pointer back to the PRD for full elaboration:

> These five principles are shared verbatim with the parent Idea and the PRD. They are the guard rails for every decision downstream.
>
> 1. [Principle 1]
> 2. [Principle 2]
> 3. [Principle 3]
> 4. [Principle 4]
> 5. [Principle 5]
>
> Full elaboration lives in the [PRD](link).

Keep the Epic's version brief. Engineers need to see the principles in Jira without clicking away, but the authoritative elaboration lives in the PRD.

### Out of Scope

Same content as Idea and PRD.

### Why This Matters

Same content as Idea and PRD.

---

## Epic-Specific Tails

### Domains

Domain sections cover the specific areas the epic touches. Typical domain sections include (adapt per initiative):

- **UX / UI** — short shape pointer plus bullets. Full design reference lives in the PRD.
- **Product Types or Surfaces** — which product types, surfaces, or integrations this affects
- **Decoupling or Migration** — work to move legacy functionality out of this surface
- **Upstream Surfaces** — what feeds this, what contract it expects
- **Rollout** — how the ship model works in engineering terms
- **Infrastructure** — design system, state management, performance, third-party integrations

Keep domain sections short (one paragraph each) and point to the PRD for design detail. Domain sections describe the engineering scope, not the design spec.

### Key Decisions

Product decisions that constrain the solution space. These should not be revisited without explicit re-alignment. Typical decisions include:

- Audience or cohort rollout order
- How upstream surfaces feed this work
- Whether audience differences fork code or are feature-flagged
- Standard template or shared shape commitments
- Dependencies on other teams or products
- Scope conventions (epics vs stories, tracking granularity)

Four to six decisions is usually right. Each should be bold-lead plus a short explanation.

### Suggested Story Ordering

Dependency-aware sequencing. Structure by readiness state:

- **In flight** — stories already committed or active
- **Next (scheduled or demand-driven)** — the next batch, with the trigger
- **Parallel workstreams** — stories that run alongside the main sequence
- **Blocked** — tracked but not sequenced, with the blocker named

Include active Jira identifiers where possible. Note that engineering may group stories into sub-epics later if it helps execution, but the default scope is stories-only under the epic.

---

## Writing Voice

Epic descriptions are read by engineers during sprint planning and by executives during reviews. Use the written register: full sentences, logical connectives, no fragments, no em dashes. Principles and decisions should be scannable.

## Reference

- Unified template source of truth: `~/.claude/projects/-Users-you-work-project/memory/feedback_unified_artifact_template.md`
- Reference implementation: PROJ-5 Example Epic
