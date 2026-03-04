---
name: daily-planner
description: "Use when the user needs to prioritize work across Jira, Gmail, Slack, Calendar, and Circleback for the day. Trigger on phrases like 'plan my day,' 'what should I work on today,' 'daily briefing,' 'morning standup prep,' 'what's on my plate,' 'build my to-do list,' 'what needs my attention,' or any request to aggregate today's work across tools."
---

# Daily Planner

## Purpose
Work is scattered across Jira, Gmail, Slack, Google Calendar, and Circleback. Starting the day means checking five different tools to figure out what matters. This skill pulls from all five, prioritizes using the Google Drive platform roadmap as the source of truth, surfaces relevant Slack messages even without direct tags, pulls meeting action items and decisions from Circleback, learns preferences over time, and then walks through every item interactively so the user can take action on each one.

## Dependencies

**Tools/APIs:**
- Jira API — JQL search, read epics/stories, update issues
- Gmail API — search unread/important, read threads, create drafts
- Slack API — search messages, read channels/DMs, send messages
- Google Calendar API — list events, find free time
- Circleback API — SearchMeetings, ReadMeetings (action items, notes, insights), SearchTranscripts, SearchCalendarEvents, FindProfiles
- WebFetch — access Google Drive roadmap (shared/published link)

**Other Skills:**
- `jira-template-builder` — epic/roadmap mapping rules (if available)
- `voice-analyzer` — drafting replies in user's voice
- `message-drafting` — email and Slack reply workflows

**Reference Files:**
- `references/preferences.md` — learned priority signals, Slack relevance, communication patterns
- `references/roadmap-cache.md` — cached Google Drive roadmap content

## Inputs
- Today's date (auto-detected)
- Optional: specific Jira project keys to focus on
- Optional: specific Slack channels to prioritize
- Optional: time horizon override (default: today)

## Outputs
- Prioritized daily to-do list (overview)
- Interactive walkthrough of every item with per-item actions
- Roadmap alignment report (Google Drive roadmap vs. Jira story readiness)
- Drafted replies, written requirements, updated tickets — as the user approves them

---

## Preference Learning System

This skill improves over time by tracking how the user interacts with the daily plan. Preferences are stored in `references/preferences.md` and updated after each session.

### What Gets Tracked

- **Priority:** promotions, demotions, skips, action speed → calibrate urgency sense
- **Slack:** channel engagement, sender importance, actioned keywords, dismissed noise → tune relevance model
- **Communication:** reply-first senders, thread engagement, reply effort level → refine urgency signals
- **Jira:** active epics, gap types actioned vs. skipped → focus attention
- **Circleback:** meeting types that produce actionable items, action items actioned vs. skipped, which attendee groups generate follow-ups → tune meeting intelligence relevance

### How Learning Works
1. During the interactive walkthrough, every user action is a signal (action taken, skipped, re-prioritized, dismissed)
2. At end of session (or when user says "done"), update `references/preferences.md`
3. Preferences loaded at the start of every session and applied to prioritization
4. Conflicting signals resolved by recency — newer behavior overrides older patterns
5. Every 10 sessions, consolidate: trim stale entries, strengthen confirmed patterns, stay under 3,000 words

### Preference File Structure
`references/preferences.md`:
```markdown
## Priority Signals
- {signal}: {weight} — {evidence}

## Slack Relevance
### High-Priority Channels
- {channel}: {weight} — {reason}
### High-Priority People
- {person}: {weight} — {reason}
### Relevance Keywords
- {keyword/topic}: {weight}
### Negative Signals (Noise)
- {pattern}: {reason to exclude}

## Communication Preferences
- {pattern}: {detail}

## Jira Focus
- {epic/area}: {importance level}
- {gap types they care about}: {ranked list}

## Circleback Meeting Intelligence
### Meeting Types That Produce Action Items
- {meeting name/pattern}: {frequency} — {typical action types}
### Attendee Groups With Follow-ups
- {group/person}: {weight} — {reason}
### Action Item Patterns
- {pattern}: {actioned or skipped} — {notes}

## Session Log
- {date}: {key adjustments made}
```

---

## Google Drive Roadmap — Source of Truth

The platform roadmap lives in Google Drive — canonical source for what the team should be working on.

### Roadmap Workflow
1. **First run:** Ask user for Google Drive roadmap URL. Must be shared/published. Fetch via `WebFetch`, parse structure, cache in `references/roadmap-cache.md` with timestamp.
2. **Each session:** Check cache age. If >24h or missing, re-fetch. User can force with "refresh roadmap."
3. **Cross-reference against Jira:**

| Gap Type | Detection |
|----------|-----------|
| Missing epic | Roadmap item has no Jira epic |
| Epic needs stories | Epic exists but no child stories |
| Stories not ready | Stories lack requirements/AC |
| Stale active work | Roadmap says active, Jira stories stale >14d |
| Orphan epic | Jira epic has no roadmap item |

