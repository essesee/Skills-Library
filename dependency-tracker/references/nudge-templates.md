# Nudge Message Templates

## Casual Nudge (Slack DM)

**When:** First follow-up. Blocking ticket has been stale for {stale_threshold} days.

**Template:**
```
Hey {name} — checking in on {blocking_ticket_key} ({blocking_ticket_summary}).

We're waiting on this for {your_ticket_key} — {brief reason why it matters}.

Any update on when you'll be able to get to it? Happy to jump on a quick call if it'd help unblock things.
```

**Adapt:** Keep it light. Don't sound like an automated reminder.

---

## Firm Follow-up (Slack DM or Channel)

**When:** Second follow-up. Blocking ticket at risk ({at_risk_threshold}+ days). Or previous nudge got no response.

**Template:**
```
Following up on {blocking_ticket_key} — this has been blocking {your_ticket_key} for {N} days now.

{Your_ticket_summary} is targeting {deadline/sprint}, and this dependency is the main blocker.

Can you give us an ETA, or should we explore a workaround? If priorities shifted on your end, let me know and we can figure out next steps.
```

**Adapt:** Direct but not aggressive. Focus on the impact and a specific ask.

---

## Escalation (Slack DM or Email to Leadership)

**When:** Critical threshold reached. Previous nudges haven't resolved the issue.

**Template:**
```
Flagging a cross-team dependency that's become critical.

**Blocked ticket:** {your_ticket_key} — {your_ticket_summary}
**Blocking ticket:** {blocking_ticket_key} ({blocking_team}) — {blocking_ticket_summary}
**Duration:** Blocked for {N} days
**Business impact:** {impact statement — what's delayed, who's affected, deadline risk}

**What we've tried:**
- Reached out to {assignee} on {date1} — {outcome}
- Followed up on {date2} — {outcome}

**Ask:** Can you help us get this prioritized on {blocking_team}'s side, or help identify an alternative path forward?
```

**Adapt:** Professional, factual, no blame. Lead with business impact. Make the ask specific.

---

## Meeting Talking Point

**When:** There's an upcoming sync with the blocking team.

**Template:**
```
Agenda item: {blocking_ticket_key} dependency

- Our {your_ticket_key} has been waiting on this for {N} days
- Impact: {brief impact}
- Ask: ETA or help identifying a workaround
```

---

## Tone Guidelines

- **Casual nudge:** Like asking a colleague for a favor. Friendly, assumes good intent.
- **Firm follow-up:** Like a professional reminder. Clear about the impact and the ask.
- **Escalation:** Like a project status report. Factual, concise, no emotional language.
- **Never:** Blame, passive-aggression, ultimatums, or copying people unnecessarily.
