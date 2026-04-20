---
name: requirements-synthesizer
description: "Find common patterns across competing stakeholder requirements, resolve conflicts, propose unified solutions."
---

# Requirements Synthesizer

## Purpose
When multiple clubs or stakeholders want different things, someone has to analyze the requests, find common patterns, identify conflicts, and propose a solution that maximizes coverage. The synthesis itself requires judgment (the user's job), but the analysis, comparison, and per-stakeholder communication can be automated. This is a high-cognitive-load Director task.

## Dependencies

**Tools/APIs:**
- Jira API — read feature requests, stories, epics with requirements
- Confluence API — read specs, requirement documents
- Slack API — read request threads, stakeholder discussions
- Gmail API — read stakeholder email requests

**Other Skills:**
- `company-context` — stakeholder profiles, club context, business terminology
- `voice-analyzer` — per-stakeholder communication tone
- `user-story-writer` — write the unified story/epic description
- `technical-to-business-translator` — explain technical constraints to stakeholders
- `decision-logger` — capture the synthesis decision

**Reference Files:**
- `references/synthesis-patterns.md` — analysis framework, conflict resolution heuristics, coverage scoring

## Inputs
- **Requests** (required): 2+ feature requests or requirements. Sources: Jira tickets, Confluence pages, Slack threads, email threads, or inline descriptions
- **Constraints** (optional): technical limitations, timeline, budget, team capacity
- **Priority frame** (optional): what to optimize for (broadest coverage, fastest delivery, strategic alignment)

## Outputs
- Analysis matrix: each request mapped to underlying needs
- Overlap/conflict map: where requests align and diverge
- Proposed unified solution with coverage assessment
- Per-stakeholder communication: "here's what we're building and how it addresses your need"

---

## Workflow

### Step 1: Gather Requirements

For each input request, extract the structured need:

| Field | What to Extract |
|-------|----------------|
| **Source** | Who requested it? (club name, stakeholder, team) |
| **Stated Want** | What they asked for (their words) |
| **Underlying Need** | What problem they're trying to solve (may differ from stated want) |
| **Use Case** | Specific scenario or workflow they described |
| **Priority** | How important is this to them? (explicit or inferred) |
| **Constraints** | Any specific requirements or limitations they mentioned |
| **Context** | Business context — why now? What changed? |

If pulling from Jira/Confluence/Slack/email, read the source material and extract. **Gmail search:** Use `from:{stakeholder_email}` and topic keywords. Search the last 90 days. Extract the specific request, stated priority, and any constraints mentioned. If inline, ask the user for each request.

Present the extracted needs for user validation: "Here's what I understand each stakeholder wants. Correct?"

### Step 2: Map to Underlying Needs

Abstract from stated wants to underlying needs. This is the key synthesis step.

**Technique: Need Decomposition**
For each request, ask:
- What problem does this solve?
- What outcome does the stakeholder actually want?
- If they got this exact feature, would their problem be solved?
- Could a different approach achieve the same outcome?

**Build the Need Matrix:**

```
| Need | Club A | Club B | Club C | Notes |
|------|--------|--------|--------|-------|
| {underlying need 1} | Stated as X | Stated as Y | Not mentioned | Same need, different expression |
| {underlying need 2} | Required | Nice-to-have | Required | Priority differs |
| {underlying need 3} | Not mentioned | Required | Conflicts with Club A | Conflict |
```

### Step 3: Identify Patterns

From the need matrix:

**Overlap (build confidence):**
- Needs mentioned by 2+ stakeholders → high-confidence requirements
- Same underlying need expressed differently → validate with user

**Conflicts (require resolution):**
- Needs that are mutually exclusive → flag for decision
- Needs that compete for the same resources → prioritize

**Unique needs (require judgment):**
- Needs from only one stakeholder → assess: is this important enough to include?

Present the pattern analysis: "Here's where they agree, where they conflict, and what's unique to each."

### Step 4: Propose Unified Solution

Based on the analysis, propose a solution that maximizes coverage:

```
## Proposed Solution: {title}

### Core (addresses all stakeholders)
- {Requirement that satisfies the shared underlying needs}

### Extended (addresses most stakeholders)
- {Requirement that covers 2+ stakeholders}
- {Configuration or option that handles divergent preferences}

### Stakeholder-Specific
- {Requirement unique to one stakeholder, if included}

### Excluded (with rationale)
- {Request not included}: {why — conflict, low impact, technical infeasibility}

### Coverage Score
- Club A: {X}% of needs addressed
- Club B: {Y}% of needs addressed
- Club C: {Z}% of needs addressed
```

Offer alternatives if the primary proposal has significant trade-offs:
- "Option A maximizes coverage but takes longer"
- "Option B ships faster but leaves Club C's unique need unaddressed"

### Step 5: Review and Refine

Present the proposal. Actions:
- **"Approve"** — proceed to communication drafting
- **"Adjust coverage"** — include/exclude specific needs
- **"Explore alternative"** — generate a different solution approach
- **"Add constraint"** — introduce a new constraint (timeline, technical, political)
- **"Split"** — break into phases (MVP + future)

### Step 6: Generate Per-Stakeholder Communication

For each stakeholder, draft a message explaining:

1. **What we're building** — framed in their terms, referencing their original request
2. **How it addresses their need** — specific mapping to what they asked for
3. **What's different from their request** — transparent about changes, with rationale
4. **What's not included** (if anything) — with honest explanation
5. **Timeline** — when they can expect it

Adapt tone per stakeholder using `voice-analyzer` profiles.

Present each draft for review. Actions: **Send** / **Edit** / **Skip**.

### Step 7: Capture Decision

Prompt to log the synthesis decision via `decision-logger`:
- What was decided: the unified approach
- Alternatives considered: the different stakeholder requests
- Rationale: why this synthesis
- Who was involved: the stakeholders whose input was synthesized

### Step 8: Create Artifacts (Optional)

After Step 7, ask: "Want me to create any artifacts from this?" Then offer:
- **Jira epic/stories** — using `user-story-writer` for the unified solution
- **Confluence spec** — requirements document with the synthesis analysis
- **Stakeholder messages** — sent via Slack or email

---

## Context Rules
- In Step 1, load one request source at a time. Extract structured data, then drop raw source.
- Keep the need matrix in context through Steps 2-5 (compact structured data).
- In Step 6, load one stakeholder profile at a time for communication drafting.
- After Step 6, drop individual communication context.
- Never auto-send stakeholder communications. Always review.

## Edge Cases
- **Only 2 requests:** Still useful — the analysis framework helps even with two inputs.
- **Requests are nearly identical:** Note the alignment. "All stakeholders want essentially the same thing. Here's the unified version." Simpler workflow.
- **Requests are fundamentally incompatible:** Surface the conflict clearly. "These requests can't both be satisfied. Here are the trade-offs." Help the user make the decision, don't pretend synthesis is possible.
- **Stakeholder changed their mind:** If newer information contradicts an earlier request, flag: "Club A's latest message seems different from their original request. Use the newer version?"
- **Technical infeasibility:** If a need is technically impossible or impractical, explain why using `technical-to-business-translator`. Propose alternatives.
- **10+ stakeholders:** Summarize by group rather than individual. Look for clusters of aligned needs.
- **Missing context on a request:** Ask: "I don't have enough context on Club B's request. Can you provide more detail, or should I proceed with what I have?"
- **Single stakeholder with contradictory requirements:** Still useful — the need decomposition helps the user see the internal conflict. Frame as: "These requirements are in tension: {A} vs. {B}. Which is higher priority, or is there a design that satisfies both?"

## When NOT to Use
- **Single stakeholder's requirement** — use `user-story-writer` or `discover-design-deliver`
- **Bug triage across teams** — use `bug-consolidator`
- **General backlog prioritization** — use `backlog-groomer`
- **Technical architecture decisions** — use `domain-modeler` or `api-composer`