---

## Slack Relevance Without Tags

### Relevance Detection Model

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

**Layer 3: Learned Adjustments** — boost/penalize based on past sessions: actioned keywords, read-but-no-post channels, dismissed patterns.

**Scoring thresholds:** >=70 → P1. 40-69 → P2 ("Possibly relevant"). <40 → skip (log for learning).

**Bootstrapping (first few sessions):** Ask for priority channels and key people. Auto-seed keywords from Jira + roadmap. Refine from behavior.

---

## Workflow

### Phase 1: Gather (Parallel)
Run all data pulls simultaneously.

#### 1A: Google Drive Roadmap
1. Check `references/roadmap-cache.md` freshness
2. If stale or missing, fetch via `WebFetch`, parse, update cache

#### 1B: Jira — Roadmap-Aligned Story Readiness
1. Pull active epics from relevant projects via JQL
2. Match epics to roadmap items
3. For each roadmap-linked epic, find stories and check readiness:

   | Check | Detection | Suggested Action |
   |-------|-----------|-----------------|
   | Missing description | Empty or < 50 chars | "Write requirements for {story}" |
   | No acceptance criteria | Lacks AC patterns | "Add acceptance criteria to {story}" |
   | Unestimated | Points/estimate is null | "Estimate {story} with team" |
   | No assignee | Assignee is null | "Assign {story}" |
   | Stale | Status unchanged > 14 days | "Update status on {story}" |
   | Blocked | Status = Blocked/Needs Info | "Unblock {story}" |
   | Missing epic link | No parent epic | "Link {story} to an epic" |
   | No sprint | Not in any sprint | "Groom {story} into sprint" |

4. Find user's assigned stories needing action (current sprint)
5. Build roadmap alignment gap table
6. Process in batches of 5 epics. Extract gaps, drop raw data.

#### 1C: Gmail — Actionable Messages
1. Search: `is:unread -category:promotions -category:social -category:updates`
2. Also: `is:starred is:unread`
3. Read each (up to 20), extract sender, subject, urgency signals, action needed
4. Apply learned sender importance from preferences
5. Categorize: **Needs Reply**, **FYI Only**, **Can Wait**

#### 1D: Slack — Tagged + Relevant Messages (batch: 10 messages)
1. Direct: `slack_search_public_and_private` with `to:me` (last 24h) + unread DMs
2. Contextual: scan high-priority channels and people, score against relevance model
3. Tag each surfaced message with why: "mentioned you", "your project discussed", "key person", "decision being made"

#### 1E: Google Calendar + Circleback Meeting Prep (batch: all today's events, typically <15)
1. `gcal_list_events` for today
2. Extract: title, time, duration, attendees, agenda
3. `gcal_find_my_free_time` for focus blocks
4. For each meeting with 2+ attendees, search Circleback (`SearchMeetings`) for the most recent past meeting with the same attendees/topic
5. If found, `ReadMeetings` to pull: previous action items, open decisions, key notes
6. Attach as "Last meeting context" to the calendar item for prep during walkthrough

