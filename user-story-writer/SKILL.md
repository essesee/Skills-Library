---
name: user-story-writer
description: "Write or improve story descriptions for Jira, Linear, or GitHub Issues. Auto-validates when writing to trackers."
---

# User Story Writer

## Purpose

Produces structured, high-quality story descriptions in markdown. Tool-agnostic — output works in any tracker. Follows a proven, opinionated format.

## Dependencies

**Reference Files** (load as needed — not all at once):
- `references/templates.md` — Load at Step 2. Literal skeletons per story type.
- `references/examples.md` — Load only if calibration needed (first time using a type, or quality unclear).
- `references/quality-checklist.md` — Load at Step 4 (validation).
- `references/feature-flag-convention.md` — Load at Step 1.5 if feature flag is needed.

**Other Skills:**
- `company-context` — Load if you need persona definitions or team conventions.
- `../company-context/references/team-agreement.md` — Load at Step 4 (validation). DoR criteria supplement the quality checklist.

**Data Files:**
- `memory/developer-profiles.md` — Check at Step 3 if assignee is known. Overrides default format.

## Inputs

| Input | Required | Source |
|-------|----------|--------|
| Problem/feature description | Yes | User, meeting notes, bug cluster, Slack thread |
| Story type | No | Auto-detected (see decision table) |
| Assignee | No | If known, check developer profiles for format overrides |
| Related tickets | No | For bug consolidation: list of child bugs |
| Design reference | No | Figma link, mockup, or screenshot |
| Feature flag needed | No | Ask user at Step 1.5 (Feature stories only) |

## Output

Structured markdown with these sections (type determines which are included):

**Feature / Tech Debt / Simple Config:**
```
### User story
### Context
### Acceptance criteria  OR  ### Requirements (assignee preference)
### Design  (if applicable)
### Other information  (if applicable)
```

**Bug:**
```
### Bug summary
### Current vs Expected
### Reproduction
### Acceptance criteria  OR  ### Requirements (assignee preference)
```

## Step 0: Detect Mode

| Signal | Mode |
|--------|------|
| Content already exists (plan, draft, existing ticket) | **Review Mode** — skip to Step 3 (assignee prefs) then Step 4 (validate) |
| No content yet, writing from scratch | **Author Mode** — proceed Step 1 through Step 5 as normal |

**Review Mode workflow**: Load the pre-written content, apply assignee preferences (Step 3), validate against quality checklist (Step 4), propose improvements if any fail. Present the validated (or improved) content at Step 5.

## Step 1: Detect Story Type

| Signal | Type | Template |
|--------|------|----------|
| Single bug, defect, incorrect behavior | Bug | `bug` |
| Multiple related bugs being grouped | Bug Consolidation | `bug-consolidation` |
| "Spike", investigation, questions to answer | Spike | `spike` |
| Tech debt, refactor, code cleanup, infra | Tech Debt | `tech-debt` |
| Simple toggle, config change, env variable | Simple Config | `simple-config` |
| New capability, user-facing feature, UI work | Feature | `feature` |

**Default**: Feature (if ambiguous, ask user).

## Step 1.5: Feature Flag Check

Skip this step for non-Feature story types.

1. Ask the user: "Does this feature need a PostHog feature flag for rollout?"
2. If **no** → proceed to Step 2 normally
3. If **yes**:
   a. Load `references/feature-flag-convention.md`
   b. Propose a flag name following the convention: `{product}-{feature-description}` in kebab-case
   c. Confirm the flag name with the user before proceeding
   d. Carry the confirmed flag name into Step 2 for inclusion in acceptance criteria

## Step 2: Load Template and Write

1. Load `references/templates.md`
2. Find the template matching the detected type
3. Fill in the skeleton using the input context

**User story format**: `As a [persona], when [situation], I want [need] so that [value]`
- Multiple user stories are OK if genuinely distinct perspectives exist
- Use bullet points for multiple user stories, not numbered lists
- Personas: `operator`, `developer`, `maintainer`, `user`, `admin`

**Context section**: 2-5 sentences. Cover: what's the problem, why it matters now, what depends on this. No filler.

**Acceptance criteria**:
- Engineering/tech debt: Checkbox format (`- [ ] specific testable criterion`)
- Product/feature: Bullet points or checkboxes — either works
- Each criterion must be independently testable
- Include negative cases and edge cases when relevant
- If a feature flag was confirmed at Step 1.5, include flag ACs from the template (gating, both states, cleanup). Assignee profile overrides the exact format.

## Step 3: Apply Assignee Preferences

1. Check `memory/developer-profiles.md` for a default assignee and any named assignee
2. IF assignee is specified → look up their profile. IF profile exists → apply their format overrides
3. IF no assignee is specified → use the default assignee's profile (currently: the default assignee)
4. IF user says "for AI", "for Claude", or similar → use the Claude Code profile instead
5. IF no profile found for the assignee → use default template as-is

## Step 4: Validate

Load `references/quality-checklist.md`. Run every item, including the Definition of Ready section, and fix failures before presenting output. The DoR checks reference the Platform Scrum team agreement (March 2026); flag DoR gaps gently per the instructions in the checklist.

## Step 5: Present

Show the complete story description in a markdown code block. If creating in Jira or another tool, confirm with user before writing.

**Follow-up**: After the story is accepted, suggest using `superpowers:test-driven-development` to write tests before implementation — especially for Feature and Bug Consolidation types.

## Orchestration

When called by other skills:
- `bug-consolidator` → passes cluster data + child bugs. Use `bug-consolidation` template.
- `ticket-proposer` → passes action item + context. Auto-detect type, default to `feature`.
- Return the formatted description. Caller handles ticket creation.

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Copy bug symptoms as the user story | Write a story about the underlying problem |
| Vague ACs ("works correctly") | Specific, testable criteria with expected behavior |
| Wall of text in Context | 2-5 sentences max. Link to docs for detail. |
| Repeat info across sections | Each section has unique content |
| Add Out of Scope unless needed | Only include if scope is genuinely ambiguous |
| Write ACs the developer can't test | Every AC maps to a verifiable action |
| Use passive voice in user stories | "I want to X" not "X should be done" |
| Over-engineer edge cases | Only include edge cases relevant to the change |
| Generic personas ("as a user") when specific exists | Use the real persona (operator, developer, etc.) |
| Include implementation details in user story | Keep user story focused on need and value. Implementation goes in ACs or dev notes. |
