---
name: technical-to-business-translator
description: "Use when translating technical concepts for business audiences or business requests for technical teams. Trigger on phrases like 'explain this to leadership,' 'business case for,' 'how do I tell the clubs,' 'frame this for,' 'translate this for,' 'what does this mean for the business,' 'non-technical explanation,' 'executive summary of this technical change,' or when stakeholder-update needs to explain a technical topic."
---

# Technical-to-Business Translator

## Purpose
As a connector between technical and business worlds, substance translation is a daily task. `stakeholder-update` and `voice-analyzer` handle *how* to say it (tone, voice, channel). This skill handles *what* to say — translating the substance between technical and business framing. Works in both directions: tech→business and business→tech.

## Dependencies

**Tools/APIs:**
- Jira API — read technical tickets, PRs, architecture decisions for context
- Confluence API — read specs, architecture docs for technical detail
- Slack API — read technical discussions for context

**Other Skills:**
- `company-context` — audience personas, team context, business terminology
- `voice-analyzer` — adapt output tone for the target audience
- `stakeholder-update` — feeds translated content into status updates
- `impact-analyzer` — pull quantitative impact data (when available)

**Reference Files:**
- `references/translation-patterns.md` — common tech→business mappings, framing templates, dimension checklist
- `references/audience-frames.md` — per-audience framing guides (clubs, leadership, engineering, partners)

## Inputs
- **Source material** (required): technical proposal, Jira ticket, architecture decision, code change description, or business requirement
- **Target audience** (required): "leadership," "clubs," "engineering," "partners," or specific person
- **Direction** (auto-detected): tech→business or business→tech
- **Depth** (optional): "one-liner," "paragraph," "full brief" (default: paragraph)

## Outputs
- Audience-framed explanation at the requested depth
- Business dimensions identified: cost, risk, timeline, user impact, opportunity cost
- Optional: per-stakeholder variants if multiple audiences specified

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Tech→Business** | "explain this to leadership," "business case for," "non-technical summary" | Translate technical content into business-framed explanation |
| **Business→Tech** | "technical feasibility of," "what would this require," "engineering assessment" | Translate business request into technical scope and constraints |
| **Multi-Audience** | "explain this to clubs AND leadership" | Generate audience-specific variants of the same content |
| **Inline** | Called by another skill (stakeholder-update, change-communicator) | Return translated content without interactive review |

---

## Workflow

### Tech→Business Mode

#### Step 1: Understand the Technical Content

If source is a reference (Jira ticket, Confluence page, Slack thread):
1. Read the source material
2. Extract: what changed/is proposed, why, technical constraints, dependencies, risks
3. If the technical content is unclear, ask the user for clarification before translating

If source is inline description:
1. Parse the user's explanation
2. Ask clarifying questions if anything is ambiguous (one at a time)

#### Step 2: Identify Business Dimensions

For every technical change, map to the business dimensions the audience cares about. Check each — not all apply to every change:

| Dimension | Question | Example Mapping |
|-----------|----------|-----------------|
| **User Impact** | How does this affect the end user's experience? | "Database migration" → "3-hour maintenance window Sunday night" |
| **Cost** | What does this cost in money, time, or opportunity? | "New service" → "$X/month infrastructure, 2 sprints to build" |
| **Risk** | What could go wrong? What's the downside? | "Library upgrade" → "Low risk, extensive test coverage, rollback plan in place" |
| **Timeline** | When will this happen? How long? | "Refactor" → "3 weeks, transparent to users" |
| **Opportunity Cost** | What are we NOT doing while we do this? | "Tech debt sprint" → "Delaying feature X by one sprint" |
| **Strategic Alignment** | How does this connect to business goals? | "API redesign" → "Enables the self-service integration clubs have been requesting" |
| **Revenue/Growth** | Does this affect revenue, retention, or growth? | "Performance fix" → "Reduces booking abandonment by ~15%" |

#### Step 3: Frame for Audience

Load the target audience's framing guide from `references/audience-frames.md`. Apply:

**Clubs (travel club stakeholders):**
- Lead with: how this affects their agents and members
- Frame in: workflow impact, timeline, what they need to do (if anything)
- Avoid: technical jargon, internal team references, architecture details
- Include: "what this means for you" section

**Leadership (executives):**
- Lead with: business impact (revenue, risk, strategic alignment)
- Frame in: ROI, timeline, trade-offs, resource implications
- Avoid: implementation details, technical terminology
- Include: decision or FYI framing — do they need to act?

**Engineering (other technical teams):**
- Lead with: what changed and why
- Frame in: technical details they need to know, integration impact, API changes
- Avoid: over-explaining things engineers already know
- Include: action items if their systems are affected