#### 1F: Circleback — Meeting Action Items & Decisions (batch: 20 meetings max)
1. `SearchMeetings` for last 3 days (no search term, just date range)
2. `ReadMeetings` for all returned meetings — extract action items, decisions, insights
3. Filter action items: keep those assigned to user or unassigned (likely user's responsibility as PO)
4. Tag each with source meeting name, date, and attendees
5. Deduplicate against Jira — if an action item already has a linked Jira ticket, mark as "tracked" and deprioritize
6. For items touching active roadmap topics (from cache), cross-reference and note alignment
7. Optionally: `SearchTranscripts` for keywords matching active Jira stories/roadmap items to surface meeting discussions the user may have missed or forgotten

### Phase 2: Prioritize

**Learned preferences override default tiers.** Check `references/preferences.md` first — if the user consistently promotes or demotes a category, apply that override before default assignment.

Default tiers (when no preference signal exists):

| Tier | Label | Items |
|------|-------|-------|
| P0 | Do First | Blockers, deadlines, at-risk roadmap items, urgent messages, imminent meetings, stale meeting action items (>3 days old) |
| P1 | Do Today | Sprint stories needing requirements, roadmap gaps, direct questions 24h+, Slack >=70, meeting action items from last 3 days |
| P2 | Do If Time | Unestimated/unassigned stories, non-urgent replies, Slack 40-69, stale stories, meeting decisions needing follow-up |
| P3 | Delegate/Defer | Items user typically dismisses, low-priority backlog, FYI-only messages, tracked action items (already in Jira) |

### Phase 3: Present Overview
Show the full daily plan as a scannable summary:

```
## Today's Plan — {date}
{total items count} items across Jira, Gmail, Slack, Calendar

### Schedule
{time-blocked view: meetings + focus blocks}

### Summary by Priority
- P0: {count} items — {top 2-3 previewed}
- P1: {count} items
- P2: {count} items
- P3: {count} items

Ready to work through them? Starting with P0.
```

This is just the overview. The real work happens in Phase 4.

### Navigation Commands (Available in All Phases)
- **"Next"** — Skip current item, move to next
- **"Back"** — Go back to previous item
- **"Jump to P{n}"** — Skip to a specific priority tier
- **"Show overview"** — Re-display the Phase 3 summary
- **"Done for now"** — End the walkthrough, trigger Phase 5 learning
- **"Remaining"** — Show count of items left per priority tier

### Phase 4: Interactive Walkthrough

Work through **every item** one at a time, priority order (P0 → P3). Present with context, offer type-specific actions.

**If an email or Slack thread exceeds 1,000 words, summarize before loading full context.**

#### Presentation Template
```
[P{n}] {type indicator}: {title/summary}
{type-specific context lines}
```

#### Actions by Item Type

| Type | Actions |
|------|---------|
| **Jira Story** | "Write requirements" (draft desc + AC, update Jira), "Add AC only", "Assign to {person}", "Move to sprint", "Comment" |
| **Gmail** | "Draft reply" (via voice-analyzer + message-drafting → Gmail draft), "Quick ack", "Forward to {person}", "Star for later" |
| **Slack** | "Reply", "React", "Reply in thread", "Read full thread" |
| **Calendar** | "Prep notes" (pull Jira/Slack/email context + Circleback last meeting notes), "Add agenda", "Decline", "Show last meeting notes" |
| **Meeting Action Item** | "Create Jira ticket" (draft story from action item), "Draft follow-up" (Slack/email to relevant attendees), "Mark done" (note in session log), "Already tracked" (link to existing Jira ticket), "Search transcript" (pull transcript context via SearchTranscripts) |
| **Roadmap Gap** | "Create epic", "Create stories", "Write requirements", "Flag in standup" |
| **All types** | "Skip" (logged as preference signal), "Not my problem" / "Not important" / "Not relevant" (logged as strong negative signal) |

### Phase 5: Learn
Triggered when user says "done" or all items are exhausted.

1. Tally actions taken per item type:
   - Actioned, skipped, dismissed, re-prioritized
2. Update `references/preferences.md`:
   - Strengthen signals for items actioned
   - Weaken signals for items dismissed or marked "not relevant" / "not my problem"
   - Add new keywords/channels/people discovered
   - Log session date and key adjustments
3. If 10th+ session, run consolidation:
   - Remove zero-weight signals
   - Merge duplicates
   - Trim to stay under 3,000 words
4. Summary to user:
   ```
   Session complete — {X} items actioned, {Y} skipped, {Z} deferred.
   Preferences updated: {1-2 sentence summary of what changed}.
   ```

---

## Edge Cases
- **Empty source** (no unread Gmail, no Slack messages, no recent meetings, etc.) — skip that source, note it in overview. Do not error.
- **API failure** — log which source failed, proceed with remaining sources. Note gap in overview.
- **Circleback not connected** — skip Phase 1F and Circleback meeting prep in 1E. Note in overview. Do not error.
- **No active Jira epics** — skip Jira story readiness checks. Still check roadmap alignment.
- **No roadmap URL cached** — trigger first-run setup flow before proceeding.
- **All items are P3** — present overview, ask if user wants to walkthrough or skip to "Done."

## When NOT to Use
- Only need to check one tool (Jira, Gmail, Slack, or Calendar) — use that tool directly
- Need to draft a single email or Slack reply — use `message-drafting`
- Running a backlog grooming session — use `backlog-groomer`
- Need to process meeting notes into tickets — use `ticket-proposer`

## Context Rules
- Run Phase 1 data pulls in parallel — each source is independent.
- Load roadmap cache once at session start. Keep through session for cross-referencing.
- Load preferences file once at session start. Update at session end only. Do not reload mid-workflow.
- After Phase 2, drop all raw data. Keep only prioritized item summaries.
- During Phase 4, load only the current item's full context. Drop previous item context before loading next.
- When drafting a reply: load only the relevant thread + voice profile. Drop all other context.
- When writing Jira requirements: load only the story + epic + roadmap item. Drop all other context.
- Keep calendar data loaded through entire session (lightweight footprint).
- Summarize email/Slack threads exceeding 1,000 words before loading into walkthrough context.
- Batch limits: Jira epics 5, Gmail messages 10, Slack messages 10, Calendar events all, Circleback meetings 20.
- Score Slack relevance during Phase 1D ingestion only — do not re-score later.
- Circleback meeting prep context (Phase 1E step 4-6): load per-meeting during walkthrough, not all at once. Keep lightweight.
- SearchTranscripts (Phase 1F step 7): only run if user has opted in or if matching a high-priority roadmap/Jira keyword. Do not speculatively search all transcripts.
