---
name: stakeholder-update
description: "Use when you need to send tailored project status updates to one or more stakeholders via Slack or email. Requires at least one stakeholder profile. Trigger on phrases like 'send stakeholder update,' 'weekly update,' 'project status to stakeholders,' 'update my stakeholders,' 'send the weekly report,' 'stakeholder email,' 'status update for {person},' or any request to communicate project progress."
---

# Stakeholder Update

## Purpose
Stakeholders need regular project updates but each one cares about different things. This skill pulls project data from Jira and the roadmap, drafts a tailored update per stakeholder in the user's voice, walks through each draft for review, and sends via the stakeholder's preferred channel.

**Prerequisite:** At least one stakeholder profile must exist at `references/stakeholders/{name}.md`. If none exist, the skill guides setup interactively (see First-Run Setup).

## Dependencies

**Tools/APIs:**
- Jira API — JQL search for sprint data, epics, stories, blockers
- Gmail API — search stakeholder threads, create email drafts
- Slack API — search DMs/channels, send messages
- WebFetch — access Google Drive roadmap (shared with `daily-planner`)

**Other Skills:**
- `voice-analyzer` — extract user's voice profile for drafting (load in Phase 2 before first draft)
- `daily-planner` — shares roadmap cache and Jira gap analysis when available

**Reference Files:**
- `references/stakeholders/{name}.md` — one per stakeholder (profile, voice, delivery log)
- `references/profile-template.md` — template for creating new profiles
- `references/update-templates.md` — Brief/Standard/Detailed format examples
- `references/voice-guidelines.md` — shared voice blending rules and "Never Do This" universals

## Inputs
- Which stakeholders to update (from profiles, or "all")
- Optional: specific topics to highlight or omit
- Optional: custom time window (default: last 7 days)
- Optional: delivery channel override (Slack or email)

## Outputs
- Tailored update draft per stakeholder
- Interactive review of each draft before sending
- Sent updates via Slack and/or email
- Updated stakeholder profiles with delivery log

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Weekly Routine** | "send stakeholder update" / "weekly update" | Pull last 7 days, draft for all due stakeholders |
| **Ad-hoc Update** | "update {name} on {topic}" | Single stakeholder, specific topic, any time |
| **Profile Setup** | "add stakeholder" / first run with no profiles | Interactive profile creation (see First-Run Setup) |
| **Catch-Up** | Auto: last update >14 days ago | Cover 2-week window, adjust JQL ranges, note gap in update |

---

## Stakeholder Profiles

Each stakeholder gets their own file at `references/stakeholders/{name}.md`. See `references/profile-template.md` for full structure. Key sections:

- **Contact info** — email, Slack ID, preferred channel, cadence
- **What They Care About** — topics with HIGH/MEDIUM/LOW importance
- **Format Preferences** — length (Brief/Standard/Detailed), metrics (Yes/No), risks filter
- **Communication Voice Profile** — how THEY communicate (tone, sentence style, vocabulary, openers/closers)
- **"Never Do This"** — references `references/voice-guidelines.md` + stakeholder-specific additions
- **Delivery Log** — last 8 entries, older entries summarized into patterns

### First-Run Setup
1. Ask: "Who are your stakeholders? Names and how you reach them (Slack or email)."
2. Look up each via `slack_search_users` and/or Gmail
3. Analyze their communication style: search `from:{email}` in Gmail + Slack messages. Analyze 10-20 messages. Build Communication Voice Profile.
4. Ask: "What does each person care about? (Or I can figure it out from past messages.)"
5. If "figure it out" — search sent messages and DMs, analyze topics
6. Create profile file per stakeholder

---

## Workflow

### Phase 1: Pull Project Data (Parallel)

Run all data pulls simultaneously. Each source is independent.

#### 1A: Jira — Sprint & Epic Progress (batch: 5 JQL queries max)

| Query | JQL Pattern | Extract |
|-------|------------|---------|
| Completed this week | `status changed to Done DURING (startOfWeek(), now())` | key, summary, epic, points |
| In progress | `status = "In Progress" AND sprint in openSprints()` | key, summary, assignee, days in status |
| Blockers/risks | `status in ("Blocked", "Needs Info", "On Hold")` | key, summary, blocker reason, days blocked |
| Coming next week | `status in ("To Do", "Ready") AND sprint in openSprints()` | key, summary, estimate |
| Sprint health | Calculate points committed vs. completed vs. remaining | velocity trend if available |

Drop raw API responses after extracting key fields.

#### 1B: Google Drive Roadmap
1. Load roadmap cache from `references/roadmap-cache.md` (shared with `daily-planner`). If >24h old or missing, refresh via WebFetch.
2. For each active roadmap item: current status, Jira alignment (epic exists? stories in progress? on track?), flag items at risk.

#### 1C: Recent Context — Slack + Email (batch: 10 messages per stakeholder)
For each stakeholder being updated:
1. Search last 7 days of Slack DMs and relevant channels. Extract: decisions made, questions asked, meeting topics discussed.
2. Search Gmail for recent threads. Extract: unresolved questions, commitments needing status updates.
3. Summary format: `[topic] — stakeholder asked about X on [date].`
4. Drop raw messages after extraction.

---

### Phase 2: Draft Per Stakeholder

