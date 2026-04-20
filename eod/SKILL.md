---
name: eod
description: "Use when ending the workday, wrapping up work for the day, or planning tomorrow. Captures accomplishments, surfaces unfinished work from Jira, Slack, Gmail, Circleback, and git, walks through carry-forward decisions, asks guided questions, and writes tomorrow.md for /morning to consume."
---

# End of Day

## Purpose

Close the daily loop. Capture what happened today, surface what's unfinished, decide what matters tomorrow, and write a state file that `/morning` reads the next day. The skill infers unfinished work from multiple sources (plans, Jira, Slack, Gmail, Circleback, git) then guides the user through obligation scanning and priority framing.

## Dependencies

**Tools/APIs:**
- Jira API: JQL search for in-progress sprint stories
- Google Calendar API: gcal_list_events for tomorrow
- Slack API: slack_search_public_and_private for unanswered threads and open DMs
- Gmail API: gmail_search_messages for pending responses from today
- Circleback API: SearchMeetings + ReadMeetings for today's meeting action items
- Git: worktrees, status, log (local commands via Bash)

**Other Skills:**
- `/wrap`: optional invocation at end (Phase 7)
- `/morning`: consumes tomorrow.md (reads it in Agent 7 / Section 1)

**State Files:**
- `~/.claude/tomorrow.md`: written by this skill, read by /morning and next /eod
- `~/.claude/today-plan.md`: read by this skill (written by /morning Section 4)

## Inputs
- Today's date (auto-detected)
- Optional: "skip wrap" to suppress the Phase 7 prompt
- Optional: specific items to carry forward ("carry forward the caching spike")

## Outputs
- `~/.claude/tomorrow.md` overwritten with structured plan
- Optional: `/wrap` invocation

---

## State File Contract

Two files form the daily planning loop:

| File | Written by | Read by | Lifecycle |
|------|-----------|---------|-----------|
| `~/.claude/tomorrow.md` | `/eod` | `/morning` Agent 7, next `/eod` | Overwritten each `/eod` run |
| `~/.claude/today-plan.md` | `/morning` Section 4 | `/eod` Phase 1 | Overwritten each `/morning` run |

### tomorrow.md Structure

```markdown
# EOD — {date written}

## Accomplished
- {accomplishment with enough context to be meaningful}

## Carry Forward
- {item}: {status note, what remains}

## Tomorrow
### Must
- {item}
### Should
- {item}
### Could
- {item}

## Calendar Prep
- {time} {meeting}: {prep note or "no prep needed"}

## Open Threads
- {description}: {next action or who you're waiting on}
```

---

## Workflow

### Phase 1: Gather

File reads and git checks run inline. API calls dispatch as parallel tool calls in a single turn.

**Inline reads (instant):**
1. Read `~/.claude/today-plan.md` for what /morning planned today. If missing, note the gap.
2. Read `~/.claude/tomorrow.md` for last night's intent (what the user said they'd do today). If missing, note the gap.
3. Git state: open worktrees, uncommitted changes, branches with unpushed commits.
4. Conversation scan: extract accomplishments and open items from current session context.

**Parallel API calls (dispatch together):**
5. Jira sprint check: JQL for user's in-progress stories in current sprint.
6. Tomorrow's calendar: `gcal_list_events` for tomorrow.
7. Slack open threads: `slack_search_public_and_private` for threads where user was last respondent with unanswered replies, plus DMs awaiting response. Tag each with: "someone is waiting on you in #channel."
8. Gmail pending responses: `gmail_search_messages` for threads user was CC'd/To'd on that have unanswered follow-ups from today. Lightweight scan, not full triage.
9. Circleback action items: `SearchMeetings` for today's meetings, `ReadMeetings` to extract action items assigned to user or unassigned.

**After all sources return:**
10. Build carry-forward candidates: all items from today-plan.md and yesterday's tomorrow.md that aren't obviously completed (Jira status = Done/Closed), plus open obligations from Slack/Gmail/Circleback. These are candidates; the user confirms each in Phase 3.

### Phase 2: Accomplishments (Quick)

Present inferred accomplishments from:
- Conversation scan (what happened this session)
- Git commits today
- Jira story transitions (moved to Done/Closed today)

Ask: "What else did you get done today that I'm not seeing?"

