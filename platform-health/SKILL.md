---
name: platform-health
description: "Check platform product health — insurance attachment rates by product/funnel/price, manual booking trends, ticket consolidation candidates, self-service opportunities, and metric anomalies."
---

# Platform Health

## Purpose
Business intelligence health report for the platform team's product areas (insurance, manual bookings). Not just "did anything break" — this is product-level intelligence that informs strategic decisions about insurance coverage, attachment rates, self-service opportunities, and ticket patterns.

## Dependencies

**Tools/APIs:**
- PostHog — insurance attachment rates, booking metrics, error tracking, funnel analysis (primary metrics source)
- Jira API — ticket inflow, issue search, epic/story queries for platform queues
- `bug-consolidator` skill — ticket clustering logic for identifying consolidation candidates and self-service opportunities

**Reference Files:**
- `references/metric-definitions.md` — canonical metric definitions, segmentation dimensions, and anomaly thresholds

## Inputs
- **Time window** (optional): comparison period (default: WoW with YoY overlay)
- **Focus area** (optional): narrow to insurance-only or manual-bookings-only (default: both)
- **Orchestrated mode flag** (optional): `called_by_morning = true` — returns structured data, defers interactive presentation to the orchestrator. Default: `false`

## Outputs
Three-layer health report with interactive drill-down.

**Standalone mode** (default): Interactive presentation per layer — present findings, offer drill-in or move-on.
**Orchestrated mode** (`called_by_morning = true`): Return structured data for `/morning` to present. Skip Phase 3.

---

## Workflow

### Phase 1: Gather (Parallel)
Run all data pulls simultaneously. Each source is independent.

#### 1A: Metrics Pull
1. Query PostHog for insurance-related events in the configured time window
2. Calculate attachment rates segmented by:
   - Product type (car, hotel, cruise, tour, air)
   - Funnel (agent console, consumer e-site, specific club)
   - Price tier (<$500, $500-$2000, $2000+)
3. Pull comparison data for WoW calculation (same period last week)
4. Pull comparison data for YoY calculation (same period last year)
5. Identify coverage gaps — product combinations where insurance is not offered in any funnel
6. Pull manual booking volume, segmented by product type
7. Calculate manual booking WoW and YoY trends
8. Batch limit: process in groups of 5 metric queries. Drop raw event data after aggregation.

#### 1B: Issue Pull
1. JQL search for recent platform tickets in your Jira project (last 7 days default, configurable via time window)
2. Filter and tag tickets from known creators:
   - Mindy Bonney (UC8E8HM5W)
   - Dawn
   - Other configured creators
3. Invoke `bug-consolidator` clustering logic on the recent ticket set to identify:
   - Groups of similar tickets (consolidation candidates)
   - Recurring patterns across tickets
4. Score each cluster for self-service potential:
   - Recurring pattern: 3+ similar tickets in 30 days
   - User-facing resolution: not a code fix, could be addressed by documentation or tooling
   - Could be addressed by self-service UI, FAQ, or better error messaging
5. Batch limit: 50 tickets max per pull. Process clusters in groups of 10.

#### 1C: Anomaly Scan
1. Load anomaly thresholds from `references/metric-definitions.md`
2. Compare current insurance metrics against thresholds:
   - Significant change: >10% WoW deviation from trailing 4-week average
   - Spike: >25% single-day deviation from 7-day average
   - Trend reversal: 3+ consecutive weeks of directional change
3. Check PostHog error tracking for new or spiking errors in platform areas (insurance flows, manual booking flows)
4. Pull funnel analysis for key flows — flag any stage with >5% drop-off increase WoW
5. Flag any metric crossing a threshold with magnitude, direction, and time of onset

### Phase 2: Analyze
1. Rank metrics findings by magnitude of change (largest deviation from expected first)
2. Rank issue clusters by size (most tickets) and self-service potential score
3. Rank anomalies by severity: spike > trend reversal > significant change
4. Cross-reference: do any anomalies correlate with issue clusters? (e.g., error spike in insurance flow correlates with ticket inflow about insurance failures)
5. Drop all raw query results after analysis. Keep only analyzed findings with supporting numbers.