For each stakeholder (one at a time):

1. **Load:** stakeholder profile + project data + recent context with that person
2. **Filter content by format preference:**

| Preference | Include | Exclude |
|-----------|---------|---------|
| Brief | Milestones, top 2 risks, next week priorities | Story-level detail, metrics |
| Standard | All 6 sections (completed, in progress, blockers, roadmap, next week, closing) | Story breakdowns, sprint metrics |
| Detailed | Standard + story breakdowns + sprint metrics + technical decisions | Nothing |

Additional filters: Metrics Yes/No. Risks: Always / Critical only / Skip.

3. **Draft the update** — see `references/voice-guidelines.md` for blending rules. User's voice is core; stakeholder's communication profile tunes tone, formality, and structure. Address open questions from recent context. Cover topics they tend to follow up about. Use their vocabulary.
4. **Delivery channel decision:**
   1. Check stakeholder's "Preferred Channel" in profile
   2. If "Both" — check which was used last time
   3. If tied or unclear — ask user
   4. User can override per-send
5. **Format for channel:** Slack = concise, bulleted, markdown. Email = can be longer, include subject line, optional deep-dive sections.

See `references/update-templates.md` for Brief/Standard/Detailed format examples.

### Phase 3: Interactive Review

Walk through each stakeholder's draft one at a time:

```
Update for: {Stakeholder Name}
Delivering via: {Slack / Email}
Format: {Brief / Standard / Detailed}
---
{Draft content}
---
```

Actions per draft:
- **"Send"** — Approve and deliver. Gmail: create draft. Slack: send message.
- **"Edit"** — User modifies. Changes logged to refine voice + preferences.
- **"Add section"** — Include something the skill didn't surface.
- **"Remove section"** — Logged as negative signal for this stakeholder.
- **"Change channel"** — Switch Slack/email for this send.
- **"Skip"** — Don't send this week. Logged in delivery log.
- **"Next"** / **"Back"** / **"Show overview"** / **"Remaining"** — Navigation.

### Phase 4: Learn

After all updates are sent:

1. **Log delivery** in each stakeholder's profile: date, channel, topics covered, user edits, sections added/removed.
2. **Detect responses** (next time skill runs):
   - Search Gmail: `to:me from:{stakeholder email}` within 7 days of send date
   - Search Slack DM for replies within same window
   - Extract topics mentioned, sentiment (positive/neutral/negative)
   - Log findings to delivery log
3. **Update stakeholder profiles:** strengthen topics with engagement (+2), user edits (+1), weaken skipped/ignored topics (-1). Adjust format/channel if user consistently overrides.
4. **Consolidation:** Every 5 sends, average weight deltas. Re-analyze voice profile every 10 sessions or on demand. Delivery log: keep last 8 entries, summarize older entries as patterns.
5. **Session summary:**
   ```
   Updates sent: {N}
   - {Name}: {channel} — {sent/drafted/skipped}
   Profiles updated: {what changed}
   Next update due: {date}
   ```

---

## Cadence Management

- **Reminder:** If last update to any stakeholder was 7+ days ago, mention it. If running daily-planner on Friday, suggest updates.
- **Per-stakeholder cadence:** Weekly/biweekly/on-demand stored in profile. Default to showing stakeholders who are "due." User can override with "send to all" or "just {name}."
- **Catch-Up Mode:** If last update >14 days ago, automatically cover 2-week window. Adjust JQL: `DURING (startOfWeek(-2), now())`. Note gap in update. If content exceeds format preference length, apply aggressive filtering (include HIGH topics only, brief mention MEDIUM, drop LOW).

---

## Edge Cases
- **No stakeholder profiles exist** — trigger First-Run Setup before proceeding
- **Stakeholder hasn't been updated in 30+ days** — flag explicitly, ask if still active or should be archived
- **User overrides all AI suggestions** — log as strong signal, adjust aggressively
- **Stakeholder is non-responsive (no reply in 3+ sends)** — note in profile, suggest adjusting cadence or channel
- **Jira/Gmail/Slack API failure** — proceed with available sources, note gaps in draft
- **Roadmap cache is stale and refresh fails** — use stale cache with "[roadmap data may be outdated]" caveat

## When NOT to Use
- Drafting a single email reply — use `message-drafting`
- Real-time co-drafting a sensitive communication — work directly with user
- Crisis communication requiring immediate escalation — handle manually
- 10+ stakeholders in one run — split into batches to manage context

## Context Rules
- Run Phase 1 data pulls in parallel — Jira, roadmap, and per-stakeholder context are independent.
- After Phase 1, drop all raw data. Keep only extracted project summaries and stakeholder context.
- During Phase 2, load only the current stakeholder's profile. Drop it before loading the next (prevents tone cross-contamination).
- Two voice profiles active during drafting: user's voice + stakeholder's communication profile. Both dropped between stakeholders.
- During Phase 3 review, keep only one stakeholder's draft in context.
- Load roadmap cache once. Share with `daily-planner` — don't re-fetch if already fresh.
- Max 1,000 words per stakeholder profile file. Delivery log keeps last 8 entries; older entries summarized into patterns.
- Re-analyze stakeholder voice profiles every 10 sessions or on demand.
- Batch limits: Jira 5 queries, Gmail/Slack 10 messages per stakeholder.
