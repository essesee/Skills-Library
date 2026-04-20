# Analysis Heuristics

*Tunable thresholds, signal keywords, confidence rules, transcript search strategy, auto-apply tiers, and gate check logic for skill evolution.*

## Signal Keywords

### Frustration / Friction
- "skip this", "don't need this", "ignore that step", "too much", "overkill"
- "already did that", "I know", "just do it", "we covered this"
- "wrong order", "backwards", "not yet", "too early", "too late"

### Positive Signals
- "this is good", "helpful", "keep this", "exactly right"
- User follows step without prompting or correction

### Correction Signals
- User overrides a default, changes a value, edits output
- "no, do X instead", "actually", "change that to", "not X, Y"
- User adds a step the skill didn't include

### Error Signals
- Tool call failures during skill execution
- "that broke", "wrong file", "doesn't exist", "not found"
- Retries or fallback paths triggered

## Thresholds

| Metric | Threshold | Action |
|--------|-----------|--------|
| Step skip rate | >60% across sessions | Propose remove or make optional |
| Step always edited | >50% across sessions | Propose change default |
| Step causes errors | >30% across sessions | Propose add error handling |
| Unlisted step added by user | 2+ sessions | Propose add to workflow |
| Learning section empty | After 3+ sessions | Investigate — missing category or dead weight? |
| Learning entry stale | 30+ days unmodified | Flag for review |
| Reference file contradicts SKILL.md | Any occurrence | Propose alignment edit |
| Batch size complaints | 2+ sessions | Propose adjustment |
| SKILL.md line count | >500 lines | Propose splitting to reference files |
| Reference nesting depth | >1 level from SKILL.md | Propose flattening |
| Reference file without TOC | >100 lines | Propose adding table of contents |

## Confidence Scoring

| Level | Criteria |
|-------|----------|
| HIGH | 3+ sessions with consistent signal, OR explicit user statement (Phase 2C), OR clear single-session pattern with strong evidence |
| MEDIUM | 2 sessions with consistent signal, OR moderate behavioral signal in 1 session |
| LOW | 1 session only, OR ambiguous signal. Never auto-applied to core workflow changes |

### Modifiers
- User verbal input (Phase 2C) → auto-promotes to HIGH
- Previously rejected proposal → capped at MEDIUM, even with new evidence
- Automatic mode (current conversation only) → treated as 1 strong session; can reach HIGH if user explicitly confirms

## Transcript Search Strategy

### Search Location
`.jsonl` files in the Claude Code project directory (`~/.claude/projects/{project-dir}/`)

### Search Pattern
Grep for `Skill` tool_use blocks where the skill argument matches the target skill name.

### Extraction Scope
- Max 5 transcripts analyzed per run
- Max 20 files scanned to find those 5
- Search most recent files first (sort by modification time)
- Extract: tool calls, user messages within 5 turns of skill invocation, and assistant responses showing skill execution

### What to Extract per Transcript
1. Which workflow steps were executed vs. skipped
2. User corrections or overrides (exact quotes)
3. Error messages and recovery paths
4. Trigger phrase that invoked the skill
5. Any user preference language ("always", "never", "I prefer")

## Re-Proposal Rules

- Rejected proposals logged in `evolution-log.md` with date and reason
- NOT re-proposed unless 2+ additional evidence sessions accumulate after rejection date
- Re-proposed at MEDIUM max confidence with note: "Previously rejected on {date}."
- If rejected twice, requires explicit user request to reconsider

## Auto-Apply Trust Tiers

How the auto-apply scope expands over time based on approval history.

### Tier 0 — Always Auto-Apply
No approval history needed. These are safe by definition:
- Typo corrections (spelling, grammar)
- Markdown formatting fixes (broken links, missing fences, heading levels)
- Metadata corrections (frontmatter field alignment, path fixes)

### Tier 1 — Graduated Auto-Apply
Requires 10 consecutive approvals of the specific change type to graduate from propose to auto-apply:
- Duplicate sentence removal
- Meaning-preserving compression (same content, fewer words)
- Dead-weight removal (empty sections, unused placeholders)

**Graduation rules:**
- Approved proposal → increment consecutive approval counter for that change type
- Edited-then-applied → continues the streak (counts as approval)
- Rejected → resets counter to 0
- Skipped → neutral (no change to counter)
- **Graduation threshold: 10 consecutive approvals** → type moves to auto-apply
- **Demotion:** if user rejects an auto-applied change, type immediately reverts to propose, counter resets, re-graduation requires 15 approvals

### Never Auto-Apply
These always require user approval regardless of history:
- Cross-skill consolidation (shared references, conflict resolution)
- Workflow changes (step additions, removals, reordering)
- Description or trigger phrase changes
- Context Rules modifications
- Edge Case additions or removals

## Prompt Clarity Signals

Indicators that a skill's instructions may be unclear or ambiguous.

### Ambiguity Indicators
- Conditional logic without clear branching ("if appropriate", "when relevant", "as needed")
- Undefined terms used in instructions (term appears in workflow but never explained)
- Steps that reference external state without specifying how to check it
- "Should" vs. "must" inconsistency within the same workflow

### Style Indicators
- Second-person language ("you should", "you can", "you need to") instead of imperative form
- Description summarizes workflow steps (CSO anti-pattern)
- Inconsistent terminology (same concept, different words across sections)

### Redundancy Indicators
- Same instruction appears in multiple sections (e.g., Context Rules repeats a Workflow constraint)
- Reference file restates content already in SKILL.md
- Multiple synonyms used for the same concept without explicit definition

## Token Efficiency Signals

Indicators that a skill consumes more context than necessary.

### Verbose Sections
- Any single section exceeding 500 words — candidate for compression or extraction to reference file
- Tables where prose would be shorter (or vice versa)
- Examples that could be collapsed into a pattern statement

### Reference File Bloat
- Reference files exceeding 3,000 words without a consolidation rule
- Reference files with >30% content duplicated from SKILL.md
- Multiple reference files with >70% content overlap (consolidation candidate)

### Repeated Concepts
- Same rule stated in SKILL.md and a reference file
- Workflow step that restates a Context Rule
- Edge case that duplicates a workflow conditional

## Gate Check Thresholds

Thresholds for the automatic mode gate (Phase 1). All four must be NO for silent exit.

| Signal | Threshold | How to Check |
|--------|-----------|--------------|
| Friction in session | 2+ signals from keywords above | Scan current conversation for frustration, correction, or error keywords near skill execution |
| Learning period | usage_count < 5 | Read optimizer-observations.md |
| Stale | last_optimized > 30 days | Read optimizer-observations.md |
| Cross-skill flag | Any pending flag | Check evolution-log.md Cross-Skill Patterns for unresolved flags mentioning this skill |
