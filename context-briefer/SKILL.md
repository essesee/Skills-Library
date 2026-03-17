---
name: context-briefer
description: "Use when switching between workstreams and need to reload context on a specific epic, project, or topic. Trigger on phrases like 'brief me on,' 'what happened with,' 'catch me up on,' 'where did we leave off with,' 'context on,' 'status of,' 'what's the latest on,' or any request to get up to speed on a specific area of work."
---

# Context Briefer

## Purpose
Context-switching is the #3 weekly time sink. The cost isn't the tasks — it's reloading the mental model. This skill generates a focused "where you left off" briefing for a specific epic, project, or topic by pulling recent activity from Jira, Slack, Confluence, and Circleback.

Unlike `daily-planner` (which scans everything to prioritize your day), Context Briefer deep-dives one workstream on demand.

## Dependencies

**Tools/APIs:**
- Jira API — epic/story status, recent transitions, comments, blockers
- Slack API — search by keywords and channels, read threads
- Confluence API — recent page edits, comments on relevant specs
- Circleback API — SearchMeetings, ReadMeetings (decisions and action items from recent meetings on topic)
- Google Calendar API — upcoming meetings related to topic

**Other Skills:**
- `company-context` — project keys, team names, Confluence spaces
- `decision-logger` — search for recent decisions on the topic (if available)

**Reference Files:**
- `references/briefing-preferences.md` — learned topic-to-source mappings, preferred depth, channel associations

## Inputs
- **Topic** (required): epic name, project key, feature area, or freeform topic
- **Time window** (optional): "since Monday," "last 2 weeks," "since I was last in this" (default: 7 days)
- **Depth** (optional): "quick" (3-sentence summary) or "full" (detailed briefing, default)

## Outputs
- A structured briefing covering: current status, recent changes, open questions, pending decisions, upcoming deadlines, and action items for the user
- Quick mode: 3-5 sentence paragraph
- Full mode: structured sections with links

---

## Workflow

### Step 1: Resolve Topic

Map the user's topic to queryable identifiers:

| Input Type | Resolution |
|-----------|-----------|
| Epic name/key | Direct Jira lookup |
| Project name | Map to Jira project key(s) via `company-context` |
| Feature area | Map to epic(s), Confluence space, Slack channels |
| Freeform topic | Search Jira + Confluence + Slack for best matches; confirm with user |

If ambiguous, ask: "I found [X] and [Y] — which one?" Don't guess.

