# Change Communication Templates

## By Change Type

### Feature Launch

**Slack:**
```
*New: {Feature Name}*

{1-2 sentence description of what it does and why it matters}

*What's new:*
- {Key capability 1}
- {Key capability 2}

*How to use it:* {brief instructions or link to docs}

Questions? Drop them in this thread.
```

**Email:**
```
Subject: New Feature: {Feature Name}

Hi {name},

We've launched {feature name} — {1 sentence value prop}.

What's New:
- {Capability with benefit}
- {Capability with benefit}

What This Means for You:
{Specific impact on their workflow}

Getting Started:
{Instructions or link}

Questions? Reply to this email or reach out in {channel}.

Best,
{name}
```

### Deprecation

**Stage 1 — Advance Notice (60-90 days out):**
```
*Heads up: {Feature/API} deprecation planned for {date}*

We're planning to retire {feature/API} on {date}. Here's what you need to know:

*Why:* {Brief rationale — better alternative, security, maintenance burden}
*What replaces it:* {New feature/API with link}
*Timeline:*
- {date}: Migration guide available
- {date}: Deprecation warnings begin
- {date}: {Feature} retired

*Action needed:* {What they need to do and by when}

We'll send reminders as the date approaches. Questions? {contact}
```

**Stage 2 — Reminder (30 days out):**
```
*Reminder: {Feature/API} retiring on {date}*

This is a reminder that {feature/API} will be retired on {date} — that's {N} days from now.

*If you haven't migrated yet:* {link to migration guide}
*Need help?* {contact}
```

**Stage 3 — Final Notice (7 days out):**
```
*Final notice: {Feature/API} retiring {date}*

{Feature/API} will be retired on {date}. After this date, {what happens}.

*If you still need to migrate:* {link} or contact {person} immediately.
```

### Breaking Change

```
*Action Required: {Change Title}*

We're making a change to {system/API} that requires action on your part.

*What's changing:* {Specific change}
*When:* {Date}
*Why:* {Brief rationale}

*What you need to do:*
1. {Specific step}
2. {Specific step}
3. {Specific step}

*Deadline:* {date} — after this, {consequence}

*Migration guide:* {link}
*Support:* {contact}
```

### Rollback

```
*Update: {Feature/Change} rolled back*

We've reverted {change} that was deployed {when}.

*What happened:* {Brief explanation — not a full postmortem}
*Current state:* {What's working now, what's reverted}
*Impact:* {Who was affected and how}
*What's next:* {Fix timeline, when to expect the change again}

We apologize for any disruption. {Contact} if you're still seeing issues.
```

### Maintenance Window

```
*Scheduled Maintenance: {date}, {time range} {timezone}*

We'll be performing maintenance on {system} during this window.

*Impact:* {What will be unavailable or degraded}
*Duration:* {Expected time}
*Workaround:* {If available, or "Please plan accordingly"}

We'll send an update when maintenance is complete.
```

### General Update

```
*Update: {Title}*

{1-2 sentence description of what changed}

*What changed:* {Details}
*Why:* {Rationale}
*Impact on you:* {Specific impact, or "No action needed"}
```

## Tone Calibration

| Urgency | Opener | Structure | CTA |
|---------|--------|-----------|-----|
| **Routine** | Informational | Brief, bullets | "No action needed" or soft CTA |
| **Important** | Clear emphasis | Structured sections | Specific action with timeline |
| **Urgent** | Lead with action | Action first, context second | Bold deadline, consequences |
