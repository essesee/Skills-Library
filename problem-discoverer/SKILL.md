---
name: problem-discoverer
description: "Use when you need to proactively discover platform problems — monthly health checks (Full Scan), targeted area scans (Focused Scan), or validation of a specific signal (Signal Triage). Orchestrates quantitative data, bug patterns, organizational signals, structural analysis, and communication channels. Trigger on phrases like 'full scan,' 'quarterly health check,' 'what's broken,' 'platform health,' 'scan the checkout flow,' 'focused scan on search,' 'validate this signal,' 'is this a problem.'"
---

# Problem Discoverer

## Purpose
Platform problems emerge as scattered, disconnected signals. This skill orchestrates all signal sources into a ranked list of validated candidates, ready for Problem Statements. All queries scoped to your team's Jira filter.

## Dependencies

**Tools/APIs:**
- Jira — existing tickets, epics, Problem Statements
- Confluence — specs, retrospectives, incident reports
- PostHog — usage data, error rates, funnel metrics

**Other Skills (always called):**
- `sql-data-analysis` — quantitative metrics
- `bug-consolidator` — bug pattern analysis (orchestrated mode)

**Other Skills (optional — user chooses which to include):**
- `customer-signal-scanner` — external escalations (orchestrated mode)
- `team-signal-scanner` — internal pain signals (orchestrated mode)
- `vendor-signal-scanner` — vendor issues (orchestrated mode)

**Handoff:**
- `discover-design-deliver` — receives evidence packages for Problem Statements

**Reference Files:**
- `references/signal-scoring.md` — composite scoring rubric
- `references/discovery-log.md` — learning file from past sessions

## Inputs
- **Mode**: Full Scan / Focused Scan / Signal Triage
- **Focus area** (Focused Scan only): component, feature, or domain to target
- **Signal** (Signal Triage only): the specific signal to validate
- **Time window** (optional): how far back to scan (default: 90 days for Full Scan, 30 days for Focused/Triage)
- **Recent backlog-groomer output** (optional): if available, ingest for dedup baseline

## Outputs
- Ranked list of problem candidates with evidence packages
- Per-candidate: composite score, evidence summary, affected personas, trend direction, strategic alignment
- Disposition log: decisions made on each candidate
- Handoff packages for candidates promoted to Problem Statements

## Modes

| Mode | Trigger | Behavior | Default Window |
|------|---------|----------|----------------|
| **Full Scan** | "full scan," "quarterly health check," "what problems should we work on" | All signal sources, comprehensive ranked list | 90 days |
| **Focused Scan** | "scan the checkout flow," "problems in the audit system" | Same workflow scoped to a feature area/domain | 30 days |
| **Signal Triage** | "validate this signal," "is this a problem," "I heard X is broken" | Validate a specific user-provided signal | 30 days |

## Workflow

### Step 1: Configure and Load References
- Confirm mode and scope with user
- Load `references/signal-scoring.md` and `references/discovery-log.md`
- Set time window (use defaults or user override)
- For Focused Scan: confirm target area and relevant components/channels
- For Signal Triage: capture the initial signal and formulate validation queries

### Step 2: Gather Dedup Baseline (Known Work)
Before scanning for new signals, establish what's already known:
- Search Jira for existing Problem Statements (open and recently closed)
- Search Jira for in-progress epics and their associated problems
- If recent `backlog-groomer` output is available, ingest its findings
- Search Confluence for recent discovery docs, retrospectives, and incident reports
- Build a "known issues" list to dedup against in Step 4

This prevents surfacing problems that are already being worked on.

### Step 3: Gather Signals (Parallel)

All 5 categories are independent — run concurrently. For each, extract signal summaries then drop raw data.

| Category | Sources | What to extract |
|----------|---------|-----------------|
| **3a. Quantitative** | `sql-data-analysis` + PostHog | Error rates, latency trends, funnel drop-offs, usage anomalies, spikes vs. baseline |
| **3b. Bug patterns** | `bug-consolidator` (orchestrated mode) | Clusters by root cause, recurring patterns, severity distribution, systemic indicators |
| **3c. Organizational** | Jira (workarounds, reopened 3x+, stale 6mo+, high-comment), Confluence (recurring retro items, incomplete postmortems) | Unformalized pain patterns |
| **3d. Communication** (optional) | `customer-signal-scanner`, `team-signal-scanner`, `vendor-signal-scanner` (all orchestrated mode) | Voice signals corroborating or adding to data-visible problems |

### Step 4: Synthesize
Merge and analyze all signals from Step 3:

1. **Deduplicate against baseline**: Remove signals that match known work from Step 2
2. **Cluster related signals**: Group signals that point to the same underlying problem (e.g., customer complaints + error spike + bug cluster all pointing to checkout flow reliability)
3. **Score each cluster**: Apply composite formula from `references/signal-scoring.md` (6 weighted factors: breadth, depth, persona impact, strategic alignment, trend, actionability)
4. **Sharpen framing**: For top 3-5 candidates (or any scoring 3.5+), stress-test using first principles, inversion, and second-order thinking
5. **Resolve ambiguity**: For candidates with contradictory signals across categories, surface assumptions explicitly and present both perspectives

### Step 5: Present Ranked Candidates
Present each problem candidate, ranked by composite score:

Per candidate:
- **Problem summary**: 1-2 sentence description of the underlying problem
- **Composite score**: numeric score with factor breakdown
- **Evidence summary**: which sources contributed, key data points
- **Affected personas**: End Users, Support Team, Back Office, Engineering Teams
- **Trend**: getting worse / stable / improving (with supporting data)
- **Strategic alignment**: connection to platform strategy
- **Similar known issues**: any partial overlap with Step 2 baseline

For Signal Triage mode: present validation results for the specific signal — confirmed/partially confirmed/not confirmed, with supporting and contradicting evidence.

### Step 6: One-at-a-Time Review
Walk through each candidate with the user.

| Disposition | Action |
|-------------|--------|
| **Write Problem Statement** | Package evidence, hand off to `discover-design-deliver` |
| **Investigate More** | Note what additional evidence is needed |
| **Merge** | Combine with another candidate's evidence |
| **Already Known** | Link to existing Jira item from Step 2 baseline |
| **Not a Problem** | Dismiss with reasoning. Log to discovery-log |
| **Defer** | Log to discovery-log with revisit date |

Navigation commands: Next / Back / Jump to # / Show summary / Done / Remaining count

### Step 7: Handoff and Log
For each candidate marked "Write Problem Statement":
1. Compile evidence package: signal summaries, data points, persona impact, trend data
2. Hand off to `discover-design-deliver` in "Write Problem Statement" mode with pre-compiled evidence package
3. `discover-design-deliver` can skip its own context gathering for covered areas and focus on structuring the Problem Statement

Update `references/discovery-log.md`:
- Record all dispositions (promoted, dismissed, deferred, merged)
- Note which signal sources were most/least valuable
- Track deferred items for future revisit
- Consolidate log every 5 sessions (keep under 2,000 words)

## Context Rules
- Load `references/signal-scoring.md` and `references/discovery-log.md` once at Step 1. Do not reload mid-workflow.
- Run Step 3 source categories in parallel. Drop raw data after extracting signal summaries.
- Scanner skills in orchestrated mode return structured data — no interactive review within scanners.
- Call `bug-consolidator` with `called_by_problem_discoverer = true` — structured cluster data, no interactive review.
- During Step 6 review, keep only the current candidate and its evidence in context.
- Update discovery log at session end only, not during workflow.
- Do NOT invoke `backlog-groomer` — too heavy. Ingest recent output passively if available.
- Dedup is critical — the biggest risk is surfacing already-known problems. Step 2 baseline must be thorough.
- Communication scanners (3d) are optional — ask user which signal sources to include at Step 1.

## Edge Cases
- **No significant signals found**: Report a clean bill of health. Note which sources were checked and the time window. This is a valid and useful outcome.
- **Too many candidates (15+)**: Present top 10 by score. Offer to continue with remaining candidates or defer lower-ranked ones.
- **Conflicting signals**: When quantitative data says "fine" but voice signals say "broken" (or vice versa), flag the contradiction explicitly. Surface assumptions and present both perspectives.
- **Stale discovery log**: If the log references problems from 6+ months ago with no resolution, surface them as "previously identified, still unresolved" candidates.
- **Scanner skill unavailable**: If a scanner skill fails or returns no results, note the gap in coverage and proceed with available sources. Don't block the scan.
- **Signal Triage finds nothing**: If the user's signal can't be validated, say so clearly. "We checked X, Y, Z sources and found no corroborating evidence. This may be an isolated incident or the signal may be too early to detect in our data."

## When NOT to Use
- **Single bug investigation** — use `bug-consolidator`
- **Backlog grooming** — use `backlog-groomer`
- **Problem already clear, need Problem Statement** — use `discover-design-deliver`
- **Specific data question** — use `sql-data-analysis`
- **Specific customer complaint** — use `customer-signal-scanner`