If any Jira transitions represent feature completions (epic fully closed or standalone feature story shipped), note: "{Feature} shipped today. Consider `/post-ship-retro` to evaluate the bet."

Build the accomplishments list. This is fast, not a deep walkthrough.

### Phase 3: Carry Forward Walkthrough

Present each unfinished item one at a time. Sources: today-plan.md items not completed, yesterday's tomorrow.md items not completed, in-progress Jira stories, open git worktrees, unanswered Slack threads, pending Gmail responses, unaddressed Circleback meeting action items.

For each item, offer:

| Action | Effect |
|--------|--------|
| **Carry forward** | Goes into tomorrow.md with status note |
| **Done** | Moves to accomplishments (missed in Phase 2) |
| **Drop** | Removed, not carrying forward |
| **Modify** | Edit description, add context for tomorrow |

### Phase 4: Guided Questions (Layered)

**Layer 1: Obligation Scan.** Ask one at a time:
- "Did you commit to anything today that's still open? (Slack promise, meeting action item, someone waiting on you)"
- "Is anything blocked on you right now?"
- "Any deadlines in the next 48 hours I should know about?"

**Layer 2: Energy/Priority Framing.** Ask one at a time:
- "What's the one thing that would make tomorrow a win?"
- "Anything you're dreading that you should tackle first?"
- "Anything on this list that can actually wait until next week?"

Each answer may add, reprioritize, or remove items from the tomorrow plan.

### Phase 5: Calendar Prep

Walk through tomorrow's meetings one at a time. For each meeting with 2+ attendees:
- "Do you need to prep anything for {meeting} at {time}?"
- If yes, add a prep item to tomorrow.md with specifics.
- If no, mark "no prep needed."

### Phase 6: Build and Review

Assemble the full tomorrow.md content. Present it for review. User can edit, reorder, add, or remove items. Once approved, write the file to `~/.claude/tomorrow.md`.

### Phase 7: Optional Wrap

Ask: "Run session wrap-up too?"
- If yes, invoke `/wrap` via the Skill tool.
- If no, confirm "EOD complete. Tomorrow's plan saved." and exit.
- If invoked via `/wrap`, skip this phase entirely (don't ask about wrap again).

---

## Edge Cases

- **First run (no state files):** Skip the diff. Gather from Jira, Slack, Gmail, Circleback, git, calendar, and conversation only. Write tomorrow.md normally.
- **No /morning run today:** today-plan.md missing or stale (date mismatch). Fall back to yesterday's tomorrow.md + Jira/git/Slack/Gmail inference for carry-forward check.
- **Multiple sessions per day:** Each session's /eod overwrites tomorrow.md. Last one wins. Correct: latest session has the most current view.
- **Weekend/day off:** Friday's tomorrow.md persists. Monday's /morning reads it. Items may be stale, but /morning walkthrough lets user drop them.
- **Light session (nothing to report):** If no items surface in gather or questions, write minimal tomorrow.md with just calendar prep. Skip walkthrough.
- **Jira unavailable:** Skip sprint check. Note gap. Proceed with other sources.
- **Calendar unavailable:** Skip Phase 5. Note gap.
- **Slack unavailable:** Skip thread scan. Note gap. Phase 4 obligation scan catches Slack commitments manually.
- **Gmail unavailable:** Skip email scan. Note gap.
- **Circleback unavailable:** Skip meeting action items. Note gap. Phase 4 obligation scan covers meeting commitments manually.
- **Invoked via /wrap:** Skip Phase 7 (don't ask about wrap; /wrap resumes after /eod completes).

## Context Rules

- No separate agent subprocesses. All API calls are single-turn parallel tool calls.
- Jira, Calendar, Slack, Gmail, and Circleback queries dispatch together in the gather phase.
- Drop raw data from all sources after extracting relevant items in Phase 1.
- Slack scan: unanswered threads only, not full channel triage.
- Gmail scan: pending responses only, not full inbox triage (that's /comms).
- Circleback scan: today's meetings only.
- During Phase 3 walkthrough, load only the current item's context.
- Keep tomorrow's calendar data loaded through session (lightweight).

## When NOT to Use

- Only need to check one tool (Jira, Slack, Gmail): use that tool directly
- Need to draft a reply: use `comms` or `message-drafting`
- Running a full morning startup: use `/morning`
- Just want session recap without tomorrow planning: use `/wrap`
