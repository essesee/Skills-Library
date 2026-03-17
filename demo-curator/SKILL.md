---
name: demo-curator
description: "Use when preparing demos or showcases — pulling recently shipped work and generating a narrative with talking points. Trigger on phrases like 'prep demo for,' 'what shipped for showcase,' 'demo talking points,' 'prepare showcase,' 'sprint demo,' 'stakeholder demo,' 'show and tell prep,' 'what can we demo,' or any request to prepare a presentation of delivered work."
---

# Demo Curator

## Purpose
Demo and showcase prep is recurring, well-defined, and currently manual each time. This skill pulls recently shipped work from Jira, groups by theme and user impact rather than ticket order, generates a narrative arc (problem → what we built → impact), and produces talking points tailored to the audience.

Unlike `release-notes` (which produces written documentation), this skill produces talking points and a presentation narrative.

## Dependencies

**Tools/APIs:**
- Jira API — recently completed stories/tasks, epic context, sprint data
- GitHub CLI — merged PRs, demo-worthy screenshots or artifacts
- PostHog API — usage/impact data for shipped features
- Slack API — search for demo-worthy announcements, user feedback
- Confluence API — specs and design docs for background context

**Other Skills:**
- `company-context` — audience context, team names, business terminology
- `technical-to-business-translator` — frame technical work for non-technical audiences
- `release-notes` — shares shipped work data when both run in the same period
- `impact-analyzer` — pull usage/impact metrics for shipped features (inline mode)

**Reference Files:**
- `references/demo-preferences.md` — audience profiles, narrative style, past demo log, favorite formats

## Inputs
- **Time window** (optional): "last sprint," "last 2 weeks," date range (default: last 14 days)
- **Audience** (required): "clubs," "leadership," "engineering," "all-hands," or specific group
- **Scope** (optional): specific epic(s) or "everything shipped"
- **Format** (optional): "talking points," "slide outline," "narrative script" (default: talking points)

## Outputs
- Themed grouping of shipped work (not ticket-order dump)
- Narrative arc per theme: problem → solution → impact
- Audience-specific talking points
- Optional: slide outline with suggested visuals
- Optional: impact metrics per feature

---

## Workflow

### Step 1: Gather Shipped Work (Parallel)

#### 1A: Jira — Completed Work

```
project IN ({team_projects}) AND status changed to Done DURING ("{start_date}", "{end_date}") ORDER BY priority ASC
```

Extract per item: key, summary, description, epic, story points, type (story/bug/task), labels, acceptance criteria summary.

Batch: 20 items.

#### 1B: GitHub — Merged PRs (Optional)

If configured, pull merged PRs for the period. Match to Jira tickets via key references in PR titles/bodies. Flag PRs with visual changes (UI components, screenshots in PR body).

#### 1C: PostHog — Usage Data (Optional)

For major features shipped in the period:
- Early adoption metrics (users, events since launch)
- Error rates (is it stable?)
- Comparison to pre-launch baseline

#### 1D: Slack — Reactions and Feedback

Search for messages about shipped features:
- Customer feedback or reactions
- Internal team reactions ("this is great," "nice work")
- Demo-worthy screenshots or recordings shared

### Step 2: Theme and Group

Don't present tickets in order. Group by user impact themes:

**Grouping heuristics:**
1. **Same epic** → natural group
2. **Same user workflow** → group by the user journey they improve
3. **Same problem domain** → bugs + features addressing the same area
4. **Standalone improvements** → individual items that don't cluster

For each group, identify:
- The user problem or opportunity this addresses
- The key deliverables (features, fixes, improvements)
- The measurable or observable impact

**Prioritize groups** for demo order:
1. Highest user impact (most users affected, biggest workflow improvement)
2. Strategic alignment (connects to roadmap/OKRs)
3. Visual appeal (something you can actually show)
4. Technical achievement (impressive engineering work)

### Step 3: Build Narrative

For each theme group, construct:

```
### {Theme Title}
**Problem:** {What was broken/missing/painful — in audience terms}
**What We Built:** {2-3 sentence summary of the solution}
**Impact:** {Metrics if available, qualitative signal if not}
**Demo Path:** {If applicable: specific steps to walk through in a live demo}
```

**Narrative arc for the full demo:**
1. **Opening:** "This sprint we focused on {1-2 themes}. Here's what shipped."
2. **Main themes:** Walk through each group, problem → solution → impact
3. **Quick wins:** Rapid-fire list of smaller improvements/fixes
4. **What's next:** Brief preview of upcoming work (from in-progress stories)
5. **Close:** Restate the overall impact/progress

### Step 4: Audience-Specific Framing

Adapt the narrative for the target audience:

| Audience | Emphasis | Avoid |
|----------|----------|-------|
| **Clubs** | Agent workflow improvements, member experience, time savings | Technical details, architecture, internal tooling |
| **Leadership** | Strategic progress, ROI indicators, risk mitigation | Implementation details, story-level granularity |
| **Engineering** | Technical achievements, architecture improvements, debt reduction | Business framing they already know |
| **All-hands** | Mix of business impact + technical highlights, team recognition | Deep dives on any one area |

Use `technical-to-business-translator` inline for non-technical audiences.

### Step 5: Generate Output

Based on requested format:

**Talking Points (default):**
```
## Demo: {date range} — {audience}

### Theme 1: {title}
- PROBLEM: {one sentence}
- SOLUTION: {one sentence}
- IMPACT: {metric or signal}
- SHOW: {what to demo live, if applicable}
- TALKING POINT: {key message to land}

### Theme 2: {title}
...

### Quick Wins
- {item}: {one-line impact summary}

### Coming Next
- {upcoming work}: {one-line preview}
```

**Slide Outline:**
For each theme: suggested slide title, bullet points, recommended visual (screenshot, chart, live demo).

**Narrative Script:**
Full spoken narrative with transitions between sections. Natural voice, not robotic.

### Step 6: Review

Present the output. Actions:
- **"Approve"** — done
- **"Reorder"** — change the presentation order
- **"Remove item"** — drop something from the demo
- **"Add item"** — include something not captured by data pull
- **"Change audience"** — reframe for a different audience
- **"Add metrics"** — invoke `impact-analyzer` for specific items
- **"Expand {theme}"** — add more detail to a specific section

### Step 7: Learn

Update `references/demo-preferences.md`:
- Which themes the user promoted/demoted
- Items removed (not demo-worthy signals)
- Audience framing adjustments
- Narrative style preferences
- Session log entry

---

## Context Rules
- Run Step 1 data pulls in parallel. Drop raw data after extraction.
- Keep the themed grouping compact — summaries, not full ticket descriptions.
- In Step 3, load epic/spec context only for the theme currently being narrated. Drop before the next.
- Impact metrics (PostHog): only pull for top 3-5 themes. Don't query for every minor fix.
- Batch limits: Jira 30 items, PRs 20, Slack 10.

## Edge Cases
- **Nothing shipped:** "No completed work found in this period. Possible reasons: date range too narrow, work is still in progress, or tickets aren't being closed in Jira."
- **Only bug fixes shipped:** Still demo-worthy. Frame as reliability and quality improvements. "Zero new bugs" is a valid talking point.
- **50+ items shipped:** Group aggressively. Top 3-4 themes with narratives, rest as "quick wins" list.
- **No impact data available:** Use qualitative signals from Slack/Jira. If nothing: "Impact data not yet available — feature just shipped."
- **User wants to demo someone else's team's work:** Can pull from other Jira projects if keys are provided. Note: context may be thinner.
- **Recurring demo audience:** Check demo preferences for past sessions. Avoid repeating themes from recent demos. Focus on what's new since last demo.

## When NOT to Use
- **Written release notes** — use `release-notes`
- **Stakeholder-specific status update** — use `stakeholder-update`
- **Sprint retrospective** — different format and purpose
- **Sales presentation** — this is internal team demos, not sales materials
