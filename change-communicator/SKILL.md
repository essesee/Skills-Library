---
name: change-communicator
description: "Use when something ships, changes, or gets deprecated and needs audience-specific communications. Trigger on phrases like 'announce this change,' 'rollout comms,' 'deprecation notice,' 'notify clubs about,' 'communicate this update,' 'migration announcement,' 'breaking change notice,' 'feature launch comms,' or when release-notes identifies a user-facing change needing announcement."
---

# Change Communicator

## Purpose
When something ships, changes, or gets deprecated, multiple audiences need to know — each in their own terms. This skill generates audience-tailored change communications covering what changed, why, impact on their workflow, and any required action. Unlike `stakeholder-update` (which sends periodic status updates), this generates change-specific announcements.

## Dependencies

**Tools/APIs:**
- Slack API — send messages/announcements to channels and DMs
- Gmail API — draft/send email communications
- Jira API — read the change details (ticket, epic, release)
- Confluence API — read specs, publish change documentation

**Other Skills:**
- `company-context` — audience segments, stakeholder profiles, channel mappings
- `voice-analyzer` — per-audience tone and voice
- `technical-to-business-translator` — translate technical changes for non-technical audiences
- `impact-analyzer` — quantify who's affected and how (inline mode)

**Reference Files:**
- `references/change-templates.md` — communication templates by change type (launch, deprecation, migration, breaking change, rollback, maintenance)

## Inputs
- **Change** (required): what changed — Jira ticket, PR, inline description, or "the release we just shipped"
- **Change type** (auto-detected or explicit): launch, update, deprecation, migration, breaking change, rollback, maintenance window
- **Audiences** (required): who needs to know — "clubs," "engineering," "leadership," "all," or specific groups
- **Urgency** (optional): routine, important, urgent (default: routine)
- **Required action** (optional): does the audience need to do anything?

## Outputs
- Per-audience communication drafts
- Channel recommendations per audience
- Consolidated FAQ if multiple audiences have questions
- Delivery tracking (which messages sent, which pending)

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Announce** | "announce this change," "feature launch comms," "notify clubs" | Draft and send change announcements |
| **Deprecation** | "deprecation notice," "sunsetting," "end of life" | Multi-stage deprecation comms (advance notice → reminder → final) |
| **Breaking Change** | "breaking change," "migration required" | Urgent comms with clear action items and timeline |
| **Rollback** | "we rolled back," "reverted the change" | Quick damage-control comms explaining what happened |
| **Inline** | Called by `release-notes` or other skills | Return drafted comms without interactive review |

---

## Workflow

### Step 1: Understand the Change

If source is a Jira ticket/PR/release:
1. Read the source material
2. Extract: what changed, why, who's affected, technical details, timeline, any required action

If inline description:
1. Parse the user's description
2. Ask clarifying questions if needed (one at a time):
   - "Who is affected by this change?"
   - "Does anyone need to take action?"
   - "When does this take effect?"
   - "Is there a rollback plan?"

Auto-detect change type from context:

| Signal | Type |
|--------|------|
| New feature, launch keywords | Launch |
| "Removing," "deprecating," "sunsetting" | Deprecation |
| "Breaking," "must update," "migration" | Breaking Change |
| "Rolled back," "reverted" | Rollback |
| "Maintenance," "downtime," "scheduled" | Maintenance |
| Everything else | Update |

### Step 2: Identify Audiences and Channels

For each audience, determine:

| Audience | What They Need | Preferred Channel |
|----------|---------------|-------------------|
| **Clubs** | Impact on their workflow, action needed, timeline | Email + Slack channel |
| **Leadership** | Business impact, risk, timeline | Slack DM or exec channel |
| **Engineering** | Technical details, API changes, migration steps | Engineering Slack channel |
| **Partners** | API/integration changes, versioning, migration guide | Email |
| **Support** | What to tell customers, FAQ, known issues | Support Slack channel |

Check `company-context` for specific channel mappings and stakeholder profiles.

### Step 3: Draft Per Audience

