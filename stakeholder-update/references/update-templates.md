# Update Templates

## Executive / Brief Format (Slack or Email)
```
{Greeting — one line, matches user's voice}

**This Week**
- {Milestone/deliverable completed}
- {Key progress on roadmap item}
- {Notable decision or change}

**Risks / Blockers**
- {Risk}: {impact} — {mitigation}

**Next Week**
- {Top 2-3 priorities}

{Sign-off — matches user's voice}
```

## Standard Format (Email)
```
Subject: {Project Name} — Weekly Update — {date}

{Opening line — context or BLUF}

## Completed
- [{JIRA-123}] {Summary} — {epic/roadmap context}
- [{JIRA-456}] {Summary}

## In Progress
- [{JIRA-789}] {Summary} — {assignee}, {status detail}

## Blockers & Risks
- [{JIRA-012}] {Summary} — Blocked by {reason}. {Mitigation or ask.}

## Roadmap Status
| Roadmap Item | Status | On Track? | Notes |
|...

## Next Week
- {Priority 1}
- {Priority 2}

{Closing — any asks, FYIs, or meeting references}
```

## Detailed Technical Format (Email)
Everything in Standard plus:
- Story-level breakdowns per epic
- Sprint metrics (velocity, points, burndown)
- Technical decisions made and rationale
- Dependency callouts