**Partners (external technical partners):**
- Lead with: what's changing in the interface they use
- Frame in: API contract changes, migration steps, timeline, support available
- Avoid: internal business rationale, team structure
- Include: specific version/date/migration guide references

#### Step 4: Generate Translation

Compose the translation at the requested depth:

**One-liner:** Single sentence capturing the business impact.
**Paragraph:** 3-5 sentences covering impact, timeline, and any action needed.
**Full brief:** Structured document with all relevant business dimensions, background, impact, timeline, and recommendations.

Apply `voice-analyzer` profiles if sending to a specific person.

#### Step 5: Review

Present the translation. Actions:
- **"Use it"** — copy/approve for use in another skill or direct communication
- **"Simpler"** — reduce jargon, shorter sentences, fewer details
- **"More detail"** — add technical context or business dimensions
- **"Different angle"** — reframe emphasis (e.g., focus on risk instead of opportunity)
- **"Another audience"** — generate a variant for a different audience

### Step 6: Learn

After the translation is used:
1. Log which audience frame was applied and whether the user edited the output
2. Track which business dimensions the user added, removed, or emphasized
3. If a translation was particularly effective (approved without edits), note it as a reusable pattern
4. Update `references/translation-preferences.md`

Consolidate every 10 sessions. Keep under 1,500 words.

---

### Business→Tech Mode

#### Step 1: Understand the Business Request

Parse the business requirement or request. Extract:
- What the stakeholder wants (stated need)
- Why they want it (underlying need — may differ from stated)
- Constraints mentioned (timeline, budget, scope)
- Success criteria (how they'd know it's working)

#### Step 2: Technical Assessment

Map to technical dimensions:

| Dimension | Assessment |
|-----------|-----------|
| **Feasibility** | Can we do this? What's required? |
| **Complexity** | Simple / Moderate / Complex — and why |
| **Dependencies** | What else needs to change? Other teams involved? |
| **Risks** | Technical risks, unknowns, integration concerns |
| **Alternatives** | Simpler ways to achieve the same business goal |
| **Effort** | Rough scope (not a precise estimate — flag this) |

#### Step 3: Generate Assessment

Produce a technical feasibility assessment framed for the requesting audience:
- Business-friendly summary: "Yes, here's what it would take" or "This is more complex than it sounds because..."
- Technical detail section (for engineering team)
- Recommended approach with trade-offs
- Questions that need answering before scoping

---

## Reference File: Translation Patterns

`references/translation-patterns.md` contains:
- Common technical terms → business equivalents (e.g., "latency" → "how long users wait")
- Framing templates for common scenarios (migration, deprecation, new feature, bug fix, performance improvement, security patch)
- Anti-patterns: translations that hide important information or create false precision

`references/audience-frames.md` contains:
- Per-audience framing guides (what they care about, vocabulary, structure preferences)
- "Never say this" lists per audience (e.g., don't tell clubs about "technical debt")
- Example before/after translations

---

## Context Rules
- Load source material in Step 1. Drop raw source after extracting key points.
- Load audience frame only for the current target audience. Drop before loading another.
- In inline mode (called by another skill), return the translation directly without interactive review.
- When translating for multiple audiences, generate one at a time. Drop previous before generating next.
- Never fabricate quantitative data. If impact numbers aren't available, say "we'd need data from {source} to quantify this" or invoke `impact-analyzer`.
- Source material batch limit: summarize epics with >20 stories rather than loading each.
- In Inline mode (called by another skill), return the translation directly. Do not load preferences or run the Learn step.
- Translation output should not exceed 300 words for paragraph depth, 100 words for one-liner, or 1,000 words for full brief.

## Edge Cases
- **Source material is already business-friendly:** Don't over-translate. Polish and add missing dimensions, but don't strip useful detail.
- **Technical content the user can't fully explain:** Help them articulate it. "What problem does this solve?" / "What happens if we don't do it?"
- **Audience has technical background:** Some leadership and club contacts are technical. Check audience profiles before dumbing down.
- **Controversial or politically sensitive topic:** Flag to user: "This topic may need careful framing. Want me to draft multiple angles for you to choose from?"
- **Translation would hide important risk:** Never optimize for comfort at the expense of honesty. Flag: "The business-friendly version understates this risk. Want to include a risk callout?"

## When NOT to Use
- **Drafting the actual message** — use `stakeholder-update` or `message-drafting` (this skill provides the substance, those handle delivery)
- **Data analysis to quantify impact** — use `impact-analyzer` or `sql-data-analysis`
- **Writing technical documentation** — use `system-behavior-documenter`
- **Code review or technical design** — those are engineering tasks, not translation