Load `references/briefing-preferences.md` for any learned mappings (e.g., "booking pipeline" → epic PLAT-123, #booking-pipeline channel, Booking Pipeline Confluence space).

### Step 2: Gather Activity (Parallel)

Run all data pulls for the resolved topic simultaneously. Time-scope all queries to the user's window.

#### 2A: Jira — Work Status
1. Pull the epic and its child stories/tasks
2. Extract per item: key, summary, status, assignee, last status change date, recent comments (last 2)
3. Identify: what moved since the window start, what's blocked, what's newly created
4. Calculate: stories done vs. total, velocity trend if available

#### 2B: Slack — Conversations
1. Search topic keywords + project name in relevant channels (from preferences or auto-detected)
2. Search for threads mentioning the epic key or feature name
3. Extract: key discussions, questions asked (especially unanswered), decisions made, escalations
4. Tag each with: who said it, when, which channel

#### 2C: Confluence — Documentation Changes
1. Search for recently edited pages in the relevant space or with relevant labels
2. Extract: page title, editor, edit date, change summary (if available)
3. Flag: spec changes, new pages, comment threads

#### 2D: Circleback — Meeting Context
1. Search meetings by topic keywords and key participants (last N days)
2. Read matched meetings — extract: decisions made, action items (especially for user), open questions, key discussion points
3. Tag each with: meeting name, date, attendees

#### 2E: Calendar — Upcoming
1. Search upcoming events (next 7 days) matching topic keywords or key attendees
2. Extract: meeting name, date/time, attendees, agenda if available

### Step 3: Synthesize Briefing

Merge all sources into a coherent briefing. Prioritize signal over noise.

**Quick mode (3-5 sentences):**
```
{Epic/Topic} is {overall status}. Since {window start}: {key changes — 1-2 most important}.
{Open questions or blockers, if any}. Next up: {what's coming}.
```

**Full mode (structured):**

```
## Briefing: {Topic} — as of {date}

### Status
{Overall health: on track / at risk / blocked}
{Key metric: X of Y stories done, or equivalent}

### What Changed ({window})
- {Status changes, new work, completed items}
- {Each with Jira link}

### Open Questions & Blockers
- {Unanswered questions from Slack/meetings}
- {Blocked items with blocker description}
- {Pending decisions}

### Recent Decisions
- {Decisions from meetings, Slack, or decision log}
- {Each with source link}

### What's Coming
- {Upcoming deadlines}
- {Scheduled meetings on this topic}
- {Next sprint commitments}

### Action Items for You
- {Items assigned to or relevant to the user}
- {Unanswered questions directed at user}
- {Follow-ups from meetings}
```

### Step 4: Present and Offer Actions

Present the briefing. Then offer contextual actions:

| Action | When Available |
|--------|---------------|
| "Reply to {question}" | Unanswered question in Slack |
| "Unblock {story}" | Blocked item identified |
| "Update status on {item}" | Stale item detected |
| "Prep for {meeting}" | Upcoming meeting on this topic |
| "Log decision" | Decision surfaced but not formally captured |
| "Go deeper on {section}" | User wants more detail on one area |

User can take actions, ask follow-ups, or say "done."

### Step 5: Learn

After the briefing:
1. If this is a new topic, save the topic-to-source mapping in `references/briefing-preferences.md`
2. Update existing mappings if the user corrected channel/epic associations
3. Track which sections the user engaged with most (for future depth calibration)

---

## Preference Learning

`references/briefing-preferences.md`:
```markdown
## Topic Mappings
- {topic name}: epic={key}, channels=[{list}], confluence_space={key}, key_people=[{names}]

## Depth Preferences
- {topic}: {quick/full} — {reason learned}

## Section Engagement
- {topic}: {which sections user reads/acts on vs. skips}

## Session Log
- {date}: {topic} — {adjustments}
```

Consolidate every 10 sessions. Keep under 1,500 words.

---

## Context Rules
- Run Step 2 data pulls in parallel — all sources are independent.
- After Step 2, drop raw data. Keep only extracted summaries.
- Quick mode: do not load full Slack threads or Confluence pages. Summaries only.
- Full mode: load full content only for items the user asks to "go deeper" on.
- Keep the briefing itself under 500 words for full mode, 100 words for quick mode.
- Batch limits: Jira stories 30, Slack messages 15, Confluence pages 10, Circleback meetings 10.

## Edge Cases
- **Topic not found:** "I couldn't find an epic or project matching '{topic}'. Did you mean one of these: {suggestions}?"
- **No activity in window:** "Nothing has changed on {topic} since {date}. Last activity was {date}: {summary}." This is useful information.
- **Too much activity:** If >50 items changed, summarize by category rather than listing each. Offer to drill into specific areas.
- **Cross-team topic:** Include activity from other teams' Jira projects if they're linked to the epic. Flag cross-team items clearly.
- **Circleback not connected:** Skip meeting context. Note the gap.
- **User asks for multiple topics:** Process one at a time. Offer to brief on the next after the first is done.

## When NOT to Use
- **Planning the whole day** — use `daily-planner`
- **Sending a status update to someone** — use `stakeholder-update`
- **Deep backlog analysis** — use `backlog-groomer`
- **Searching for a specific decision** — use `decision-logger`
- **General "what's happening" across all projects** — use `daily-planner`
