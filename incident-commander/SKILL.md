---
name: incident-commander
description: "Full incident lifecycle — first report through postmortem and action item tracking."
---

# Incident Commander

## Purpose
Every step of the incident lifecycle is currently manual — triage, stakeholder comms, timeline reconstruction, postmortem, and follow-up. This skill orchestrates the full lifecycle as a coordinated flow, using existing skills where applicable. When it's needed, urgency is high and manual overhead is the enemy.

## Dependencies

**Tools/APIs:**
- PostHog API — error rates, event data, affected user counts, recent errors
- Jira API — create incident ticket, create follow-up action items, link related issues
- Confluence API — write postmortem document
- Slack API — send stakeholder communications, search incident threads
- GitHub CLI — recent deploys, PR merge history, rollback capability

**Other Skills:**
- `impact-analyzer` — quantify affected users and business impact (invoked in Phase 2 for stakeholder comms and Phase 5 for postmortem impact section)
- `company-context` — team roster, escalation contacts, stakeholder profiles
- `voice-analyzer` — audience-appropriate communication tone
- `technical-to-business-translator` — explain technical issues to non-technical stakeholders
- `decision-logger` — capture key decisions made during incident

**Reference Files:**
- `references/incident-templates.md` — postmortem template, comms templates by audience, severity matrix
- `references/escalation-matrix.md` — who to notify at each severity level, contact channels, escalation paths

## Inputs
- **Trigger** (required): error report, alert, user report, or "write postmortem for {incident}"
- **Severity** (optional, auto-assessed if not provided): P0 (critical) / P1 (major) / P2 (minor)
- **Phase** (optional): start from a specific phase if incident is already in progress

## Outputs
- Incident ticket in Jira
- Audience-specific stakeholder communications
- Reconstructed timeline
- Structured postmortem in Confluence
- Follow-up action items as Jira tickets linked to postmortem

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Live Incident** | "production issue," "outage," "P0" | Full lifecycle: triage → comms → resolution tracking |
| **Postmortem** | "write postmortem," "incident timeline" | Reconstruct timeline and write postmortem for a resolved incident |
| **Follow-up Check** | "incident follow-ups," "postmortem action items" | Check status of action items from past incidents |

---

## Workflow

### Phase 1: Triage

#### Step 1: Assess the Situation

Pull data from available sources in parallel:

| Source | What to Pull |
|--------|-------------|
| **PostHog** | Recent error spikes, affected events, error rates vs. baseline |
| **Jira** | Related recent bugs, any existing incident tickets |
| **Slack** | Recent mentions of errors, user reports, deploy announcements |
| **GitHub** | Recent merges/deploys (last 24h) — potential trigger |

#### Step 2: Classify Severity

Auto-assess severity using `references/incident-templates.md` severity matrix:

| Severity | Criteria |
|----------|----------|
| **P0 — Critical** | Core functionality down, revenue impact, >25% users affected, no workaround |
| **P1 — Major** | Significant feature degraded, <25% users affected, workaround exists but painful |
| **P2 — Minor** | Edge case, low user impact, workaround is easy, non-blocking |

Present assessment to user for confirmation. User can override.

#### Step 3: Create Incident Ticket

Create a Jira incident ticket with:
- Summary: `[P{severity}] {brief description}`
- Description: initial assessment, affected systems, known impact
- Priority: maps from severity
- Labels: `incident`, `P{severity}`
- Link to related bugs/tickets found in Step 1

#### Step 4: Identify Owner and Affected Systems

Based on the error data and system area:
- Suggest an incident owner (from team roster)
- List affected systems/services
- Identify: is this our team's system or a dependency?

---

### Phase 2: Stakeholder Communications

Generate audience-specific messages using `references/incident-templates.md` and stakeholder profiles. Use `impact-analyzer` (inline mode) to quantify affected users for leadership comms and postmortem impact sections.

#### Clubs / External Stakeholders
- Lead with: impact on their users/agents
- Include: what's happening, estimated timeline to resolution, workaround if available
- Tone: empathetic, professional, action-oriented
- Channel: per stakeholder profile preference
- Use `technical-to-business-translator` for technical details

#### Leadership / Executives
- Lead with: business risk and scope
- Include: severity, affected user count, revenue impact estimate, who's working on it
- Tone: factual, concise, escalation-appropriate
- Channel: Slack DM or escalation channel

#### Engineering / Internal Teams
- Lead with: technical details
- Include: affected systems, error data, suspected root cause, who's investigating
- Tone: direct, technical, action-oriented
- Channel: incident Slack channel or team channel

Present each draft for review. Actions: **Send** / **Edit** / **Hold** / **Skip**.

---

### Phase 3: Resolution Tracking

During an active incident:

1. **Monitor:** Offer to periodically check PostHog error rates to detect resolution
2. **Update comms:** When status changes (investigating → identified → fixing → resolved), generate updated stakeholder messages
3. **Capture decisions:** When key decisions are made (rollback, hotfix, feature toggle), prompt to log via `decision-logger`
4. **Track timeline:** Note key events with timestamps as they happen

