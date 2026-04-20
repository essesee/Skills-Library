# Idea Template (MDP Roadmap & Discovery)

Follows the unified artifact template (shared core plus Idea-specific tails). Use this for any roadmap Idea. The same Problem, Desired End State, Principles, Out of Scope, and Why This Matters content should be mirrored into the PRD (in Confluence) and the implementing Epic (in Jira).

## Purpose

An Idea lives in the roadmap project. Its audience is executives, the advisory group, and anyone scanning the roadmap. The Idea owns the strategic narrative for an initiative:

- Why this matters
- Where it fits in the platform portfolio
- What phasing looks like
- How success is measured
- What's still unknown
- Which other initiatives depend on it

The Idea does not own the design reference — that's the PRD's job. The Idea does not coordinate engineering execution — that's the Epic's job.

---

## Shared Core (mirror to PRD and Epic)

The first five sections are shared verbatim with the PRD and Epic. Write them once, then copy across artifacts.

### Problem

What's broken, who it hurts, why it matters now. Name compounding issues explicitly and lead with any forcing functions (framework end-of-life, compliance deadline, contractual dependency).

### Desired End State

What "done" looks like from the user's perspective. Concrete enough that a reader can visualize the future state.

### Principles

Four to six principles that act as guard rails for every decision downstream. Format each as a bold lead sentence plus a short elaboration. Shared verbatim with the PRD and Epic.

### Out of Scope

What this initiative explicitly does not do, with reasons.

### Why This Matters

Business value and strategic fit. Name the forcing function if there is one.

---

## Idea-Specific Tails

### Phased Approach

How the work rolls out over time. Structure by status rather than date:

- **Completed and in flight** — specific stories, epics, or milestones already shipped or being worked
- **Next** — the next scheduled or expected step, with the trigger (platform-scheduled, product-team-driven, demand-driven)
- **Then** — subsequent steps, in the order they are expected to unlock
- **Parallel** — workstreams that run alongside the main sequence
- **Future, not yet scheduled** — deferred or blocked items, with reasons

Link active work items (PROJ-, ROADMAP-) directly where possible.

### Success Metrics

Four or fewer signals that anchor validation. For each:

- Signal name and category (primary, reliability, cost, revenue)
- What it measures
- Measurement source
- Any instrumentation dependencies

The Idea-level metrics should match the PRD's metrics. The PRD expands them with baselines, targets, and cadence; the Idea names them.

### Open Questions

Unresolved items that affect prioritization or execution. Two to four is usually right. Each question should:

- Describe the uncertainty
- Name the decision or metric it blocks
- Indicate what would resolve it

Executive readers use this section to evaluate whether the initiative is ready to move out of Parking Lot status.

### Related Ideas

Other MDP Ideas that share scope, infrastructure, or audience with this one. For each:

- MDP identifier and title
- Nature of the relationship (depends on, unblocks, shares infrastructure with, subsumed by)

Use this section to help executives see the portfolio-level connections. Avoid listing Epics here — use the implementing Epic link in the Idea header instead.

---

## Writing Voice

Ideas are exec-visible. Use the written register: full sentences, logical connectives, no fragments, no em dashes. The forcing function, persona impact, and measurable outcomes should be scannable in the first two sections.

## Reference

- Unified template source of truth: `~/.claude/projects/-Users-you-work-project/memory/feedback_unified_artifact_template.md`
- Reference implementation: ROADMAP-35 Checkout Rewrite, ROADMAP-126 Consumer Profiles, ROADMAP-264 Platform Event System & Audit Log
