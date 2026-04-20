---
name: signal-scanner
description: "Scan Slack, Gmail, and Circleback for signals by source type -- team (internal pain), customer (escalations), or vendor (integration issues). Run one source or all."
---

# Signal Scanner

## Purpose
Systematically scans communication channels for structured signals and surfaces them as evidence for decision-making. Supports three source types with source-specific signal patterns, or scans all sources at once.

## Source Types

| Source | What It Finds | Where It Looks |
|--------|--------------|----------------|
| **team** | Internal pain -- workarounds, frustration, knowledge gaps, cross-team friction, repeated manual work, incident echoes | Slack engineering/support/product channels, Gmail internal threads, Circleback internal meetings |
| **customer** | External-facing signals -- escalations, repeated issues, severity indicators, unresolved threads | Gmail customer/partner emails, Slack support/customer channels, Circleback customer meetings |
| **vendor** | Integration/service signals -- degradation, breaking changes, integration failures, contract/pricing changes, responsiveness | Gmail vendor correspondence, Slack vendor/integration channels, Circleback vendor meetings |

## Dependencies

**Tools/APIs:**
- Slack -- channel search and message retrieval
- Gmail -- thread search and message retrieval
- Circleback -- SearchTranscripts, SearchMeetings, ReadMeetings, FindDomains

**Reference Files:**
- `references/signal-patterns.md` -- learned patterns per source type (action vs. dismissal)

## Inputs
- **Source type** (required): `team`, `customer`, `vendor`, or `all`
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels or Gmail labels to target (default: auto-detect by source type)
- **Focus area** (optional): narrow to a specific topic, component, team, customer, or vendor
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` -- returns structured signal list, no interactive review. Default: `false`

## Outputs
Ranked signal list with structured detail, cluster groupings, and summary statistics.

**Standalone mode** (default): Interactive review per signal (Acknowledge / Investigate / Dismiss / Link to Existing).
**Orchestrated mode**: Returns structured signal list. No interactive review.

## Signal Patterns by Source

### Team Signals

| Type | Severity | Patterns |
|------|----------|----------|
| Workaround Language | complaint | "hack," "workaround," "for now," "temporary fix," "tech debt," "band-aid," "quick fix," "duct tape" |
| Frustration Patterns | complaint/recurring | "again," "keeps happening," "still broken," "another one," "not this again," "every time" |
| Knowledge Gaps | mention | "does anyone know," "how does X work," "where is this documented," "who owns" |
| Cross-Team Friction | complaint | "waiting on," "blocked by," "nobody owns this," "whose responsibility," "fell through the cracks" |
| Repeated Manual Work | mention | "I have to do this every time," "manual process," "copy-paste," "run this script manually" |
| Incident Echoes | escalation | references to past incidents, "happened again," "same thing as last time" |

### Customer Signals

| Type | Severity | Patterns |
|------|----------|----------|
| Direct Escalations | escalation | "this is broken," "we need this fixed," "escalating," "unacceptable," "when will this be resolved" |
| Repeated Issues | complaint/recurring | same reporter + same topic, multiple reporters + similar keywords |
| Severity Indicators | escalation/complaint | "urgent," "critical," "losing business," "switching to," "can't use," "deadline," "blocking," "SLA" |
| Unresolved Threads | complaint | customer question/complaint with no follow-up resolution, thread age > 3 days |
| Customer-Specific | varies | customer name + issue keywords, multiple issues from same customer |

### Vendor Signals

| Type | Severity | Patterns |
|------|----------|----------|
| Service Degradation | escalation | "downtime," "degraded performance," "known issue," "service disruption," "partial outage" |
| Breaking Changes | escalation | "deprecation," "end of life," "EOL," "migration required," "sunset," "breaking change" |
| Integration Failures | complaint | "timeout," "connection refused," "rate limit," "authentication failed," "sync failure" |
| Contract/Pricing | complaint/informational | "renewal," "price increase," "new terms," "SLA breach," "contract update," "pricing change" |
| Vendor Responsiveness | mention | unanswered support threads (> 3 business days), repeated follow-ups without resolution |

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source_type: team|customer|vendor
- source: { type: Gmail|Slack|Circleback, channel/thread: string, date: date }
- reporter: string (team member, customer/org, or vendor)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Determine source type(s) to scan. If `all`, run team + customer + vendor.
- Set time window (default: 30 days)
- Identify channels and sources per source type:
  - **team**: engineering channels, support channels, product/general channels, internal Gmail threads
  - **customer**: support channels, customer-facing channels, customer Gmail correspondence
  - **vendor**: vendor-specific channels, integration channels, vendor Gmail threads
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/signal-patterns.md` for learned patterns

