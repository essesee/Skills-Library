---
name: message-drafting
description: "Triage inbox, draft email or Slack replies, process unread messages."
---

# Message Drafting & Triage

## Purpose
Prioritize what to answer first, draft replies in the user's voice, and learn from corrections over time.

## Dependencies

**Tools/APIs:** Gmail, Slack
**Other Skills:** cyrano (profiles loaded at draft time — self-profile for user's voice, contact profiles for recipient context). Falls back to lightweight profiling if no Cyrano profile exists for a contact.

## Inputs
- Gmail inbox and/or Slack unreads
- Optional: one-line intent per message ("say yes but push the timeline," "decline politely," etc.)

## Outputs
- Prioritized message queue
- Draft reply per message
- Approve/deny/edit workflow per draft
- Growing contact profile database

## Workflow

### Phase 1: Triage
1. Pull unread messages (Gmail: up to 10, Slack: up to 10).
2. Categorize by urgency: **Urgent** (hours), **Important** (today), **Normal** (this week), **Low** (when convenient).
3. Recommend reply order within each tier.
4. Flag channel switches needed (e.g., "this email thread should move to Slack").
5. Present prioritized queue as a summary.

### Phase 2: Draft (One Message at a Time)
1. Load the message thread.
2. If user provided intent, capture it. If not, infer the appropriate response.
3. **Invoke the `cyrano-write` skill** to produce the draft. Pass the thread, inferred/provided intent, and the recipient name. `cyrano-write` handles self-profile loading, recipient profile loading, staleness checks, and drafting — do not load profiles or draft inline.
4. If no Cyrano contact profile exists for the recipient, `cyrano-write` will offer quick-mode or full-profile paths. Follow its flow.
5. Default to shortest version that gets the point across — bias toward brevity, especially for Slack.

### Phase 3: Approve / Deny / Edit
**Approve**: Send the message (with user confirmation). **Deny**: Skip, move to next. **Edit**: User modifies draft; log the delta and refine voice profile.

### Phase 4: Contact Profiles
Check `~/.claude/cyrano/profiles/contacts/` for existing Cyrano profiles first — these are richer and more reliable. For contacts without a Cyrano profile, build lightweight profiles passively: tone, ask patterns, formality, preferred format. Seed from last 50-100 messages per frequent contact. New contacts get **low confidence** tag until 10+ interactions. If a lightweight profile proves useful, suggest building a full Cyrano profile for that contact.

## Context Rules
- During triage: load full queue overview. Do not load drafting context.
- During drafting: load only current thread + voice profile + contact profile. Drop all other context.
- Contact profiles: separate file per person, under 500 words. Load only when drafting a reply to that person.
- Process one message at a time — do not carry forward context from previous drafts.

## When NOT to Use
- Writing formal documents or proposals — use `style-editor-expanded`.
- Analyzing message sentiment without drafting replies.
