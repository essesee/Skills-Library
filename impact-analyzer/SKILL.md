---
name: impact-analyzer
description: "Quantify business impact of features, bugs, or changes using PostHog, Jira, and Slack data."
---

# Impact Analyzer

## Purpose
"How many users does this affect?" and "What's the business impact?" are among the most common information-gathering questions. This skill synthesizes quantitative data (PostHog), qualitative context (Jira, Slack), and audience-appropriate framing into a single impact statement. Saves the time of manually querying multiple sources and composing the answer.

## Dependencies

**Tools/APIs:**
- PostHog API — user counts, event volumes, error rates, funnel metrics, feature flag exposure
- Jira API — affected epics, related bugs, customer-reported issues
- Slack API — customer complaints, support volume, internal discussion context
- Circleback API — meeting discussions referencing the topic (decisions, stakeholder concerns)

**Other Skills:**
- `sql-data-analysis` — complex PostHog queries, custom HogQL
- `company-context` — persona definitions, business terminology, customer segments
- `technical-to-business-translator` — frame findings for non-technical audiences (inline mode)

**Reference Files:**
- `references/impact-framework.md` — impact dimensions, severity scales, framing templates, persona mapping

## Inputs
- **Subject** (required): feature, bug, change, or system area to assess
- **Question** (optional): specific question to answer (e.g., "how many users hit this error?" vs. "what's the overall business impact?")
- **Audience** (optional): who the impact statement is for (default: the user, can be "leadership," "clubs," "engineering")
- **Time window** (optional): assessment period (default: 30 days)

## Outputs
- Impact statement framed for the target audience
- Quantitative data: user counts, event volumes, error rates, trends
- Qualitative context: customer signals, related bugs, organizational impact
- Severity assessment: critical / high / medium / low with supporting evidence

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Full Assessment** | "what's the impact of," "impact assessment," "how big is this problem" | Complete multi-source analysis |
| **Quick Count** | "how many users," "how often does this happen" | Quantitative answer only, from PostHog |
| **Inline** | Called by another skill (incident-commander, bug-consolidator, stakeholder-update) | Return structured data, skip interactive review |

---

## Workflow

### Step 1: Define the Assessment

1. Parse what the user wants to assess. Map to queryable concepts:
   - Feature → PostHog events, feature flags, Jira epic
   - Bug → error events, affected flows, Jira ticket(s)
   - Change → before/after comparison, affected events
   - System area → all events in that flow, error rates, user volumes

2. If ambiguous, ask: "To assess the impact, I need to know: is this about a specific error, a feature's usage, or a proposed change?"

**Quick Count mode:** Skip clarifying questions. Interpret the user's query literally and pull quantitative data only. If truly ambiguous, ask one targeted question maximum.

3. Define assessment dimensions from `references/impact-framework.md`:
   - **Breadth:** How many users/sessions/transactions affected?
   - **Depth:** How severely are they affected? (broken vs. degraded vs. inconvenient)
   - **Frequency:** How often does this occur?
   - **Revenue/Business:** Does this affect revenue, bookings, or contractual SLAs?
   - **Trend:** Getting worse, stable, or improving?
   - **Persona:** Which user types are affected? (agents, members, back-office, partners)

### Step 2: Gather Data (Parallel)

#### 2A: Quantitative — PostHog

Invoke `sql-data-analysis` or query PostHog directly:

| Query Type | What |
|-----------|------|
| User count | Unique users who encountered the feature/error in the time window |
| Event volume | Total events, daily average, trend (increasing/decreasing/stable) |
| Error rate | Error events / total events for the flow, comparison to baseline |
| Funnel impact | If part of a funnel: drop-off rate at this step vs. previous period |
| Segment breakdown | Impact by user segment, club, or geography if available |

If the subject is a proposed change, pull current baseline metrics for comparison.

#### 2B: Qualitative — Jira

1. Search for bugs, stories, and support tickets related to the subject
2. Extract: how many tickets, severity distribution, customer-reported vs. internally found
3. Note: any escalations, SLA violations, or priority overrides

#### 2C: Qualitative — Slack + Circleback

1. Search Slack for customer complaints, support discussions, or internal frustration about the subject
2. Search Circleback using `SearchMeetings` by topic keywords and key participants. For matched meetings, use `ReadMeetings` to extract discussions, decisions, and action items related to the subject.
3. Extract: volume of complaints, sentiment, who's raising it, how recently

