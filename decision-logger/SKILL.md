---
name: decision-logger
description: "Use when capturing decisions with context, searching for past decisions, or when other skills surface decision points. Trigger on phrases like 'log this decision,' 'why did we decide,' 'capture this decision,' 'decision record,' 'what was decided about,' 'document this choice,' 'decision log,' or when brainstorming/discover-design-deliver reaches a decision point."
---

# Decision Logger

## Purpose
Decisions evaporate across Slack, Jira, Confluence, and meetings. This skill captures decisions with full context (why, alternatives, who, constraints) and stores them in Confluence in a consistent, searchable format. It also retrieves past decisions when someone asks "why did we do X?"

## Dependencies

**Tools/APIs:**
- Confluence API — create/update decision log pages, search existing decisions
- Slack API — read threads that contain decision context
- Jira API — link decisions to related tickets/epics
- Circleback API — SearchMeetings, ReadMeetings (pull decisions from meeting notes)

**Other Skills:**
- `company-context` — Confluence space keys, project names, team context
- `discover-design-deliver` — feeds decisions into the log when discovery/design phases produce them
- `voice-analyzer` — when drafting decision summaries for external audiences

**Reference Files:**
- `references/decision-template.md` — structured decision record format
- `references/decision-log-config.md` — Confluence space/page config, tag taxonomy, search patterns

## Inputs
- **Decision statement** (required for logging): what was decided
- **Context source** (optional): Slack thread URL, meeting name, Jira ticket, or inline conversation
- **Search query** (for retrieval): topic, keyword, date range, or decision area

## Outputs
- **Logging mode:** Structured decision record written to Confluence, linked to relevant Jira tickets
- **Search mode:** Retrieved decision(s) with context, date, rationale, and Confluence link
- **Suggestion mode:** Prompt to log a decision when detected in conversation flow

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Log** | "log this decision," "capture this," "decision record" | Structured capture workflow |
| **Search** | "why did we decide," "what was decided about," "find the decision on" | Search Confluence decision log, return matches with context |
| **Suggest** | Auto-detected decision point in conversation or from another skill | Prompt user: "This looks like a decision worth capturing. Want me to log it?" |

---

## Workflow

### Log Mode

#### Step 1: Gather Decision Context

If the user provides a source (Slack thread, meeting, Jira ticket), pull context from it first. Otherwise, ask structured questions.

**From Slack thread:**
1. Read the thread via Slack API
2. Extract: the decision statement, alternatives discussed, participants, constraints mentioned
3. Present extracted context for user confirmation

**From Circleback meeting:**
1. Search recent meetings by topic/attendees
2. Read meeting notes — extract decisions, action items, participants
3. Present extracted context for user confirmation

**From Jira ticket:**
1. Read the ticket and its comments
2. Extract decision-relevant discussion
3. Present extracted context for user confirmation

**From conversation (inline):**
Ask the user directly. Questions asked one at a time — skip any the user marks as not applicable:

