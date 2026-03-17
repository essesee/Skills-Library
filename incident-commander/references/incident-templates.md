# Incident Templates

## Severity Matrix

| Severity | User Impact | Scope | Revenue | Workaround | Response |
|----------|-----------|-------|---------|------------|----------|
| **P0 — Critical** | Core function down | >25% users | Direct revenue loss | None | All hands, immediate |
| **P1 — Major** | Significant degradation | 5-25% users | Indirect revenue risk | Painful | Team priority, same day |
| **P2 — Minor** | Edge case, cosmetic | <5% users | Negligible | Easy | Normal priority |

## Communication Templates

### Initial Notification — Clubs/External

```
We're currently investigating an issue affecting {affected functionality}.

**What's happening:** {user-visible symptoms in their terms}
**Impact:** {who's affected and how}
**Workaround:** {if available, or "We're working on a fix"}
**ETA:** {estimated resolution time, or "We'll update you within {timeframe}"}

We'll keep you updated as we learn more. For urgent needs, contact {support contact}.
```

### Initial Notification — Leadership

```
**[P{severity}] {Brief title}**

- **Impact:** {X users/transactions affected}, {revenue/SLA implications}
- **Status:** {Investigating / Root cause identified / Fix in progress}
- **Team:** {who's working on it}
- **ETA:** {estimate or "assessing"}
- **Business risk:** {specific risk statement}

Will update in {timeframe}.
```

### Initial Notification — Engineering

```
**[P{severity} Incident] {Technical title}**

- **Affected systems:** {service/component list}
- **Symptoms:** {error messages, metrics anomalies}
- **Suspected cause:** {if known, or "investigating"}
- **Timeline:** Started ~{time}, detected at {time}
- **Current actions:** {what's being done}

Incident channel: {channel link}
```

### Update Template (Any Audience)

```
**Update on {incident title} — {timestamp}**

**Status:** {Investigating → Identified → Fixing → Monitoring → Resolved}
**Change since last update:** {what's new}
**Next steps:** {what's happening now}
**ETA:** {updated estimate}
```

### Resolution Notification — Clubs/External

```
The issue affecting {functionality} has been resolved.

**What happened:** {brief, non-technical explanation}
**Duration:** {start to end}
**Current status:** Fully operational
**What we're doing:** {preventive measures, if appropriate to share}

We apologize for any inconvenience. If you're still experiencing issues, contact {support contact}.
```

## Postmortem Template

```
# Incident Postmortem: {Title}

| Field | Value |
|-------|-------|
| **Date** | {YYYY-MM-DD} |
| **Severity** | P{n} |
| **Duration** | {start time} to {end time} ({total}) |
| **Impact** | {users affected}, {transactions affected}, {revenue impact if known} |
| **Detection** | {how it was detected: alert, user report, monitoring} |
| **Resolution** | {what fixed it: rollback, hotfix, config change} |

## Summary
{2-3 paragraph narrative of what happened, written for an informed technical audience}

## Timeline
| Time | Event | Source |
|------|-------|--------|
| {HH:MM} | {event description} | {Slack/deploy/alert/etc.} |

## Root Cause
{Technical root cause analysis. Be specific.}

## Contributing Factors
{What made this worse, harder to detect, or harder to fix}
- {Factor 1}
- {Factor 2}

## What Went Well
- {Thing that worked during response}

## What Could Be Improved
- {Process, tooling, or system improvement}

## Action Items
| # | Action | Owner | Priority | Due | Jira |
|---|--------|-------|----------|-----|------|
| 1 | {specific action} | {person} | P{n} | {date} | {to create} |

## Lessons Learned
{Key takeaway for the team}
```

## Jira Incident Ticket Template

```
Summary: [P{severity}] {Brief description}
Type: Bug (or Incident if available)
Priority: {maps from severity}
Labels: incident, P{severity}
Description:
  ## Incident Summary
  {What happened}

  ## Impact
  {Who was affected and how}

  ## Timeline
  {Key events}

  ## Resolution
  {What fixed it}

  ## Postmortem
  {Link to Confluence postmortem when available}
```