For each audience, draft a communication using `references/change-templates.md`:

**Standard Structure:**

```
{Greeting appropriate to channel/audience}

**What's changing:** {1-2 sentences in their terms}

**Why:** {Brief rationale — in their priorities, not yours}

**Impact on you:** {Specific to this audience — what changes in their workflow/experience}

**Action required:** {Clear, specific — or "No action needed"}

**Timeline:** {When it takes effect, key dates}

**Questions?** {Where to go for help}
```

**Adapt by change type:**

| Type | Special Elements |
|------|-----------------|
| **Launch** | Highlight benefits, link to docs/guide, celebrate |
| **Deprecation** | Timeline with stages, migration path, support available |
| **Breaking Change** | Urgency indicator, exact action steps, deadline, consequences of inaction |
| **Rollback** | What happened, current state, what to expect next, apology if warranted |
| **Maintenance** | Window, affected services, workarounds during window |

**Tone calibration:**
- Routine changes: informational, brief
- Important changes: clear emphasis on what's different and why it matters
- Urgent changes: lead with action needed, highlight deadline, follow up

Use `voice-analyzer` for audience-specific tone. Use `technical-to-business-translator` for non-technical audiences.

### Step 4: Review Each Draft

Present each audience's communication one at a time:

```
Communication for: {Audience}
Channel: {Slack channel / Email / etc.}
Type: {change type}
Urgency: {routine / important / urgent}
---
{Draft content}
---
```

Actions per draft:
- **"Send"** — deliver via the specified channel
- **"Edit"** — modify content or tone
- **"Change channel"** — switch delivery method
- **"Add FAQ item"** — include anticipated question/answer
- **"Skip"** — don't send to this audience
- **"Next" / "Back"** — navigate between drafts

### Step 5: Send and Track

For approved messages:
1. For Slack: send the message directly (with user confirmation). For email: always create a Gmail draft — never send directly. The user reviews and sends from Gmail.
2. Log: what was sent, to whom, via which channel, when

If the change type is **Deprecation**, offer to schedule follow-up reminders:
- "Want me to create a Jira ticket with a due date for the follow-up reminder, or schedule a Slack message for {date}?"

### Step 6: Generate FAQ (Optional)

If the change is complex or affects multiple audiences:
1. Anticipate common questions based on the change type and affected audiences
2. Draft FAQ entries
3. Offer to post to Confluence or a Slack channel

### Step 7: Learn

After communications are sent:
1. Track which audiences were included for each change type
2. Note channel preferences per audience (which channels get approved vs. switched)
3. Log template variations that were approved without edits
4. Update `references/change-preferences.md`

Consolidate every 10 sessions. Keep under 1,000 words.

---

## Context Rules
- Load change source material in Step 1. Drop raw data after extracting key points.
- In Step 3, load one audience profile at a time. Drop before drafting for the next.
- Never auto-send. All messages require user approval.
- In Inline mode (called by another skill), return drafts without interactive review.
- Batch: draft for up to 5 audiences per invocation. If more, ask user to prioritize.

## Edge Cases
- **Change is purely internal:** Skip external audiences. Focus on engineering and internal team comms.
- **Change is customer-facing but minor:** Suggest lighter-touch comms: "This is a minor update. A brief Slack message to {channel} might be sufficient — do you want a full announcement?"
- **Change was already partially communicated:** Ask: "Has this been mentioned anywhere already? I don't want to send conflicting info."
- **Multiple changes at once:** Group into a single digest per audience rather than separate messages.
- **Urgent rollback:** Streamline the workflow — skip non-essential questions, prioritize speed, draft the most critical audience first.
- **Audience doesn't exist in profiles:** Ask: "I don't have a profile for {audience}. Where should I send their communication?"

## When NOT to Use
- **Periodic status updates** — use `stakeholder-update`
- **Release notes document** — use `release-notes`
- **Incident communication** — use `incident-commander`
- **Single reply to a question** — use `message-drafting`
- **Internal team announcements unrelated to product changes** — just post directly
