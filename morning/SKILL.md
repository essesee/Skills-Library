---
name: morning
description: "Start the workday with a full morning briefing — gather platform health, industry signals, calendar, Jira, Slack, and email in parallel, then walk through priorities and draft responses."
---

# Morning Routine

## Purpose
Interactive morning startup routine (~1hr). Gathers data from all sources in parallel (Calendar, Jira, Slack, Gmail, Circleback, PostHog, web), then walks through five sections: prior work review, platform health, industry briefing, daily planning, and communications triage. Data-first, then planning — so signals inform priorities.

Absorbs and replaces the `daily-planner` skill. All daily-planner functionality (preference learning, Slack relevance model, roadmap alignment, interactive prioritization) is preserved here.

## Dependencies

**Tools/APIs:**
- Jira API — JQL search, read epics/stories, update issues
- Gmail API — search unread/important, read threads, create drafts
- Slack API — search messages, read channels/DMs, send messages
- Google Calendar API — list events, find free time
- Circleback API — SearchMeetings, ReadMeetings, SearchTranscripts, SearchCalendarEvents, FindProfiles
- PostHog — platform metrics, error tracking, funnel analysis
- WebFetch — Google Drive roadmap, travel industry news

**Other Skills:**
- `platform-health` — invoked in orchestrated mode for Section 2
- `comms` — invoked in orchestrated mode for Section 5
- `competitive-intel-scanner` — travel tech competitor activity for Section 3
- `vendor-signal-scanner` — vendor/provider signals for Section 3
- `customer-signal-scanner` — club escalation signals for Section 3
- `cyrano-profile` / `cyrano-write` — recipient profiling and voice-matched drafting (via `comms`)
- `user-story-writer` — story descriptions when writing Jira requirements

**Reference Files:**
- `references/morning-config.md` — section order, parallel agent config, source mappings
- `references/preferences.md` — learned priority signals, Slack relevance, communication patterns
- `references/roadmap-cache.md` — cached Google Drive roadmap content

## Inputs
- Today's date (auto-detected)
- Optional: specific Jira project keys to focus on
- Optional: specific Slack channels to prioritize
- Optional: skip sections (e.g., "skip industry briefing today")

## Outputs
- Five-section interactive walkthrough with per-item actions
- Platform health report (metrics, issues, anomalies)
- Industry/competitive briefing
- Prioritized daily plan with roadmap alignment
- Drafted communications with Cyrano profiling
- Updated preferences file (learned from session actions)

---

## Preference Learning System

Absorbed from `daily-planner`. Preferences stored in `references/preferences.md` and updated after each session.

### What Gets Tracked
- **Priority:** promotions, demotions, skips, action speed
- **Slack:** channel engagement, sender importance, actioned keywords, dismissed noise
- **Communication:** reply-first senders, thread engagement, reply effort level
- **Jira:** active epics, gap types actioned vs. skipped
- **Circleback:** meeting types producing action items, attendee groups generating follow-ups
- **Morning sections:** which sections get drilled into vs. skipped, time spent per section

### How Learning Works
1. Every user action during the walkthrough is a signal
2. At session end (or "done"), update `references/preferences.md`
3. Preferences loaded at session start and applied to prioritization
4. Conflicting signals resolved by recency
5. Every 10 sessions, consolidate: trim stale entries, stay under 3,000 words

---

## Google Drive Roadmap

Platform roadmap in Google Drive — canonical source for what the team should be working on.

1. **First run:** Ask for Google Drive roadmap URL. Fetch via `WebFetch`, cache in `references/roadmap-cache.md`
2. **Each session:** Check cache age. If >24h or missing, re-fetch. Force with "refresh roadmap."
3. **Cross-reference against Jira:**

| Gap Type | Detection |
|----------|-----------|
| Missing epic | Roadmap item has no Jira epic |
| Epic needs stories | Epic exists but no child stories |
| Stories not ready | Stories lack requirements/AC |
| Stale active work | Roadmap says active, Jira stories stale >14d |
| Orphan epic | Jira epic has no roadmap item |

---

## Slack Relevance Model

Apply all three layers additively. Layer 1 bypasses scoring; Layers 2+3 combine into a 0-100 score.

**Layer 1: Direct Signals (always surface, skip scoring)**
- @mentioned, DM/group DM, or thread user previously replied to

**Layer 2: Contextual Signals (scored 0-100)**

