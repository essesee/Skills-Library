---
name: dependency-tracker
description: "Use when tracking cross-team dependencies, surfacing stale blockers, or generating nudge communications. Trigger on phrases like 'what's blocked,' 'dependency status,' 'check on backend,' 'stale blockers,' 'cross-team blockers,' 'what are we waiting on,' 'dependency check,' 'nudge the backend team,' 'blocked by another team,' or before standup to surface stale dependencies."
---

# Dependency Tracker

## Purpose
Backend team dependencies have zero tracking system. This skill monitors cross-team blocking relationships in Jira, cross-references with Slack activity, surfaces stale dependencies, and generates context-appropriate follow-up messages. Even basic visibility is a step change from the current state.

## Dependencies

**Tools/APIs:**
- Jira API — JQL search for blocking/blocked relationships, issue links, status transitions
- Slack API — search for mentions of blocking tickets/teams, recent activity signals
- Google Calendar API — find upcoming syncs with blocking teams

**Other Skills:**
- `company-context` — team names, project keys, people directory
- `voice-analyzer` — tone-appropriate nudge messages
- `daily-planner` — can surface stale dependencies in morning briefing

**Reference Files:**
- `references/dependency-config.md` — team mappings, Jira project keys, staleness thresholds, escalation rules
- `references/nudge-templates.md` — message templates by escalation level (casual, firm, escalation)

## Inputs
- **Scope** (optional): specific epic, project, or "all" (default: all active team epics)
- **Focus team** (optional): "backend," "infrastructure," or specific team name
- **Action** (optional): "status" (default), "nudge {team/person}," "escalate {ticket}"

## Outputs
- Dependency status report: all cross-team dependencies with health indicators
- Stale blocker alerts with recommended actions
- Draft nudge or escalation messages ready to send

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Status Check** | "what's blocked," "dependency status," "cross-team blockers" | Full scan and report |
| **Nudge** | "nudge the backend team," "follow up on {ticket}" | Generate and offer to send follow-up message |
| **Escalate** | "escalate {ticket}," "this has been blocked too long" | Generate escalation message for engineering leadership |
| **Morning Brief** | Called by `daily-planner` or "check dependencies before standup" | Abbreviated report focused on items needing action today |

---

## Workflow

### Step 1: Configure and Load

1. Load `references/dependency-config.md` — team mappings, thresholds, escalation rules
2. If first run, prompt for: your team's Jira project keys, key dependency teams, staleness thresholds (defaults: 3 days = stale, 7 days = escalation candidate, 14 days = critical)
3. Resolve scope — specific epic(s) or all active team work

### Step 2: Scan Dependencies (Parallel)

#### 2A: Jira — Blocking Relationships

1. Search for issues in your team's projects that have blocking links:
   ```
   project IN ({team_projects}) AND issueFunction in linkedIssuesOf("project NOT IN ({team_projects})", "is blocked by")
   ```
   Fallback JQL if `issueFunction` is unavailable:
   ```
   project IN ({team_projects}) AND status IN ("Blocked", "Waiting", "On Hold")
   ```
2. For each blocked issue, follow the link to the blocking issue. Extract:
   - Your ticket: key, summary, status, assignee, sprint, days blocked
   - Blocking ticket: key, summary, status, assignee, project/team, last updated
   - Link type: blocks, is blocked by, depends on
3. Also search for your team's issues that block others (outbound dependencies)

#### 2B: Slack — Activity Signals

For each blocking team/ticket identified in 2A:
1. Search Slack for the blocking ticket key (last 14 days)
2. Search for the blocking team's channels for mentions of your team's tickets
3. Extract: last mention date, who mentioned it, context (working on it? stuck? no mention = silent)

#### 2C: Calendar — Upcoming Syncs

1. Search for meetings with blocking team members in the next 7 days
2. Flag: if a sync exists, note it as a natural follow-up opportunity

### Step 3: Assess Health

For each dependency, compute a health status:

| Status | Criteria |
|--------|----------|
| **Healthy** | Blocking ticket updated within threshold, Slack activity shows progress |
| **Stale** | No update on blocking ticket in {stale_threshold} days, no Slack mentions |
| **At Risk** | No update in {escalation_threshold} days, or blocking ticket deprioritized/moved out of sprint |
| **Critical** | No update in {critical_threshold} days, or your blocked ticket has an approaching deadline |

