---
name: team-signal-scanner
description: "Scan Slack and Gmail for internal team signals — engineering pain points, recurring complaints, cross-team friction, and workaround culture. Trigger on phrases like 'what's the team frustrated about,' 'internal pain points,' 'what are engineers complaining about,' 'support team patterns,' 'team signal scan,' 'engineering friction,' 'workaround culture,' 'team health check,' or any request to surface internal frustration and friction."
---

# Team Signal Scanner

## Purpose
Systematically scans internal Slack and Gmail for informal team pain — workarounds, recurring frustration, cross-team friction, knowledge gaps — and surfaces them as structured evidence before they calcify into "that's just how it works."

## Dependencies

**Tools/APIs:**
- Slack — engineering channels, support channels, general/product channels
- Gmail — internal threads about recurring problems, cross-team coordination
- Circleback — SearchTranscripts (keyword search across meeting recordings), SearchMeetings + ReadMeetings (meeting notes, action items, decisions)

**Reference Files:**
- `references/team-signal-patterns.md` — learned patterns (action vs. dismissal)

## Inputs
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels to target (default: all internal team channels)
- **Focus area** (optional): narrow to a specific topic, component, or team interaction
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` — returns structured signal list, no interactive review. Default: `false`

## Outputs
Ranked signal list with structured detail, cluster groupings, and summary statistics.

**Standalone mode** (default): Interactive review per signal (Acknowledge / Investigate / Dismiss / Link to Existing).
**Orchestrated mode** (`called_by_problem_discoverer = true`): Returns structured signal list. No interactive review.

## Signal Types

### Workaround Language (severity: complaint)
Patterns: "hack," "workaround," "for now," "temporary fix," "tech debt," "band-aid," "quick fix," "duct tape," "monkey patch," "hardcoded"

### Frustration Patterns (severity: complaint, recurring)
Patterns: "again," "keeps happening," "still broken," "another one," "not this again," "every time," "why does this keep"

### Knowledge Gaps (severity: mention)
Patterns: "does anyone know," "how does X work," "where is this documented," "who owns," "is there a doc for," "I can't find"

### Cross-Team Friction (severity: complaint)
Patterns: "waiting on," "blocked by," "nobody owns this," "whose responsibility," "fell through the cracks," "no response from," "who do I talk to about"

### Repeated Manual Work (severity: mention)
Patterns: "I have to do this every time," "manual process," "copy-paste," "run this script manually," "check every," "manually verify"

### Incident Echoes (severity: escalation)
Patterns: references to past incidents, "happened again," "same thing as last time," "thought we fixed this," "root cause was never"

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source: { type: Gmail|Slack, channel/thread: string, date: date }
- reporter: string (which team member or team)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Set time window (default: 30 days)
- Identify channels and sources to scan:
  - Slack: engineering channels, support channels, product channels, general channels
  - Gmail: internal threads, cross-team coordination threads
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/team-signal-patterns.md` for learned patterns

### Step 2: Scan
Pull messages from relevant Slack channels, Gmail threads, and Circleback meeting transcripts within time window. Process in batches of 20 messages. Extract signal-relevant content; drop raw message content after classification.
- Slack: workaround language, frustration patterns, knowledge gaps, friction indicators
- Gmail: internal threads with recurring problem discussion, coordination breakdowns
- Circleback: `SearchTranscripts` for signal keywords (e.g., "workaround," "hack," "keeps happening," "blocked by," "manual process"). Filter to internal meetings only (exclude customer/vendor domains via `FindDomains`). Also `SearchMeetings` + `ReadMeetings` for recent meetings — scan notes and action items for pain signals that surface verbally but never get written down in Slack/email. Batch limit: 20 transcript hits max.

### Step 3: Classify
Apply signal type detection to each message/thread:
- Match against signal type patterns (Workaround Language, Frustration Patterns, Knowledge Gaps, Cross-Team Friction, Repeated Manual Work, Incident Echoes)
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (explicit workaround/frustration) / medium (likely pain point) / low (possible signal, ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying issue (same topic from multiple team members, same complaint across threads)
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals that share the same root issue

### Step 5: Rank
- Sort by severity (escalation > complaint > mention > informational)
- Within severity: sort by frequency (recurring > first occurrence)
- Within frequency: sort by recency (newer first)

### Step 6: Present or Return
**Standalone mode**: Present each signal for interactive review: **Acknowledge** / **Investigate** / **Dismiss** (log reasoning) / **Link to Existing**

Commands: **Next** / **Back** / **Jump to #** / **Show overview** / **Remaining** / **Done**

**Orchestrated mode**: Return full structured signal list to problem-discoverer. Skip interactive review.

## Context Rules
- Process messages in batches of 20. Drop raw message content after extracting signal summaries.
- In orchestrated mode, skip interactive review and return structured signal list.
- Update learning file (`references/team-signal-patterns.md`) at session end only.
- Flag team friction patterns without attributing blame to individuals.
- Only scan channels the user has access to.

## Edge Cases
- **Venting vs. real pain**: Not every frustrated message indicates a systemic problem. Weight recurring patterns heavily; single vents get low confidence.
- **High volume (100+ signals)**: Present top 20 by severity/frequency. Offer to continue with remaining.
- **Private channels**: Only scan channels the user has access to. Note coverage gaps.
- **Sarcasm and humor**: Engineering channels often use ironic language. When in doubt, classify as low confidence.
- **New team members**: Frequent "how does X work" questions from recently onboarded engineers may indicate normal ramp-up, not knowledge gaps. Check reporter tenure if detectable.

## When NOT to Use
- **Responding to a specific team thread** — use `message-drafting`
- **Customer/external signals** — use `customer-signal-scanner`
- **Vendor communication** — use `vendor-signal-scanner`
- **Full backlog grooming** — use `backlog-groomer`
- **Incident response** — handle directly, don't scan for it