| Signal | Weight | Detection |
|--------|--------|-----------|
| Channel importance | High | Learned from engagement frequency |
| Sender importance | High | Learned from interaction frequency |
| Topic match | High | Keywords from active Jira stories, epics, roadmap items |
| Project mention | High | References user's codebase area or project name |
| Question pattern | Medium | Question in a monitored channel |
| Decision signal | Medium | "let's go with," "final call" in relevant channels |
| Blocker/escalation | Medium | "blocked on," "need help with" in relevant channels |
| Thread spike | Low | 5+ rapid replies in relevant thread |

**Layer 3: Learned Adjustments** — boost/penalize based on past sessions.

**Thresholds:** >=70 → P1. 40-69 → P2. <40 → skip (log for learning).

**Bootstrapping:** First sessions — ask for priority channels and key people. Auto-seed keywords from Jira + roadmap.

---

## Workflow

### Phase 1: Parallel Data Gather (Background)

Dispatch all 8 agents concurrently. Each is independent.

#### Agent 1: Calendar + Meetings
1. `gcal_list_events` for today
2. Extract: title, time, duration, attendees, agenda
3. `gcal_find_my_free_time` for focus blocks
4. For each meeting with 2+ attendees, `SearchMeetings` via Circleback for the most recent past meeting with same attendees/topic
5. If found, `ReadMeetings` to pull previous action items, open decisions, key notes
6. Attach as "Last meeting context" to the calendar item

#### Agent 2: Sprint/Project State
1. Pull active epics from your Jira project via JQL
2. Match epics to roadmap items (from cache)
3. For each roadmap-linked epic, check story readiness (description, AC, estimates, assignees, sprint assignment)
4. Find user's assigned stories needing action (current sprint)
5. Build roadmap alignment gap table
6. Check for epics completed (all stories Done/Released) in the last 7 days. Flag for post-ship retro prompt in Section 1.
7. Batch limit: 5 epics at a time. Drop raw data after extraction.

#### Agent 3: Channel Activity
1. Direct: `slack_search_public_and_private` with `to:me` (last 24h) + unread DMs
2. Contextual: scan high-priority channels and people using Slack relevance model
3. Tag each surfaced message with why: "mentioned you", "your project discussed", "key person", "decision being made"
4. Batch limit: 10 messages

#### Agent 4: Email Scan
1. Search: `is:unread -category:promotions -category:social -category:updates`
2. Also: `is:starred is:unread`
3. Read each (up to 20), extract sender, subject, urgency signals, action needed
4. Apply learned sender importance from preferences
5. Categorize: Needs Reply, FYI Only, Can Wait

#### Agent 5: Platform Health
1. Invoke `platform-health` skill with `called_by_morning = true`
2. Receive structured data: metrics health, issue health, anomalies
3. No interactive presentation — that happens in Phase 2

#### Agent 6: Industry Briefing
1. Invoke `competitive-intel-scanner` in orchestrated mode — travel tech competitor activity
2. Invoke `signal-scanner` with source type `vendor` in orchestrated mode — vendor/provider signals
3. Invoke `signal-scanner` with source type `customer` in orchestrated mode — club escalation signals (partner organization focus)
4. Web search: "travel industry news" and "industry news" (last 7 days)
5. Consolidate all signals into a briefing with source attribution

#### Agent 7: Prior Work
1. Read `~/.claude/tomorrow.md` if it exists. This is the **primary** source for prior work context. It contains: accomplishments, carry-forward items, tomorrow's priorities, calendar prep notes, and open threads (written by `/eod`).
2. Read recent memory files from `~/.claude/projects/` for session recaps
3. Check `git log --oneline -20` across known project directories for recent commits
4. List open git worktrees for unfinished feature branches
5. Check for any incomplete task lists from prior sessions
6. Summarize: what was being worked on, what's unfinished, what's pending review. If tomorrow.md exists, use it as the primary structure and supplement with git/memory/worktree data.

#### Agent 8: Comms Triage Data
1. Run the same scan logic as `comms` Phase 1 (email + Slack)
2. Apply VIP ranking from `skills/comms/references/vip-config.md`
3. Return ranked message list — profiling and drafting happen in Phase 2 Section 5

#### After All Agents Complete
- Merge results into a unified morning briefing structure
- Drop all raw data from agents. Keep only processed summaries and ranked items.
- If any agent failed, note the gap and proceed with available data.

---

### Phase 2: Interactive Walkthrough

