---
name: backlog-groomer
description: "Systematic backlog health check — groom tickets, prep for sprint planning, identify stale or misprioritized items."
---

# Backlog Groomer

## Purpose
Systematic health check across your team's board — all ticket types, not just bugs — producing actionable recommendations with a one-at-a-time review workflow. All queries scoped to your team's Jira filter (set by org context skill; see `grooming-jql-patterns.md`).

## Dependencies

**Tools/APIs:**
- Jira — read/write tickets, epics, sprints

**Other Skills:**
- `bug-consolidator` — bug-specific clustering, stale detection, duplicates (orchestrated mode)
- `jira-template-builder` — templates and epic/roadmap mapping rules

**Reference Files:**
- `references/grooming-jql-patterns.md` — JQL queries and field extraction list
- `references/sprint-readiness-checklist.md` — readiness criteria by ticket type
- `references/staleness-thresholds.md` — configurable stale thresholds by type/status
- `references/priority-scoring-rubric.md` — priority rebalancing weights
- `references/grooming-report-template.md` — output report structure

## Inputs
- **Scope** (required): Whole board / specific epic(s) / current or next sprint / custom JQL
- **Focus** (optional, default: all):

| Focus | Runs Step(s) |
|-------|-------------|
| `stale` | 3 |
| `sprint-readiness` | 6 |
| `priorities` | 5 |
| `consolidation` | 4 |
| `estimation` | 8 |
| `epic-health` | 9 |
| `dependencies` | 10 |
| `scope-validation` | 7 |

- **Staleness overrides** (optional): Custom thresholds per `staleness-thresholds.md` format

## Outputs
- Comprehensive grooming report (per `grooming-report-template.md`)
- One-at-a-time action review with approve/deny/edit per recommendation
- Before/after impact summary

## Workflow

### Step 1: Configure Scope
- Ask user for scope and focus (or accept defaults: whole board, all dimensions)
- Confirm configuration before proceeding
- If user specifies a focus, skip non-relevant steps

### Step 2: Ingest Tickets (Batched)
Pull tickets matching scope via JQL (from `grooming-jql-patterns.md`) in batches of 20. Extract fields per `grooming-jql-patterns.md > Field Extraction`. Drop raw data after extraction.

During ingestion, incrementally build:
- Epic membership map (epic -> ticket keys)
- Dependency graph (ticket -> blocks/blocked-by)

### Step 3: Stale Ticket Detection
Compare each ticket against thresholds and classification logic from `staleness-thresholds.md`. Group stale tickets by classification (close candidate / needs update / reprioritize), then by type and status.

### Step 4: Cross-Type Consolidation

#### Step 4A: Bug Consolidation (Delegated)
Invoke **bug-consolidator** in orchestrated mode (`called_by_backlog_groomer = true`):
- Pass: all bugs from the ingested dataset
- Receive: stale bug list, duplicate pairs, clusters with priority scores, cross-team flags
- Integrate results into the grooming report
- Do not hold bug-consolidator's internal context — only its structured output

#### Step 4B: Story/Task Overlap Detection
For non-bug tickets, detect:
- **Duplicates**: Stories/tasks with highly similar summaries within the same epic
- **Subsets**: One ticket's scope is entirely contained within another's
- **Conflicting**: Tickets with contradictory acceptance criteria or goals
- **Fragmented**: Multiple small tickets that should be a single story

Compare summaries within and across epics. Flag overlaps with confidence level.

### Step 5: Priority Rebalancing
Score each non-bug ticket using the 6-dimension weighted formula in `references/priority-scoring-rubric.md`. Flag tickets where computed priority significantly diverges from Jira priority (over-prioritized or under-prioritized).

### Step 6: Sprint Readiness Assessment
Evaluate tickets in current/next sprint against criteria from `sprint-readiness-checklist.md`. Score each ticket:

- **Ready**: Meets all criteria for its type
- **Almost Ready**: Missing 1-2 non-critical items
- **Not Ready**: Missing critical items (e.g., no AC, no estimate, unassigned)

Output a sprint readiness scorecard: percentage Ready / Almost Ready / Not Ready, with specific gaps per ticket.