Enrich with context:
- Days since last activity on blocking ticket
- Days your ticket has been blocked
- Whether blocking team has mentioned it in Slack
- Whether a sync meeting exists
- Sprint/deadline pressure on your blocked ticket

### Step 4: Present Report

**Full Status Check:**
```
## Dependency Status — {date}

### Critical ({count})
{Each with: your ticket → blocking ticket, days blocked, last activity, recommended action}

### At Risk ({count})
{Same format}

### Stale ({count})
{Same format}

### Healthy ({count})
{Brief list — no action needed}

### Outbound (You're Blocking Others) ({count})
{Items where your team is the blocker — important for reciprocity}
```

**Morning Brief (abbreviated):**
```
## Dependencies Needing Action Today
- {ticket}: blocked {N} days by {team}'s {ticket} — {recommended action}
- {ticket}: approaching deadline, blocked by {ticket} — {recommended action}
{count} dependencies healthy, {count} total tracked.
```

### Step 5: Interactive Actions

For each dependency, offer contextual actions:

| Action | When |
|--------|------|
| **"Nudge"** | Stale or At Risk — draft a casual Slack message to the blocking team/person |
| **"Escalate"** | Critical or At Risk after nudge — draft escalation message to engineering leadership |
| **"Check in meeting"** | Sync exists — add to meeting agenda or prep talking point |
| **"Update Jira"** | Your blocked ticket needs status/comment update |
| **"Unblock ourselves"** | Offer to explore: can we work around this? Partial progress? |
| **"Mark resolved"** | Dependency cleared — update status |

### Step 6: Generate Messages

When the user selects "Nudge" or "Escalate," draft a message using `references/nudge-templates.md`:

**Casual Nudge (Slack DM or channel):**
- Friendly, low-pressure
- Include: what's blocked, how long, what you need
- Tone: "Hey, checking in on {ticket} — we're waiting on this for {reason}. Any update on timing?"

**Firm Follow-up (Slack, after previous nudge):**
- Direct, specific ask
- Include: what's blocked, impact on your timeline, specific ask
- Tone: "Following up on {ticket} — this is blocking {your epic} which is due {date}. Can you give us an ETA or let us know if we need to find a workaround?"

**Escalation (Slack or email to leadership):**
- Professional, factual
- Include: what's blocked, how long, business impact, what you've tried
- Tone: "Flagging a cross-team dependency that's become critical: {summary}. {Your ticket} has been blocked for {N} days. We've followed up with {team} on {dates}. Impact: {business impact}. Requesting help prioritizing or finding an alternative path."

Present the draft for review. Actions: **Send** / **Edit** / **Cancel**.

### Step 7: Learn

Update `references/dependency-config.md`:
- Track which teams are frequently blocking (pattern detection)
- Track nudge effectiveness (did things move after nudge?)
- Adjust staleness thresholds based on team response patterns
- Log: date, dependencies tracked, actions taken, outcomes

---

## Context Rules
- Run Step 2 data pulls in parallel.
- After Step 2, drop raw Jira/Slack data. Keep only dependency summaries.
- During Step 5/6, load only the current dependency's context for message drafting.
- Morning Brief mode: skip Steps 5-7, present report only.
- Batch limits: Jira issues 30, Slack searches 10 per blocking ticket (cap at 5 tickets per run).
- Never auto-send messages. Always present draft for user approval.

## Edge Cases
- **No blocking relationships found in Jira:** Report clean. Suggest: "No formal blockers in Jira. Are there informal dependencies I should track? You can tell me and I'll monitor them."
- **Jira link types vary:** Handle: "blocks/is blocked by," "depends on," "relates to" (weaker signal). Config allows specifying which link types to track.
- **Blocking team has no Jira project:** Track by person/ticket only. Note limited visibility.
- **User is the blocker:** Surface outbound dependencies prominently. "Heads up — {team} is waiting on your {ticket}."
- **Same ticket blocking multiple items:** Consolidate into one dependency with multiple impacts. Escalation priority = highest impact item.
- **Nudge already sent recently:** Check delivery log. If <3 days since last nudge, warn: "You nudged {person} about this {N} days ago. Escalate instead?"

## When NOT to Use
- **Single ticket status check** — just look at the ticket in Jira
- **Full backlog grooming** — use `backlog-groomer`
- **Internal team blockers** — this is for cross-team dependencies
- **Incident response** — use `incident-commander`
- **General project status** — use `stakeholder-update` or `context-briefer`