### Step 3: Synthesize

Combine all sources into a unified assessment:

1. **Quantify:** Lead with the numbers — users affected, frequency, trend
2. **Qualify:** Add context that numbers alone don't capture — customer sentiment, escalation history, strategic relevance
3. **Contextualize:** How does this compare? (% of total users, relative to other issues, historical baseline)
4. **Assess severity:**

| Severity | Criteria |
|----------|----------|
| **Critical** | >10% of users, revenue impact, getting worse, no workaround |
| **High** | 5-10% of users, significant workflow disruption, customer complaints |
| **Medium** | 1-5% of users, degraded experience, workarounds exist |
| **Low** | <1% of users, minor inconvenience, stable or improving |

### Step 4: Frame for Audience

**Quick Count (just numbers):**
```
{X} unique users affected over the last {N} days ({Y} events total).
Trend: {increasing/decreasing/stable}.
```

**Full Assessment (default):**
```
## Impact Assessment: {Subject}

### Summary
{2-3 sentence impact statement combining quant + qual}

### Severity: {Critical / High / Medium / Low}

### By the Numbers
- Users affected: {X} ({Y}% of active users)
- Occurrence: {frequency} — {trend}
- {Additional metrics relevant to the subject}

### Qualitative Signals
- Customer reports: {count and summary}
- Related Jira tickets: {count with links}
- Internal discussion: {summary of Slack/meeting signals}

### Who's Affected
- {Persona}: {how they're impacted}

### Trend
{Is this getting better or worse? Supporting data.}
```

**For leadership:** Add business impact framing (revenue, SLA, strategic risk). Use `technical-to-business-translator` inline.
**For clubs:** Frame in terms of their agents' and members' experience.
**For engineering:** Include technical metrics (error rates, latency, system health).

### Step 5: Present and Offer Actions

Present the assessment. Then offer:

| Action | When |
|--------|------|
| "Use in stakeholder update" | Pass to `stakeholder-update` |
| "Create Jira ticket" | Create a bug or story with the impact data attached |
| "Go deeper" | Drill into a specific dimension (e.g., "show me the daily trend") |
| "Compare to {other thing}" | Run a side-by-side impact comparison |
| "Different audience" | Reframe for another audience |

### Step 6: Learn

After the assessment:
1. Track which dimensions the user focused on or asked to expand
2. Note audience-specific framing that was approved without edits
3. Log: date, subject, severity assessed, audience, edits made
4. Update `references/impact-preferences.md`

Consolidate every 10 sessions. Keep under 1,000 words.

---

## Context Rules
- Run Step 2 data pulls in parallel — all sources are independent.
- After Step 2, drop raw query results. Keep only aggregated metrics and summaries.
- Quick Count mode: only run Step 2A. Skip qualitative sources.
- Inline mode: return structured data after Step 3. Skip Steps 4-5 interactive presentation.
- Never fabricate numbers. If a data source is unavailable, say so. "PostHog data not available for this query — assessment is based on Jira and Slack signals only."
- Batch limits: PostHog queries 5, Jira tickets 20, Slack messages 15.

## Edge Cases
- **No PostHog data for this feature/error:** Assess from Jira + Slack only. Clearly note: "Quantitative impact unknown — no matching PostHog events. Consider adding instrumentation."
- **Feature hasn't shipped yet (proposed change):** Assess potential impact using current baseline metrics and affected user segments. Frame as: "This would affect approximately {X} users based on current usage of {related feature}."
- **Contradictory signals:** Data says low impact but customers are vocal (or vice versa). Surface both: "PostHog shows {X} affected users (low), but {Y} customer complaints suggest high perceived impact. The gap may indicate {hypothesis}."
- **Very large impact (>50% of users):** Double-check the query. Confirm with user before presenting: "This shows {X}% of users affected — that's unusually high. Want me to verify the query before we proceed?"
- **Subject is too broad:** "The booking flow is very broad. Can you narrow it? Specific error, specific step, or specific user segment?"

## When NOT to Use
- **Writing the SQL query itself** — use `sql-data-analysis`
- **Full incident lifecycle** — use `incident-commander` (which may invoke this skill)
- **Proactive problem discovery** — use `problem-discoverer`
- **Stakeholder communication** — use `stakeholder-update` (pass impact data to it)