Present each section in order. All sections skippable. User can say "skip" at any section header to move to the next.

#### Navigation Commands (Available in All Sections)
- **Next** — skip current item, move to next
- **Back** — go back to previous item
- **Skip section** — jump to next section
- **Show overview** — re-display the section summary
- **Done for now** — end walkthrough, trigger Phase 3 learning
- **Remaining** — show count of items left per section

---

#### Section 1: Prior Work Review

If `~/.claude/tomorrow.md` exists (written by `/eod`), use it as the primary structure:

```
## Prior Work Review (from last EOD)

Yesterday's accomplishments:
- {accomplishment from tomorrow.md}

Carry forward items:
- {item}: {status note}

Planned priorities:
### Must: {items}
### Should: {items}
### Could: {items}

Open threads:
- {thread}: {next action}

Anything changed since last night? Want to adjust priorities?
```

If no tomorrow.md exists, fall back to inferred prior work:

```
## Prior Work Review

Recent sessions:
- {session date}: {what was being worked on} — {status}

Open worktrees:
- {branch}: {directory} — {last commit message}

Unfinished tasks:
- {task description} — {source}

Anything you want to pick back up today?
```

If Agent 2 flagged recently completed epics, surface them:
> "{Epic} completed since last session. Run `/post-ship-retro` when ready to evaluate the bet."

User can flag items to carry into daily planning. Flagged items get P0 priority in Section 4.

---

#### Section 2: Platform Health

Present `platform-health` structured data from Agent 5. Same three-layer format as standalone platform-health:

1. **Metrics Health** — insurance attachment rates, coverage gaps, manual booking volume, WoW/YoY
2. **Issue Health** — ticket inflow, clusters, self-service candidates
3. **Anomalies** — anything that moved notably

Per layer: **Drill in** / **Move on** / **Investigate**

Items flagged during drill-in carry context into daily planning (e.g., "insurance attachment drop needs investigation" becomes a planning item).

---

#### Section 3: Industry Briefing

```
## Industry Briefing — {date}

Travel Industry:
- {headline}: {one-sentence summary} (source: {web/slack/gmail})
- ...

Competitive Intel:
- {competitor}: {what changed} (source: competitive-intel-scanner)
- ...

Vendor Signals:
- {vendor}: {signal} (source: vendor-signal-scanner)
- ...

Club Signals:
- {club}: {signal} (source: customer-signal-scanner)
- ...

Anything affecting current platform work?
[Drill in] [Move on]
```

Highlight items that affect current sprint work or roadmap items. These carry into daily planning.

---

#### Section 4: Daily Planning

This section absorbs `daily-planner`'s Phases 2-4. Uses data from Agents 1-4 plus context from Sections 1-3.

##### Step 1: Prioritize

Load preferences from `references/preferences.md`. Apply learned overrides first, then default tiers:

| Tier | Label | Items |
|------|-------|-------|
| P0 | Do First | Flagged items from Sections 1-3, blockers, deadlines, at-risk roadmap items, urgent messages, imminent meetings, stale meeting action items (>3 days) |
| P1 | Do Today | Sprint stories needing requirements, roadmap gaps, direct questions 24h+, Slack >=70, meeting action items from last 3 days |
| P2 | Do If Time | Unestimated/unassigned stories, non-urgent replies, Slack 40-69, stale stories, meeting decisions needing follow-up |
| P3 | Delegate/Defer | Items user typically dismisses, low-priority backlog, FYI-only messages, tracked action items already in Jira |

##### Step 2: Present Overview

```
## Today's Plan — {date}
{total items count} items across Jira, Gmail, Slack, Calendar + morning signals

### Schedule
{time-blocked view: meetings + focus blocks}

### Summary by Priority
- P0: {count} items — {top 2-3 previewed}
- P1: {count} items
- P2: {count} items
- P3: {count} items

Ready to work through them? Starting with P0.
```

##### Step 3: Interactive Walkthrough

Work through every item one at a time, P0 → P3. Present with context, offer type-specific actions:

