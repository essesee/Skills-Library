# Grooming JQL Patterns

Reference file for the backlog-groomer skill. JQL queries for all analysis dimensions. Every query includes a team filter as a base constraint.

> **Org context:** Replace `{your_team}` with your team name. If an org context skill is loaded, it provides the concrete value (e.g., `Team = "{your_team}"`).

## Base Filter

```
Team = "{your_team}"
```

This is prepended to every query. Never run grooming queries without the team filter.

## Scope Queries

### Whole Board (All Open Tickets)

```jql
Team = "{your_team}"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Specific Epic

```jql
Team = "{your_team}"
AND "Epic Link" = "{epic_key}"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Current Sprint

```jql
Team = "{your_team}"
AND sprint IN openSprints()
ORDER BY priority DESC, rank ASC
```

### Next Sprint

```jql
Team = "{your_team}"
AND sprint IN futureSprints()
ORDER BY priority DESC, rank ASC
```

### Custom JQL (User-Provided)

Prepend `Team = "{your_team}" AND` to the user's custom JQL. Example:
```jql
Team = "{your_team}" AND {user_jql}
```

## Stale Ticket Detection

### Stale Stories (30+ days, no update)

```jql
Team = "{your_team}"
AND issuetype = Story
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-30d"
ORDER BY updated ASC
```

### Stale Tasks (21+ days, no update)

```jql
Team = "{your_team}"
AND issuetype = Task
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-21d"
ORDER BY updated ASC
```

### Stale Bugs (delegated to bug-consolidator, included for reference)

```jql
Team = "{your_team}"
AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-21d"
ORDER BY updated ASC
```

### Stale Sub-tasks (14+ days, no update)

```jql
Team = "{your_team}"
AND issuetype = Sub-task
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-14d"
ORDER BY updated ASC
```

### Close Candidates (any type, 60+ days, unassigned, no links)

```jql
Team = "{your_team}"
AND status NOT IN (Closed, Done, Resolved)
AND assignee is EMPTY
AND updated <= "-60d"
ORDER BY created ASC
```

## Priority Rebalancing

### High/Critical Priority Tickets Not In Sprint

```jql
Team = "{your_team}"
AND priority IN (Critical, Highest, High)
AND sprint is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Low Priority Tickets In Current Sprint

```jql
Team = "{your_team}"
AND priority IN (Low, Lowest)
AND sprint IN openSprints()
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority ASC
```

### Unset Priority

```jql
Team = "{your_team}"
AND priority is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

## Sprint Readiness

### Current Sprint — Missing Estimates

```jql
Team = "{your_team}"
AND sprint IN openSprints()
AND (storyPoints is EMPTY OR storyPoints = 0)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC
```

### Current Sprint — Unassigned

```jql
Team = "{your_team}"
AND sprint IN openSprints()
AND assignee is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC
```

### Next Sprint — All Tickets (for readiness check)

```jql
Team = "{your_team}"
AND sprint IN futureSprints()
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, rank ASC
```

## Estimation Gaps

### All Unestimated Tickets

```jql
Team = "{your_team}"
AND (storyPoints is EMPTY OR storyPoints = 0)
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Unestimated by Epic

```jql
Team = "{your_team}"
AND "Epic Link" = "{epic_key}"
AND (storyPoints is EMPTY OR storyPoints = 0)
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created ASC
```

## Epic Health

### Tickets Without Epic

```jql
Team = "{your_team}"
AND "Epic Link" is EMPTY
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

### Tickets in a Specific Epic

```jql
Team = "{your_team}"
AND "Epic Link" = "{epic_key}"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY status ASC, priority DESC
```

### All Active Epics

```jql
Team = "{your_team}"
AND issuetype = Epic
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

## Dependency Mapping

### Blocked Tickets

```jql
Team = "{your_team}"
AND status NOT IN (Closed, Done, Resolved)
AND issueFunction in hasLinks("is blocked by")
ORDER BY priority DESC
```

Note: If `issueFunction` is not available, fetch all tickets and filter by links client-side.

### Blocking Other Tickets

```jql
Team = "{your_team}"
AND status NOT IN (Closed, Done, Resolved)
AND issueFunction in hasLinks("blocks")
ORDER BY priority DESC
```

## Scope Validation

### Vaguely Scoped Tickets (heuristic: recent, no description)

```jql
Team = "{your_team}"
AND issuetype IN (Story, Task)
AND status NOT IN (Closed, Done, Resolved)
AND description is EMPTY
ORDER BY created DESC
```

### Stories Without Acceptance Criteria (heuristic)

Use the description-empty query above, then also check for stories where description exists but doesn't contain "acceptance criteria", "AC:", "given", "when", "then", or checklist markers.

## Cross-Type Consolidation

### All Non-Bug Open Tickets (for overlap detection)

```jql
Team = "{your_team}"
AND issuetype IN (Story, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

### All Open Bugs (passed to bug-consolidator)

```jql
Team = "{your_team}"
AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

## Field Extraction

Extract these fields per ticket during ingestion (Step 2). Drop raw Jira response after extraction.

| Field | Used By |
|-------|---------|
| key | All steps |
| summary | Consolidation (4B), scope validation (7) |
| issuetype | Staleness (3), consolidation (4), estimation (8) |
| status | Staleness (3), sprint readiness (6) |
| priority | Priority rebalancing (5) |
| assignee | Staleness (3), estimation (8) |
| created | Priority rebalancing (5), staleness (3) |
| updated | Staleness (3) |
| storyPoints | Estimation (8), sprint readiness (6) |
| description | Scope validation (7), consolidation (4B) |
| labels | Priority rebalancing (5), epic health (9) |
| components | Priority rebalancing (5), epic health (9) |
| epic link | Epic health (9), consolidation (4B) |
| sprint | Sprint readiness (6), estimation (8) |
| issue links | Dependencies (10), staleness (3) |
| comment count + last comment date | Staleness (3) |
| fix versions | Report metadata |

## Batch Processing

All queries should use `startAt` and `maxResults` parameters for pagination:
- `maxResults = 20` (batch size)
- Increment `startAt` by 20 for each batch
- Continue until `total` is reached
