---
name: customer-signal-scanner
description: "Scan Gmail and Slack for customer and partner escalations, complaints, and pain signals. Trigger on phrases like 'scan for customer issues,' 'any customer escalations,' 'what are customers complaining about,' 'customer complaints,' 'customer signal scan,' 'external escalations,' 'customer feedback,' or any request to surface what external stakeholders are reporting."
---

# Customer Signal Scanner

## Purpose
Systematically scans Gmail and Slack for external-facing signals — complaints, escalations, repeated issues, severity indicators — and surfaces them as structured evidence for decision-making.

## Dependencies

**Tools/APIs:**
- Gmail — customer/partner emails, escalation threads, complaint patterns
- Slack — support channels, customer-facing channels, escalation threads
- Circleback — SearchTranscripts (keyword search in meeting recordings), SearchMeetings + ReadMeetings (meeting notes from customer/partner meetings), FindDomains (identify customer domain filters)

**Reference Files:**
- `references/customer-signal-patterns.md` — learned patterns (action vs. dismissal)

## Inputs
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels or Gmail labels to target (default: all customer-facing channels)
- **Focus area** (optional): narrow to a specific feature, product area, or customer organization
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` — returns structured signal list, no interactive review. Default: `false`

## Outputs
Ranked signal list with structured detail, cluster groupings, and summary statistics.

**Standalone mode** (default): Interactive review per signal (Acknowledge / Investigate / Dismiss / Link to Existing).
**Orchestrated mode** (`called_by_problem_discoverer = true`): Returns structured signal list. No interactive review.

## Signal Types

### Direct Escalations (severity: escalation)
Patterns: "this is broken," "we need this fixed," "escalating," "unacceptable," "when will this be resolved"

### Repeated Issues (severity: complaint, recurring)
Patterns: same reporter + same topic within window, multiple reporters + similar keywords

### Severity Indicators (severity: escalation or complaint)
Patterns: "urgent," "critical," "losing business," "switching to," "can't use," "deadline," "blocking," "SLA"

### Unresolved Threads (severity: complaint)
Patterns: customer question/complaint with no follow-up resolution, thread age > 3 days

### Customer-Specific Patterns (severity: varies)
Patterns: customer name + issue keywords, multiple issues from same customer. Platform-wide patterns score higher than single-customer issues.

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source: { type: Gmail|Slack, channel/thread: string, date: date }
- reporter: string (which customer/partner/organization)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Set time window (default: 30 days)
- Identify channels and sources to scan:
  - Slack: support channels, customer-facing channels, escalation channels
  - Gmail: customer correspondence, partner emails, customer organization communication
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/customer-signal-patterns.md` for learned patterns

### Step 2: Scan
Pull messages from relevant Slack channels, Gmail threads, and Circleback meeting transcripts within time window. Process in batches of 20 messages. Extract signal-relevant content; drop raw message content after classification.
- Gmail: threads with customer/partner/organization senders, escalation keywords
- Slack: complaint patterns, escalation language, unresolved questions
- Circleback: Use `FindDomains` to identify customer/partner domains, then `SearchMeetings` filtered by those domains within time window. `ReadMeetings` to extract complaints, escalations, and pain points discussed in meetings. Also `SearchTranscripts` for signal keywords ("broken," "escalating," "can't use," "switching to," "blocking") filtered to customer-domain meetings. Verbal escalations in meetings are often more candid than written communication. Batch limit: 20 transcript hits max.

### Step 3: Classify
Apply signal type detection to each message/thread:
- Match against signal type patterns (Direct Escalations, Repeated Issues, Severity Indicators, Unresolved Threads, Customer-Specific)
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (clear escalation language) / medium (likely complaint) / low (possible signal, ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying issue (same topic from same reporter, or same issue across reporters)
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals that share the same root issue but from different reporters/channels

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
- Update learning file (`references/customer-signal-patterns.md`) at session end only.
- Do not surface PII beyond reporter name/org needed for signal context.
- Only scan channels the user has access to.

## Edge Cases
- **No customer-facing channels configured**: Ask user to identify relevant Slack channels and Gmail labels.
- **High volume (100+ signals)**: Present top 20 by severity/frequency. Offer to continue with remaining.
- **Ambiguous sender**: If it's unclear whether a message is from a customer or internal team, classify as "informational" with low confidence.
- **Foreign language content**: Flag as "needs translation review" rather than attempting classification.
- **Old escalations**: Signals older than the time window but still unresolved — flag as "historical unresolved" if they appear in thread context.

## When NOT to Use
- **Responding to a specific customer email** — use `message-drafting`
- **Triaging a specific bug report** — use `atlassian:triage-issue`
- **Internal team signals** — use `team-signal-scanner`
- **Vendor communication** — use `vendor-signal-scanner`
