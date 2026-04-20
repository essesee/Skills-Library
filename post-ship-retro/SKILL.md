---
name: post-ship-retro
description: "Evaluate shipped features against their original hypothesis. Use when a feature ships, an initiative completes, or you want to assess whether a bet paid off. Triggers: 'retro on X', 'how did X perform', 'did X work', 'post-ship review', 'evaluate the launch', 'did the hypothesis hold'."
---

# Post-Ship Retro

## Purpose

Close the product learning loop. When a feature ships, evaluate whether the original hypothesis held, capture what surprised you, and write learnings to memory so they inform future product decisions.

This is the counterpart to the hypothesis gates in `discover-design-deliver` (Phase 1) and `sql-data-analysis`. Those frame the bet going in. This evaluates the bet after.

## Dependencies

**Tools/APIs:**
- PostHog — adoption metrics, funnel changes, feature usage since launch
- Jira — original epic/story context, acceptance criteria, linked PRD
- Confluence — PRD success metrics, problem statement, original hypothesis

**Other Skills:**
- `sql-data-analysis` — for deeper data dives if initial metrics warrant it
- `decision-logger` — to log retro findings as a decision record
- `signal-scanner` — optional: check for customer/team signals about the shipped feature

## Inputs
- **What shipped** (required): feature name, Jira epic/story key, or description
- **When it shipped** (gather if missing): launch date, needed to scope PostHog queries
- **Original hypothesis** (gather from artifacts): what bet was being made, what success metrics were defined

## Outputs
- Structured retro summary (hypothesis result, surprises, process learnings)
- Memory file capturing learnings tagged by initiative
- Optional: updated PRD/Idea with outcomes section

---

## Workflow

### Phase 1: Identify the Ship

Accept the feature from the user. Find it in Jira (epic, story, or initiative). Pull:
- Epic/story description and acceptance criteria
- Linked PRD or Problem Statement from Confluence
- Original success metrics (from PRD if available)
- Ship date (from Jira transition to Done/Released)

If no formal PRD exists, ask: "What was the bet? What outcome were you hoping for?"

### Phase 2: Pull the Data

Query PostHog for the shipped feature's performance since launch:
- Feature adoption (users engaging with the new capability)
- Funnel impact (did the metric the feature targeted actually move?)
- Error rates or friction signals
- Any notable behavioral patterns

Compare against the original success metrics from Phase 1. If no formal metrics exist, compare against the stated hypothesis.

Present a concise data summary. Surface the headline, not a dashboard.

### Phase 3: Reflect (Interactive)

Walk through these questions one at a time:

1. **Did the hypothesis hold?** Present the data comparison and ask for the user's read. Data can be ambiguous; the user's interpretation matters.

2. **What surprised you?** Could be about user behavior, adoption speed, unexpected use cases, or internal process. This is where the non-obvious learnings live.

3. **What would you do differently?** Not "what went wrong" (that's a blame frame). What would you change about the approach, scope, or process if you did it again?

4. **What should compound?** Any pattern, technique, or approach from this initiative that should inform future work? This is what gets saved to memory.

### Phase 4: Capture

Write a memory file to the active project's memory directory with:
- Initiative name and ship date
- Hypothesis: stated vs. actual outcome
- Key surprises
- Learnings to carry forward
- Type: `project`

Offer to:
- Log as a decision record via `decision-logger` (if the retro surfaced a meaningful decision or course correction)
- Update the PRD with an Outcomes section (if a PRD exists in Confluence)
- Run `signal-scanner` to check for customer/team reactions (if the ship was recent enough)

---

## Context Rules
- Load Jira and Confluence artifacts only in Phase 1. Summarize and drop raw content before Phase 2.
- PostHog queries in Phase 2 should be scoped to post-launch date only.
- During Phase 3, keep only the data summary and original hypothesis in context.
- Memory file write is the minimum viable output. Decision logging and PRD updates are optional.
- If called from `/morning` or `/eod` with a specific shipped item, skip Phase 1 and proceed directly to Phase 2.

## When NOT to Use
- Feature hasn't shipped yet. Use `discover-design-deliver` Phase Check mode.
- Bug fix or minor change. Not worth a formal retro.
- Need detailed analytics deep dive. Use `sql-data-analysis` directly.
- Team process retrospective (not feature focused). Use `bmad-retrospective`.
- Just need to check PostHog metrics. Use `platform-health` or `sql-data-analysis`.
