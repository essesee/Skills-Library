---
name: competitive-intel-scanner
description: "Monitor travel tech landscape, check competitor activity, maintain living competitive positioning. Triggers: 'how do we compare', 'competitor X', 'competitive landscape', 'market positioning', 'who are our competitors', 'advisory prep', 'roadmap strategy', or any mention of a specific competitor name."
---

# Competitive Intel Scanner

## Purpose
Strategic awareness requires staying current on the travel technology landscape — competitor moves, industry trends, emerging technologies, and market shifts. This skill scans web sources, synthesizes findings, and produces digests with "so what" framing for your platform business.

## Dependencies

**Tools/APIs:**
- WebSearch — search for travel technology news, competitor announcements, industry reports
- WebFetch — read articles and reports found via search
- Slack API — search internal channels for competitor/industry mentions
- Confluence API — read/write competitive intelligence pages

**Other Skills:**
- `company-context` — your strategic focus areas, product positioning, competitive context
- `message-drafting` — for the "Share via Slack" action in Step 5 (optional)

**Reference Files:**
- `references/competitive-config.md` — competitor list, focus areas, news sources, scan cadence
- `references/scan-history.md` — findings history, session log, trend tracking
- `references/competitive-landscape.md` — living competitive positioning document, updated incrementally

## Inputs
- **Mode** (optional): "scan" (default), "update", or "landscape"
- **Focus** (optional): specific competitor, technology, or market segment (default: broad scan)
- **Depth** (optional): "quick" (headline scan) or "deep" (full analysis, default). Applies to scan mode.
- **Time window** (optional): how far back to scan (default: 30 days). Applies to scan mode.

## Outputs
- **Scan mode:** Competitive intelligence digest with "so what" framing, categorized findings
- **Update mode:** Updated `references/competitive-landscape.md` with new data point and implications
- **Landscape mode:** Current competitive positioning summary with roadmap cross-references
- Optional: Confluence page with digest or landscape summary

---

## Workflow

All modes load `references/competitive-landscape.md` at start. This is the living competitive positioning document. All modes update it at end with any new findings.

### Mode: Update (Lightweight)

For incremental updates when you learn something new. No web scanning required.

1. Load `references/competitive-landscape.md`
2. Accept the new data point: article, competitor announcement, meeting note, or observation
3. If a URL is provided, WebFetch to read the article
4. Determine what changed: new competitor, existing competitor move, market trend, positioning shift
5. Update the relevant section of competitive-landscape.md:
   - New competitor → add a Competitor Profile entry
   - Existing competitor move → update their "Recent moves" and "Last updated"
   - Market trend → update Trend Lines
   - Positioning shift → update the Positioning Dimensions table
6. Frame the "so what": does this affect any active Jira epics or roadmap items? Cross-reference against the Google Drive roadmap cache (from `/morning`) if available.
7. If the update reveals a gap or opportunity, note it in Open Questions
8. Present a brief summary: what changed, what it means, what (if anything) to do about it

### Mode: Landscape (Read Only)

Surface the current competitive positioning for planning, advisory prep, or roadmap discussions.

1. Load `references/competitive-landscape.md`
2. Present the current state: positioning dimensions, competitor profiles, trend lines, open questions
3. Cross-reference against active Jira epics and roadmap: where are you ahead, behind, or unaddressed?
4. If the landscape file is thin or stale (>60 days since last update), suggest running a full scan to refresh

### Mode: Scan (Default — Full Workflow Below)

### Step 1: Configure Scan

1. Load `references/competitive-config.md` — competitor list, focus areas, past scan dates
2. Load `references/competitive-landscape.md` — prior positioning context so the scan builds on existing knowledge
3. If first run, ask:
   - "Key competitors to track?"
   - "Focus areas? (GDS integration, travel syndication, partner organization tech, booking engines, etc.)"
   - "Any specific companies or technologies you're watching?"
3. Determine scan scope: broad vs. focused

### Step 2: Gather Intelligence (Parallel)

#### 2A: Competitor Activity

For each tracked competitor:
1. WebSearch: `"{competitor name}" travel technology {year}` + recent news
2. WebSearch: `"{competitor name}" announcement OR launch OR partnership OR acquisition`
3. Also use search keywords from `references/competitive-config.md` in addition to default queries
4. For top 3-5 results per competitor: WebFetch to read full article, extract key points

Extract per finding:
- What happened (factual summary)
- When (date)
- Source (publication/URL)
- Relevance to your business (high/medium/low)

#### 2B: Industry Trends

Search for broader travel tech trends:
1. `travel technology trends {year}`
2. `travel syndication platform {year}`
3. `GDS technology evolution {year}`
4. `digital transformation trends`
5. Custom searches based on focus areas from config

#### 2C: Technology Shifts