### Step 2: Scan
Pull messages from relevant sources within time window. Process in batches of 20. Extract signal-relevant content; drop raw message content after classification.

**Circleback integration** (all source types):
- Use `FindDomains` to identify relevant domains (customer, vendor, or internal)
- `SearchMeetings` filtered by domain + time window. `ReadMeetings` for notes and action items.
- `SearchTranscripts` for signal keywords specific to the source type. Batch limit: 20 transcript hits max.
- Verbal escalations in meetings are often more candid than written communication.

### Step 3: Classify
Apply signal type detection using the patterns for the active source type(s).
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (explicit signal language) / medium (likely signal) / low (ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying issue
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals that share the same root issue

### Step 5: Rank
- Sort by severity (escalation > complaint > mention > informational)
- Within severity: sort by frequency (recurring > first occurrence)
- Within frequency: sort by recency (newer first)
- **Vendor-specific**: time-sensitive signals (deprecation deadlines, mandatory migrations) surface prominently regardless of other ranking

### Step 6: Present or Return
**Standalone mode**: Present each signal for interactive review: **Acknowledge** / **Investigate** / **Dismiss** (log reasoning) / **Link to Existing**

Commands: **Next** / **Back** / **Jump to #** / **Show overview** / **Remaining** / **Done**

When running `all` source types, group results by source type with section headers.

**Orchestrated mode**: Return full structured signal list to problem-discoverer. Skip interactive review.

## Context Rules
- Process messages in batches of 20. Drop raw message content after extracting signal summaries.
- In orchestrated mode, skip interactive review and return structured signal list.
- Update learning file (`references/signal-patterns.md`) at session end only.
- Flag team friction patterns without attributing blame to individuals.
- Do not surface PII beyond reporter name/org needed for signal context.
- Flag time-sensitive vendor signals prominently regardless of other ranking.
- Only scan channels the user has access to.

## Edge Cases
- **Venting vs. real pain** (team): Weight recurring patterns heavily; single vents get low confidence.
- **Ambiguous sender** (customer): If unclear whether internal or external, classify as informational/low confidence.
- **Marketing emails vs. real vendor communication** (vendor): Filter out vendor newsletters. Focus on support threads and official notifications.
- **Sarcasm and humor**: Engineering channels often use ironic language. When in doubt, low confidence.
- **High volume (100+ signals per source)**: Present top 20 by severity/frequency. Offer to continue.
- **No relevant channels configured**: Ask user to identify relevant Slack channels and Gmail labels.
- **New team members** (team): Frequent "how does X work" from recently onboarded engineers may be normal ramp-up.
- **Multi-vendor issues** (vendor): Classify the vendor component; note internal factors as context.

## When NOT to Use
- **Responding to a specific message** -- use `message-drafting`
- **Triaging a specific bug report** -- use `atlassian:triage-issue`
- **Full backlog grooming** -- use `backlog-groomer`
- **Incident response** -- handle directly, don't scan for it
- **Competitive intelligence** -- use `competitive-intel-scanner`
- **Vendor contract negotiation** -- handle directly; this skill is for signal detection
