# Skill Conventions

*Structural patterns that well-formed skills follow. Used as an analysis lens in Phase 3 to validate target skills, and referenceable by `writing-skills` when authoring new skills.*

## SKILL.md Structure

Well-formed skills follow this section order:

```
---
name: kebab-case-name
description: "Use when [triggering conditions]. Trigger on phrases like '...', '...'"
---

# Skill Title
## Purpose
## Dependencies
## Inputs (optional)
## Outputs (optional)
## Modes (if multiple invocation paths)
## Workflow (Phases or Steps)
## Context Rules
## Edge Cases (optional)
## When NOT to Use
```

### Frontmatter Rules
- Exactly two fields: `name` and `description`
- Max 1024 characters total
- `name`: letters, numbers, hyphens only
- `description`: starts with "Use when...", describes triggering conditions only, NOT workflow summary
- Include trigger phrases at end: `Trigger on phrases like '...', '...'`

## Parallel Gathering

Skills that pull data from multiple independent sources MUST use lettered sub-steps:

```markdown
### Phase 2: Gather Data (Parallel)

Run all data pulls simultaneously. Each source is independent.

#### 2A: {Source Name}
{Tool/API call, what to extract, batch limit}

#### 2B: {Source Name}
{Tool/API call, what to extract, batch limit}
```

### Required Elements
- Header includes "(Parallel)" annotation
- Opening line states sources are independent
- Each sub-step has its own batch limit
- Context Rules section reinforces: "Drop raw data after extraction"

### Batch Limits by Source Type

| Source | Typical Batch Size |
|--------|-------------------|
| Jira tickets | 20 |
| GitHub PRs | 20 |
| Gmail messages | 10 |
| Slack messages | 10 |
| Transcript files scanned | 20 |
| Transcript files analyzed | 5 |

## Interactive Review

Skills with user-facing item review MUST include:

### 1. Fenced Presentation Template
Show exactly what to display per item:
```
[{n}] {file} > {section}: {description}
    Confidence: {level} | Evidence: {source}
```

### 2. Bold Named Actions
Each action on its own line, bolded, with em-dash description:
```markdown
- **"Apply {n}"** — Apply this proposal
- **"Edit {n}"** — Modify before applying
- **"Skip {n}"** — Defer without logging rejection
- **"Reject {n}"** — Log as rejected (affects re-proposal rules)
- **"Show diff {n}"** — Preview exact changes
- **"Apply all"** — Apply all remaining proposals
- **"Done"** — End review
```

### 3. Navigation Commands
Available at any point during review:
```markdown
- **"Next"** — Move to next proposal
- **"Back"** — Return to previous
- **"Jump to {n}"** — Skip to specific proposal
- **"Show overview"** — Re-display all proposals
- **"Remaining"** — Show count of unreviewed proposals
```

## Learning Systems

Skills that learn from sessions need:

### Weighted Preferences
- Format: `- {preference} (weight: {n}, sessions: {count})`
- Weight increases with confirming sessions, decreases with contradictions
- Zero-weight entries flagged as stale after 30 days

### Session Logs
- Appended at end of preferences/log file
- Format: `{date}: {key observations}`
- Include consolidation rule: "Consolidate every N sessions. Stay under {limit} words."

### Consolidation Rules
- State frequency (e.g., "every 5 sessions" or "every 10 sessions")
- State word limit (typically 2,000-3,000 words)
- Consolidation preserves patterns, discards individual session details

## Context Rules Section

Every complex skill MUST have a `## Context Rules` section near the end. One bullet per constraint, written as commands:

- When to load reference files (once at start, not mid-workflow)
- When to drop raw data (after extraction/cross-reference)
- What to keep during interactive review (current item only)
- When to update preference/log files (session end, never mid-workflow)
- Batch size summary
- Prohibited anti-patterns (e.g., "Do not reload reference files mid-workflow")

## Dependencies Section

List dependencies grouped by type:
- **Tools/APIs**: MCP tools, CLI commands
- **Other skills**: with usage annotation (e.g., "called in orchestrated mode")
- **Reference files**: with paths and content description

## When NOT to Use Section

Bullet list of scenarios where this skill should NOT be used, with cross-references:
```markdown
- Need to create a new skill from scratch — use `writing-skills`
- Need to debug a skill that's erroring — use `systematic-debugging`
```

## Common Anti-Patterns

| Anti-Pattern | Convention |
|-------------|-----------|
| Description summarizes workflow | Description describes only triggering conditions |
| No batch limits on gathering | Every data source has explicit batch limit |
| No consolidation rule on log files | All log/preference files state consolidation frequency + word limit |
| Missing Context Rules section | Always present in complex skills |
| Interactive review without navigation commands | Full navigation command set required |
| Parallel steps without "(Parallel)" annotation | Header must signal parallelism |
| Learning sections with no empty-section comments | Empty sections get `<!-- format -->` comments |