1. **What was decided?** (required — the decision statement)
2. **What alternatives were considered?** (helps future "why not X?" questions)
3. **Why this option?** (the rationale — most important for future retrieval)
4. **Who was involved?** (decision makers and key influencers)
5. **What constraints applied?** (timeline, budget, technical, political)
6. **What are the risks or trade-offs?** (what we're accepting)
7. **Is this reversible?** (and at what cost)
8. **Related work?** (Jira tickets, epics, roadmap items)

#### Step 2: Structure the Record

Format the decision using `references/decision-template.md`. Key fields:

| Field | Source |
|-------|--------|
| Decision ID | Auto-generated: `DEC-{YYYY}-{NNN}` |
| Date | Today or user-specified |
| Status | Active / Superseded / Reversed |
| Decision | Clear statement of what was decided |
| Context | Why this decision was needed |
| Alternatives | What else was considered and why not |
| Rationale | Why this option won |
| Participants | Who decided, who was consulted |
| Constraints | What limited the options |
| Risks/Trade-offs | What we're accepting |
| Reversibility | Easy / Moderate / Hard + notes |
| Related Work | Jira links, epic links, roadmap items |
| Tags | Domain area, team, decision type |

#### Step 3: Review and Confirm

Present the structured record to the user. Actions:
- **"Approve"** — write to Confluence
- **"Edit"** — modify any field
- **"Add context"** — append additional information
- **"Cancel"** — discard

#### Step 4: Write to Confluence

1. Write the decision record to the configured Confluence decision log page (append as a new entry or create a child page — per config)
2. If related Jira tickets exist, add a comment on each linking to the decision record
3. Confirm with user: link to the Confluence page

#### Step 5: Cross-Link (Optional)

If the decision relates to active work:
- Offer to comment on related Jira tickets with a link to the decision
- Offer to post a summary to a relevant Slack channel

---

### Search Mode

#### Step 1: Parse Query

Interpret what the user is looking for:
- Topic/keyword search: "why did we decide to use Redis?"
- Date-scoped: "decisions made last quarter about auth"
- Person-scoped: "what did we decide in the meeting with Sarah?"
- Area-scoped: "booking pipeline decisions"

#### Step 2: Search Confluence

1. Search the decision log in Confluence using CQL (keywords, labels, date range)
2. If no Confluence results, broaden: search Jira comments and Slack for decision-like language
3. Return top 5 matches ranked by relevance

#### Step 3: Present Results

For each match:
```
[DEC-2026-042] {Decision statement}
Date: {date} | Participants: {names}
Rationale: {1-2 sentence summary}
Status: {Active/Superseded/Reversed}
Link: {Confluence URL}
```

If no matches found, say so clearly and suggest: "Want me to search Slack threads or meeting notes for this topic?"

---

### Suggest Mode

When another skill or conversation surfaces a decision point:

1. Detect decision language: "let's go with," "we decided," "the call is," "agreed to," "final answer," "we're going to"
2. Prompt: "This looks like a decision worth capturing: '{extracted statement}'. Want me to log it?"
3. If yes, enter Log Mode with pre-filled context from the conversation
4. If no, move on — don't ask again for the same decision

---

## First-Run Setup

If `references/decision-log-config.md` doesn't have Confluence config:

1. Ask: "Which Confluence space should I use for decision logs?"
2. Ask: "Should decisions be entries on a single page, or individual child pages under a parent?"
3. Ask: "Any tag categories you want? (e.g., by team, by domain area, by project)"
4. Write config to `references/decision-log-config.md`
5. Create the Confluence parent page if it doesn't exist

---

## Context Rules
- In Log Mode, gather all context in Step 1 before structuring. Drop raw source data after extraction.
- In Search Mode, return summaries first. Load full decision record only if user asks for detail.
- Suggest Mode detection runs passively — do not load decision log into context for detection.
- Confluence writes require user confirmation. Never auto-publish.
- When called from another skill (e.g., brainstorming, discover-design-deliver), accept pre-filled context and skip redundant questions.

## Edge Cases
- **Confluence API unavailable:** Draft the decision record locally. Offer to write to Confluence later.
- **Decision contradicts a previous one:** Flag: "This appears to conflict with DEC-2026-028. Want to supersede that decision?"
- **User can't articulate the rationale:** Log what's available. Add a "TODO: rationale needed" note. Better to capture incomplete than not at all.
- **Multiple decisions in one conversation:** Queue them. Process one at a time.
- **Decision already logged:** If search finds a near-duplicate, surface it: "This looks similar to DEC-2026-035. Update that one or create a new record?"

## When NOT to Use
- **Technical design decisions in code** — those belong in ADRs in the repo, not the decision log
- **Personal task decisions** — "should I do X or Y today" is not a decision record
- **Brainstorming without a conclusion** — use `brainstorming` skill; log when a decision is reached
- **Meeting notes capture** — use `ticket-proposer` for action items; decision-logger is for the decisions themselves