**Transition to Phase 4:** Move to timeline reconstruction when either:
- The user says "it's resolved" or "we fixed it"
- PostHog error rates return to baseline for 15+ minutes
- The user explicitly requests a postmortem

Do not auto-transition — confirm with the user: "Error rates look normal. Is the incident resolved? Ready to start the postmortem?"

---

### Phase 4: Timeline Reconstruction

After resolution (or for postmortem mode):

#### Step 1: Gather Timeline Data (Parallel)

| Source | What to Pull |
|--------|-------------|
| **Slack** | Incident channel/thread messages, deploy announcements, user reports |
| **Jira** | Incident ticket transitions, comment timestamps |
| **GitHub** | PR merges, deploys, rollbacks with timestamps |
| **PostHog** | Error rate timeline — when did it start, peak, and resolve |

#### Step 2: Reconstruct

Build a chronological timeline:
```
{timestamp} — {event} — {source}
```

Fill gaps by asking the user: "I have a gap between {time1} and {time2}. What happened there?"

#### Step 3: Identify Contributing Factors

From the timeline, identify:
- **Trigger:** what initiated the incident (deploy, config change, external dependency, traffic spike)
- **Detection delay:** how long between trigger and first awareness
- **Resolution delay:** what slowed the fix (if anything)
- **Escalation effectiveness:** was the right team engaged quickly?

---

### Phase 5: Postmortem

#### Step 1: Draft Postmortem

Using `references/incident-templates.md` postmortem template:

```
## Incident Postmortem: {title}
**Date:** {date}
**Severity:** P{n}
**Duration:** {start} to {end} ({total time})
**Impact:** {user count affected}, {business impact summary}

### Summary
{2-3 sentence overview}

### Timeline
{Chronological events from Phase 4}

### Root Cause
{Technical root cause}

### Contributing Factors
{What made this worse or harder to detect/fix}

### What Went Well
{Things that worked during the response}

### What Could Be Improved
{Process, tooling, or system improvements}

### Action Items
| # | Action | Owner | Priority | Jira Ticket |
|---|--------|-------|----------|-------------|
| 1 | {action} | {person} | {P0/P1/P2} | {to be created} |
```

#### Step 2: Review

Present the draft. Actions:
- **"Approve"** — write to Confluence, create Jira action items
- **"Edit"** — modify any section
- **"Add action item"** — append to the action item list
- **"Revise root cause"** — update root cause analysis
- **"Add context"** — include additional details

#### Step 3: Publish

1. Write postmortem to Confluence (configured space/parent page)
2. Create Jira tickets for each action item, linked to the incident ticket
3. Link the postmortem Confluence page to the incident Jira ticket
4. Optionally: post a summary to a Slack channel with link to full postmortem
5. Log incident to `references/escalation-matrix.md` Session Log: date, severity, duration, root cause category, resolution method, action item count

---

### Phase 6: Follow-up (Follow-up Check Mode)

1. Search Jira for action items from past postmortems (label: `postmortem-action`)
2. Check status of each: done, in progress, not started, stale
3. Surface stale action items (>14 days with no activity)
4. Generate a summary: "X of Y action items from the {date} incident are complete. {N} are stale."

---

## First-Run Setup

If `references/escalation-matrix.md` is empty:
1. Ask: "Who should be notified for P0/P1/P2 incidents?"
2. Ask: "Which Confluence space for postmortems?"
3. Ask: "Incident Slack channel name?"
4. Write to `references/escalation-matrix.md`

---

## Context Rules
- Phase 1 data pulls run in parallel. Drop raw data after extraction.
- Phase 2 communications: load one audience at a time. Drop before drafting the next.
- Phase 4 timeline: keep all timeline events in context (typically compact data).
- Phase 5 postmortem: keep timeline + contributing factors while drafting. Drop source data.
- Never auto-send communications. Always present for review first.
- Never auto-create Jira tickets without user approval.
- In Postmortem mode, skip Phases 1-3 and start at Phase 4.
- During Phase 3 (Resolution Tracking), keep only the incident summary, severity, and a rolling timeline of key events (cap at 30 entries). Drop Slack/PostHog raw data between monitoring checks.

## Edge Cases
- **Incident is in another team's system:** Help with comms and coordination but note: "Root cause investigation belongs to {team}. Want me to draft an escalation message?"
- **Multiple simultaneous incidents:** Handle one at a time. Offer to create separate tickets for each.
- **Incident already resolved when skill is invoked:** Skip to Phase 4/5 (timeline + postmortem).
- **No PostHog data available:** Proceed with Slack/Jira/GitHub data. Note the gap in the postmortem.
- **User doesn't know the root cause:** That's fine. Log "Root cause: under investigation" and create an action item for root cause analysis.
- **Recurring incident (happened before):** Search for previous postmortems. Link to prior incidents. Flag: "This is the {N}th occurrence. Previous action items may not have been completed."

## When NOT to Use
- **Bug triage (not an incident)** — use `bug-consolidator` or `atlassian:triage-issue`
- **Problem discovery (proactive)** — use `problem-discoverer`
- **General status update about an issue** — use `stakeholder-update`
- **Data analysis on error rates** — use `sql-data-analysis` or `impact-analyzer`