### Step 7: Scope Validation
Flag tickets that have quality issues:
- **Stale + unworked**: In backlog for extended period with no activity
- **Vaguely scoped**: Summary or description lacks specificity (heuristic: < 20 words in description, no AC)
- **Missing acceptance criteria**: Stories without AC defined
- **Wrong type**: Tasks that read like bugs, stories that read like tasks, etc.

### Step 8: Estimation Gaps
Find tickets missing story points or estimates. Group by:
- Epic (which epics have the worst estimation coverage?)
- Assignee (who has the most unestimated work?)
- Sprint (are sprint tickets estimated?)

### Step 9: Epic Health Check
For each active epic:
- **Ticket counts**: Total, by status, by type
- **Staleness**: Percentage of stale tickets in the epic
- **Unlinked tickets**: Tickets that probably belong to this epic but aren't linked (based on labels, components, summary keywords)
- **Overloaded epics**: Epics with 20+ open tickets
- **Empty epics**: Epics with 0 open tickets (close candidates)
- **Estimation coverage**: Percentage of tickets with story points

### Step 10: Dependency Mapping
Analyze the dependency graph built during ingestion:
- **Blocked chains**: Tickets blocked by other blocked tickets (chain depth)
- **Circular dependencies**: A blocks B blocks A (immediate flag)
- **External blockers**: Tickets blocked by items outside your team's scope
- **Critical path**: Longest dependency chain affecting sprint goals

### Step 11: Generate Grooming Report
Compile all findings using `grooming-report-template.md`. Structure:
1. Executive summary (key metrics, top concerns)
2. Section per analysis dimension (only dimensions that were run)
3. Prioritized action list (sorted by impact)
4. Appendix: full ticket lists per category

Present the report overview to the user before entering review mode.

### Step 12: One-at-a-Time Action Review
Walk through recommended actions sorted by estimated impact (highest first). For each action:
- Show: what the action is, which ticket(s) it affects, why it's recommended
- Options: **Approve** / **Deny** / **Edit** (modify before applying)
- Commands: **Next** / **Back** / **Jump to #** / **Show report** / **Done** (stop review) / **Remaining** (count of unreviewed)

For Jira write actions (close, move, update priority, merge), execute on Approve. For advisory actions (flag for estimation, draft AC), note as recommendations in the summary.

### Step 13: Impact Summary
After review, show before/after metrics for all dimensions that had approved actions. Include net change in open ticket count.

## Context Rules
- Load all reference files at Step 1. Do not reload mid-workflow.
- Batch ingest at 20 tickets per batch. Drop raw data after attribute extraction.
- Pass bug subset to bug-consolidator; keep only its structured output.
- During Step 12 review, hold only the current recommendation. Load next on navigation.
- Build epic membership map and dependency graph incrementally during Step 2 ingestion.
- Compile the report from extracted attributes and analysis results, not from re-reading tickets.

## Edge Cases
- **Empty scope**: No tickets match the query. Report this immediately, suggest broadening scope.
- **Massive backlog (500+ tickets)**: Warn user. Offer to focus on a subset (current sprint, specific epic, last 90 days, specific type). Process in extended batches if user confirms full scan.
- **No epics configured**: Skip Step 9. Note in report that epic structure is missing.
- **Bug-consolidator unavailable**: Skip Step 4A. Note in report. Perform basic bug staleness check inline but skip clustering and duplicate detection.
- **Mixed Jira field configs**: Some tickets may lack fields (no story points field, no components). Handle gracefully — score based on available fields, flag gaps.
- **Write permission failures**: If Jira write fails during review, fall back to report-only mode. Log the failed action and continue with remaining items.
- **Circular dependencies detected**: Flag immediately in Step 10. Do not attempt to auto-resolve — present to user with full chain.
- **Tickets in active sprint**: Flag but do not auto-modify without explicit user confirmation per ticket.

## When NOT to Use
- **Single bug triage** — use `atlassian:triage-issue`
- **Creating tickets from meetings** — use `ticket-proposer`
- **Building Jira templates** — use `jira-template-builder`
- **Bug-only cleanup** — use `bug-consolidator`
- **Status reporting** — use `atlassian:generate-status-report`
- **Sprint velocity/metrics** — use `sql-data-analysis` with PostHog or Jira data
