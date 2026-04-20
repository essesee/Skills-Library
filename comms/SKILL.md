---
name: comms
description: "Scan email and Slack for messages awaiting response, rank by importance, build recipient profiles, draft replies in your voice, and review each before sending."
---

# Communications Triage

## Purpose
Scan email and Slack for messages requiring response, rank by VIP priority and staleness, profile each recipient with Cyrano, draft responses in the user's voice, and present for interactive review. Invocable standalone via `/comms` or called by the `/morning` orchestrator.

## Dependencies

**Tools/APIs:**
- Gmail API — search unread/important messages, read threads, send replies
- Slack API — search messages, read channels/DMs, read threads, send messages
- Cyrano (`cyrano-profile` skill) — recipient behavioral profiling and communication style analysis
- Cyrano (`cyrano-write` skill) — voice-matched draft generation in the user's writing style

**Reference Files:**
- `references/vip-config.md` — VIP list, staleness threshold, ranking logic, Cyrano profile management rules

## Inputs
- **Time window** (optional): how far back to scan for stale messages (default: 7 days)
- **Source filter** (optional): `email-only`, `slack-only`, or `both` (default: `both`)
- **Orchestrated mode flag** (optional): `called_by_morning = true` — uses pre-gathered comms scan data instead of re-scanning. Default: `false`

## Outputs
Ranked message queue with Cyrano-profiled drafts for interactive review.

**Standalone mode** (default): Full scan, rank, profile, draft, and interactive review.
**Orchestrated mode** (`called_by_morning = true`): Skip Phase 1 scan — use pre-gathered data from `/morning` Phase 1. Proceed directly to ranking, profiling, drafting, and review.

---

## Workflow

### Phase 1: Scan (Parallel)
Skip if `called_by_morning = true` — use pre-gathered data instead.

Run email and Slack scans simultaneously. Each source is independent.

#### 1A: Email Scan
1. Gmail search for messages awaiting response within the configured time window:
   - `is:unread -category:promotions -category:social -category:updates`
   - Also: `is:starred is:unread`
2. Exclude: messages sent by me, threads where I've already replied, automated notifications
3. Extract per message: sender name, sender email, subject, date received, thread preview (first 200 chars)
4. Batch limit: 20 messages max

#### 1B: Slack Scan
1. Search DMs and channels for messages directed at me or awaiting response:
   - `slack_search_public_and_private` with `to:me` (within time window)
   - Unread DMs from the time window
2. Filter out:
   - Threads where I've responded (including emoji reactions — these count as responses per VIP config)
   - Bot messages (unless from known systems like Jira)
   - Messages in muted channels
3. Extract per message: sender name, sender Slack ID, channel/DM context, message preview, timestamp, thread context if applicable
4. Batch limit: 20 messages max

### Phase 2: Rank
1. Load VIP config from `references/vip-config.md`
2. Tag each message with VIP tier:
   - **Tier 1 (VIP):** Sender matches VIP list (configured VIP contacts)
   - **Tier 2 (Standard):** Everyone else
3. Within each tier, sort by staleness (oldest unanswered first)
4. Deduplicate: if the same person messaged on both email and Slack about the same topic, group them into a single entry. Present both channels but draft one response.
5. Drop raw scan results after ranking. Keep only the ranked message list with previews.

### Phase 3: Profile & Draft (Per Message)
Process each message in ranked order. For each message:

#### Step 1: Check/Build Cyrano Profile
- **VIP contacts:** Profile should already exist. Check freshness — if older than 7 days, refresh by pulling recent Slack messages and email threads.
- **Non-VIP contacts:** Profile may not exist. Build one on the fly using available data (Slack message history, email threads, meeting notes from Circleback).
- **Hard gate:** If a profile cannot be built (no data available), surface the message without a draft. Note: "Could not profile this recipient — draft manually or skip."

#### Step 2: Draft Response
Invoke `cyrano-write` with:
- The message being responded to (full thread context, summarized if >2000 words)
- The recipient's Cyrano profile (communication style, preferences, behavioral patterns)
- the user's voice profile
- Any relevant session context (e.g., platform health findings if called from `/morning`)

#### Step 3: Queue for Review
Add the message + draft to the review queue. Do not send automatically.

### Phase 4: Interactive Review
Present each message with its draft, one at a time in ranked order:

```
[Tier {n}] {email/slack} From: {sender} — {staleness} old
{message preview or summary}

Draft:
{cyrano-generated response}

[Send] [Edit] [Skip] [Flag for later]
```

**Actions:**
- **Send** — send the message via the source channel (Slack reply or Gmail send). Requires user approval before sending.
- **Edit** — open draft for modification. Re-present with Send option after editing.
- **Skip** — move to next message. No action taken, not flagged.
- **Flag for later** — mark as needs-response. Will re-surface in the next `/morning` or `/comms` session.

**Navigation:**
- **Next** / **Back** — move between messages
- **Show overview** — display remaining message count by tier
- **Done** — end the triage session, trigger Phase 5

### Phase 5: Summary
Triggered when user says "done" or all messages are reviewed.

```
Comms triage complete:
- {X} sent, {Y} skipped, {Z} flagged for later
- {count} messages remaining in queue
```

---

## Configuration
- VIP list: configured VIP contacts, configured VIP contacts (configurable via `references/vip-config.md`)
- Staleness threshold: 24 hours default (configurable)
- Batch limits: 20 emails, 20 Slack messages per scan
- Cyrano profile staleness: refresh if >7 days old

## Edge Cases
- **No messages awaiting response:** Report "Inbox clear — no messages awaiting response." Not an error. Skip to Phase 5 summary.
- **Cyrano profile build fails:** Surface the message without a draft. Note: "Could not profile this recipient — draft manually or skip." Do not block the triage flow.
- **VIP not found in Slack/email:** Still rank at VIP tier based on name match. Profile will be thinner but functional.
- **Thread too long (>2000 words):** Summarize the thread before passing to Cyrano for drafting. Note the summary in the review presentation.
- **Same person, multiple messages:** Group and present together as a single entry. Draft can address multiple topics in one response.
- **Gmail API unavailable:** Skip email scan. Note gap. Proceed with Slack only.
- **Slack API unavailable:** Skip Slack scan. Note gap. Proceed with email only.
- **Both unavailable:** Report error and exit.

## Context Rules
- Run Phase 1 scans in parallel — email and Slack are independent.
- After Phase 2 ranking, drop raw scan results. Keep only ranked message list with previews.
- During Phase 3, load only the current message's full thread + recipient Cyrano profile. Drop the previous message's context before loading the next.
- Cyrano profile build is the most expensive operation. Cache aggressively — once built, reuse within the session.
- In orchestrated mode (`called_by_morning = true`), Phase 1 is skipped entirely. Data arrives pre-gathered from `/morning` Phase 1.
- Thread summaries (for >2000 word threads) are generated once and reused if the user drills in.
- Flagged messages are persisted to a references file for the next session to pick up.

## When NOT to Use
- **Drafting a single specific message** — use `message-drafting` or invoke Cyrano directly
- **Searching for a specific Slack conversation** — use `slack:find-discussions`
- **Reading a specific channel** — use `slack:summarize-channel`
- **Sending a pre-written message** — use Slack/Gmail tools directly
