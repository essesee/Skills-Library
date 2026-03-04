---
name: sql-data-analysis
description: "Use when the user needs to answer a data question, write or audit SQL, query PostHog events, or create dashboards. Trigger on: 'how many users,' 'what's our retention,' 'write a query for,' 'pull the data on,' 'check the numbers,' 'create a dashboard,' 'audit this SQL,' 'PostHog events,' or any business question that needs data to answer."
---

# SQL & Data Analysis

## Purpose
Handle the full loop: business question -> query -> execution -> interpretation -> follow-up.

## Dependencies

**Tools/APIs:** PostHog API (event taxonomy, HogQL, insights)
**Other Skills:** voice-analyzer (for writing up findings in user's voice)

## Inputs
- Plain-language business question, data schema, query output to interpret, or existing SQL to audit

## Outputs
- Working SQL queries tailored to the user's schema
- Plain-language summaries in user's voice
- Follow-up query recommendations (2-3 next questions)
- Visualization specs or quick charts

## Modes

### Question-to-Query
1. User asks a business question in plain language.
2. Load the relevant portion of the cached schema profile.
3. Generate working SQL, run via API, return results directly.

### PostHog Integration
Ingest event taxonomy via API (cached, refreshed weekly). Generate HogQL queries or use insights API. Run and return results directly.

### Results Interpretation
Summarize takeaways, anomalies, and trends. Write in user's voice for stakeholders. Flag anything surprising.

### Follow-Up Suggestions
After each result, recommend 2-3 next queries that go deeper (e.g., cohort breakdowns, related metrics).

### Audit Mode
Review SQL for correctness (does it answer the question?), efficiency (unnecessary joins, suboptimal patterns), and clarity (would another analyst understand?).

## Context Rules
- Cache schema profiles as files. Load only relevant tables/events per query, not the whole schema.
- Closed-loop workflow: ask -> generate -> run -> interpret -> suggest next. No copy-paste.
- One query at a time in context during the interpret step.

## When NOT to Use
- Non-data questions that don't need SQL or analytics.
- Building dashboards with complex custom visualizations — use PostHog directly.