### Phase 3: Present (Interactive)
Skip entirely if `called_by_morning = true` — return structured findings to orchestrator.

Present each layer in order. Per layer, show findings and offer actions:
- **Drill in** — expand detail on a specific finding (load additional context for that finding only)
- **Move on** — proceed to next layer
- **Investigate** — pull additional context: related Jira tickets, PostHog deep-dive, Slack discussions about the topic

#### Layer 1: Metrics Health
```
## Metrics Health — {date range}

Insurance attachment: {rate}% ({+/-change}% WoW, {+/-change}% YoY)
  By product: car {rate}%, hotel {rate}%, cruise {rate}%, tour {rate}%, air {rate}%
  By funnel: agent console {rate}%, consumer e-site {rate}%, {club} {rate}%
  By price tier: <$500 {rate}%, $500-$2k {rate}%, $2k+ {rate}%

Coverage gaps: {count} product combos without insurance
  {list of gaps}

Manual bookings: {volume} total ({+/-change}% WoW, {+/-change}% YoY)
  By product: {breakdown}

[Drill in] [Move on]
```

#### Layer 2: Issue Health
```
## Issue Health — Last {N} Days

New tickets: {count} ({change from prior period})
Top creators: {name}: {count}, {name}: {count}, ...

Clusters found: {count} groups
  Consolidation candidates: {count} clusters with {total tickets} tickets
  Self-service candidates: {count} patterns
    - {cluster description}: {ticket count} tickets, {pattern summary}
    - {cluster description}: {ticket count} tickets, {pattern summary}

[Drill in] [Move on]
```

#### Layer 3: Anomalies
```
## Anomalies

{If anomalies found:}
[SPIKE] {metric}: {description} ({magnitude}, since {onset})
  Context: {what might explain this, correlated findings}
[TREND] {metric}: {description} ({direction} for {duration})
[CHANGE] {metric}: {description} ({magnitude} vs 4-week average)

{If no anomalies:}
All metrics within normal range. No anomalies detected.

[Drill in] [Done]
```

---

## Configuration
- Known ticket creators: Mindy, Dawn (configurable)
- Platform Jira project keys: YOUR-PROJECT (configurable)
- Anomaly thresholds: defined in `references/metric-definitions.md`
- Default time window: WoW with YoY overlay
- Default focus: both insurance and manual bookings

## Edge Cases
- **PostHog unavailable:** Skip metrics layer and anomaly layer. Note gap in output. Present issue layer only.
- **No recent tickets:** Report "No new platform tickets in the last {N} days." Not an error.
- **No anomalies:** Report "All metrics within normal range." Not an error.
- **First run (no historical baseline):** Present current values without trend comparisons. Note: "Trends will appear after 2+ runs with historical data available in PostHog."
- **bug-consolidator unavailable:** Present raw ticket list without clustering. Note: "Clustering unavailable — showing unclustered ticket inflow."
- **Jira unavailable:** Skip issue layer. Note gap in output. Present metrics and anomaly layers only.
- **High volume (100+ tickets):** Present top 20 clusters by size. Offer to continue with remaining.

## Context Rules
- Run Phase 1 data pulls in parallel — each source is independent.
- After Phase 2, drop all raw query results. Keep only analyzed findings with supporting numbers.
- During Phase 3 drill-in, load only the specific finding's detail. Drop other context before loading.
- In orchestrated mode (`called_by_morning = true`), return structured data and skip Phase 3 entirely.
- Metrics pull batch limit: 5 queries at a time.
- Issue pull batch limit: 50 tickets, clusters processed in groups of 10.
- Do not cache metrics across sessions — always pull fresh data.

## When NOT to Use
- **Checking a single Jira ticket** — use Jira tools directly
- **Investigating a specific error** — use `posthog:errors`
- **Triaging a bug report** — use `atlassian:triage-issue`
- **Full platform problem discovery** — use `problem-discoverer` (which may invoke this skill)
- **Grooming the backlog** — use `backlog-groomer`