Search for technology changes relevant to your stack:
1. Emerging technologies in travel booking
2. API standards and integration patterns
3. Platform/marketplace models in travel
4. AI/ML applications in travel tech

#### 2D: Internal Signals (Optional)

Search Slack for internal competitor/industry mentions:
1. Keywords from competitor names and focus areas
2. Shared articles or industry discussions
3. Customer mentions of competitors

### Step 3: Synthesize and Frame

**Filter:** Remove noise. Keep only findings with relevance to your business.

**Categorize:**

| Category | What Goes Here |
|----------|---------------|
| **Competitor Moves** | Product launches, partnerships, acquisitions, pricing changes, market expansion |
| **Industry Trends** | Market direction, customer behavior shifts, regulatory changes, adoption patterns |
| **Technology Shifts** | New standards, platform evolution, integration patterns, emerging capabilities |
| **Opportunities** | Gaps competitors aren't addressing, market needs you could fill, partnership possibilities |
| **Threats** | Competitive encroachment, market shifts away from your model, technology obsolescence risks |

**"So What" Framing:** For each finding, answer:
- What does this mean for your business specifically?
- Should we respond, watch, or ignore?
- Does this validate or challenge our current strategy?

### Step 4: Generate Digest

```
## Competitive Intelligence Digest — {date range}

### Executive Summary
{3-5 sentences: most important takeaways and strategic implications}

### Competitor Moves
{Per competitor with activity:}
#### {Competitor Name}
- **What:** {finding}
- **So What:** {implication for your business}
- **Recommended Response:** {watch / investigate / respond / ignore}

### Industry Trends
{Trend-level findings with implications}

### Technology Shifts
{Technology changes relevant to your platform}

### Opportunities
{Market gaps, unaddressed needs, partnership possibilities}

### Threats
{Competitive or market risks to monitor}

### Recommended Actions
1. {Specific action with rationale}
2. {Action}
3. {Action}
```

**Quick scan:** Executive summary only. 5-10 bullet points.
**Deep scan:** Full digest with all sections.

### Step 5: Review and Publish

Present the digest. Actions:
- **"Approve"** — done, optionally publish to Confluence
- **"Go deeper on {competitor/topic}"** — additional research on specific area
- **"Publish to Confluence"** — write to competitive intel page
- **"Share via Slack"** — post summary to a channel
- **"Edit"** — modify findings or framing
- **"Add finding"** — include something the user knows that wasn't found in the scan

### Step 6: Learn

Update `references/competitive-config.md`:
- Adjust competitor tracking based on which had activity
- Add new competitors or focus areas discovered
- Log scan date and key findings for trend tracking
- Track which findings the user acted on (signal of value)

Update `references/competitive-landscape.md`:
- Add or update Competitor Profiles with new findings
- Update Positioning Dimensions table if competitive gaps shifted
- Add new Trend Lines from scan patterns
- Remove resolved Open Questions, add new ones
- Update "Last Updated" date

---

## Context Rules
- Run Step 2 searches in parallel. Each source category is independent.
- WebFetch articles: read first 2,000 words max. Extract key points, drop raw content.
- Limit: 5 articles per competitor, 10 industry articles total.
- After Step 2, drop raw search results and article content. Keep only extracted findings.
- Quick scan: skip Steps 2C (technology) and 2D (internal). Headline-level only.
- Load config file once at Step 1. Update at Step 6 only. Do not reload mid-workflow.
- During "go deeper" in Step 5, retain only the digest summary and the specific competitor/topic being deepened. Drop other raw findings.
- Slack internal signal search (Step 2D): batch limit 20 messages.
- Confluence publish requires user approval.

## Edge Cases
- **No significant findings:** "Quiet period — no major competitor moves or industry shifts in the last {N} days. Notable absence: {what you'd expect to see but didn't}."
- **Paywall content:** Note the finding from the headline/snippet. Flag: "Full article behind paywall — summary based on available excerpt."
- **Unverified claims:** Flag: "Source reliability: unverified. This appeared in {source type} and hasn't been corroborated."
- **Stale config (no scan in 90+ days):** "It's been {N} days since the last scan. Running a broader search to catch up."
- **Overwhelming volume:** Prioritize by relevance to your business. Present top 10, offer to expand.
- **User provides a specific article/link:** Read it, analyze it, add to the digest with context.
- **WebSearch or WebFetch failures:** Note the gap in coverage. Fall back to internal signals (Slack mentions, config history) and present what's available. Do not block the entire scan for a single source failure.

## When NOT to Use
- **Internal platform health** — use `problem-discoverer`
- **Customer feedback analysis** — use `customer-signal-scanner`
- **Market sizing or financial analysis** — beyond scope, suggest external research
- **Sales competitive positioning** — this produces awareness, not sales materials