| Type | Actions |
|------|---------|
| **Jira Story** | "Write requirements" (draft desc + AC), "Add AC only", "Assign to {person}", "Move to sprint", "Comment" |
| **Gmail** | "Draft reply" (delegates to comms/Cyrano), "Quick ack", "Forward to {person}", "Star for later" |
| **Slack** | "Reply", "React", "Reply in thread", "Read full thread" |
| **Calendar** | "Prep notes" (pull context + Circleback notes), "Add agenda", "Decline", "Show last meeting notes" |
| **Meeting Action Item** | "Create Jira ticket", "Draft follow-up", "Mark done", "Already tracked", "Search transcript" |
| **Roadmap Gap** | "Create epic", "Create stories", "Write requirements", "Flag in standup" |
| **Platform Signal** | "Investigate" (deep dive PostHog/Jira), "Create ticket", "Flag for team", "Note for later" |
| **All types** | "Skip" (logged as preference signal), "Not my problem" / "Not important" (strong negative signal) |

##### Step 4: Write Today's Plan

After the walkthrough completes (or user says "done"), write a snapshot of the prioritized items to `~/.claude/today-plan.md`. This file is consumed by `/eod` to diff planned vs accomplished.

```markdown
# Today's Plan — {date}

## P0
- {item}
## P1
- {item}
## P2
- {item}
## P3
- {item}
```

---

#### Section 5: Communications Triage

Invoke `comms` skill with `called_by_morning = true`, passing the pre-gathered comms data from Agent 8.

The `comms` skill handles:
- Cyrano profiling for each recipient
- Voice-matched draft generation
- Interactive review (send/edit/skip/flag)
- Session summary

See `skills/comms/SKILL.md` for full workflow details.

---

### Phase 3: Learn

Triggered when user says "done" or all sections exhausted.

1. Tally actions taken per item type: actioned, skipped, dismissed, re-prioritized
2. Note section engagement: which sections got drill-ins vs. skips, time indicators
3. Update `references/preferences.md`:
   - Strengthen signals for items actioned
   - Weaken signals for items dismissed or marked "not relevant"
   - Add new keywords/channels/people discovered
   - Log session date and key adjustments
4. If 10th+ session, run consolidation: remove zero-weight signals, merge duplicates, trim to stay under 3,000 words
5. Summary to user:
```
Morning routine complete — {X} items actioned, {Y} skipped, {Z} deferred.
Preferences updated: {1-2 sentence summary of what changed}.
```

---

## Edge Cases
- **Empty source** (no unread Gmail, no Slack messages, no recent meetings): skip that source, note in overview. Not an error.
- **Agent failure** (API timeout, auth issue): log which source failed, proceed with remaining. Note gap in section header.
- **Circleback not connected:** Skip meeting prep context. Note in Section 4 calendar items.
- **No active Jira epics:** Skip story readiness checks. Still check roadmap alignment.
- **No roadmap URL cached:** Trigger first-run setup flow before Phase 1.
- **All items are P3:** Present overview, ask if user wants to walkthrough or skip to "Done."
- **Platform health skill unavailable:** Skip Section 2. Note gap.
- **Comms skill unavailable:** Fall back to raw message list without Cyrano profiling.
- **Industry briefing agents fail:** Skip Section 3. Note gap.
- **User skips all sections:** Proceed directly to Phase 3 learning (log the skips as preference signals).

## Context Rules
- Run all Phase 1 agents in parallel — each is independent.
- Load roadmap cache once at session start. Keep through session for cross-referencing.
- Load preferences file once at session start. Update at session end only.
- After Phase 1 merges, drop all raw agent data. Keep only processed summaries.
- During Phase 2, load only the current section's data. Drop previous section context before loading next.
- Within Section 4, load only the current item's full context. Drop previous item before loading next.
- When drafting a reply (Section 5): load only the relevant thread + voice profile. Drop other context.
- When writing Jira requirements: load only the story + epic + roadmap item. Drop other context.
- Keep calendar data loaded through entire session (lightweight footprint).
- Summarize email/Slack threads exceeding 1,000 words before loading into walkthrough context.
- Batch limits: Jira epics 5, Gmail messages 20, Slack messages 10, Calendar events all, Circleback meetings 20.
- Platform health in orchestrated mode — structured data only, no interactive presentation.
- Comms in orchestrated mode — uses pre-gathered data, handles its own interaction.

## When NOT to Use
- Only need to check one tool (Jira, Gmail, Slack, Calendar) — use that tool directly
- Need to draft a single message — use `message-drafting` or `comms`
- Running a backlog grooming session — use `backlog-groomer`
- Need meeting notes processed into tickets — use `ticket-proposer`
- Need platform health only — use `platform-health` standalone
- Need comms triage only — use `comms` standalone
