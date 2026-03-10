---
name: skill-improver
description: "Use when a skill needs tuning based on real usage. Trigger on phrases like 'improve that skill,' 'tune the daily-planner skill,' 'skill-improver daily-planner,' 'that skill needs work,' 'fix that workflow,' 'skill needs adjustment,' or any request to refine a skill based on how it actually performed."
---

# Skill Improver

## Purpose
Observe how skills are actually used and propose concrete edits to SKILL.md, reference files, preferences, and rules. Every change requires user approval before writing.

## Dependencies

**Reference Files:**
- `references/improvement-log.md` — session history (proposals made/applied/rejected)
- `references/analysis-heuristics.md` — thresholds, signal keywords, confidence rules
- `references/skill-conventions.md` — structural patterns for well-formed skills (also the audit standard for convention compliance)

## Modes

### Explicit Mode
Invocation: `/skill-improver {skill-name}`

Targets a named skill. Scans transcripts in your Claude Code project directory (`~/.claude/projects/{project-dir}/`) for past sessions where the skill was used, plus the skill's own reference files.

### Post-Session Mode
Invocation: "improve that skill" (or similar) after using a skill in the current conversation

Analyzes the current conversation as evidence. No transcript scanning needed — the conversation IS the evidence.

## Workflow

### Phase 1: Identify Target Skill

1. **Explicit mode**: Validate the named skill exists at `~/.claude/skills/{skill-name}/SKILL.md`. If not found, list available skills and ask.
2. **Post-session mode**: Scan the current conversation for `Skill` tool_use blocks. Extract the skill name. If multiple skills were used, ask which one to improve. If none found, ask the user to name one.
3. Read the target skill's `SKILL.md` fully.

### Phase 2: Gather Evidence (Parallel)

Run all evidence gathering simultaneously. Each source is independent.

#### 2A: Transcript Analysis

**Post-session mode**: Skip this sub-step. The current conversation is the evidence — extract signals directly from it.

**Explicit mode**:
- Grep `.jsonl` files in the Claude Code project directory (`~/.claude/projects/{project-dir}/`) for `Skill` tool_use matching the target skill name.
- Scan max 20 files, analyze max 5 matching transcripts (most recent first).
- Extract per transcript:
  - Which workflow steps were executed vs. skipped
  - User corrections or overrides (exact quotes)
  - Error messages and recovery paths
  - Trigger phrase that invoked the skill
  - Preference language ("always", "never", "I prefer")
  - Frustration signals (see `analysis-heuristics.md` for keywords)

#### 2B: Learning File Analysis

Read all files in `{skill}/references/` (typically 2-5 files). Flag:
- Empty sections that remain empty after 3+ sessions
- Stale entries (30+ days unmodified) or contradictions with SKILL.md
- Missing learning categories vs. workflow steps
- Batch size mismatches between inline limits and Context Rules

If the skill has no `references/` directory, skip this sub-step.

#### 2C: User Input

Ask: **"What's working well? What's not? Anything you always skip or wish was different?"**

Treat response as ground truth — auto-promotes any resulting proposal to HIGH confidence.

### Phase 3: Analyze and Generate Proposals

Load reference files:
- `analysis-heuristics.md` for thresholds and confidence scoring
- `improvement-log.md` to filter out previously rejected proposals (see re-proposal rules)
- `skill-conventions.md` for structural validation

Apply six analysis lenses (thresholds and scoring in `analysis-heuristics.md`):

| Lens | Key Checks |
|------|------------|
| **Convention Compliance** | Structure vs. `skill-conventions.md`: batch limits, consolidation rules, Context Rules, section order, frontmatter |
| **Description / Triggers** | Actual trigger phrases vs. listed triggers; CSO anti-pattern (description summarizes workflow) |
| **Workflow Steps** | Skipped >60% → remove/optional; edited >50% → change default; errors >30% → add handling; unlisted steps → add |
| **Reference Files** | Empty sections, stale overrides, batch size complaints, rules user manually adds each session |
| **Edge Cases** | Unhandled situations → add; never-triggered cases → flag as dead weight |
| **Context Rules** | Premature data drops, batch size issues, file update timing |

### Phase 4: Interactive Review

Present proposals grouped by target file, sorted by confidence (HIGH first):

```
[n] {file} > {section}: {proposed change}
    Confidence: {level} | Evidence: {source}
```

**Actions:** **Apply {n}** / **Edit {n}** / **Skip {n}** (defer, no log) / **Reject {n}** (log to `improvement-log.md`) / **Show diff {n}** / **Apply all** / **Done** (unapplied = skipped)

**Commands:** **Next** / **Back** / **Jump to {n}** / **Show overview** / **Remaining**

If a proposal modifies the skill's `description` field, warn: "This changes skill discovery. Other sessions may find/miss this skill differently."

### Phase 5: Apply Changes

For each approved proposal:
1. Read the target file fresh (not from earlier cache)
2. Apply the edit
3. Show brief confirmation: `Applied [n]: {description}`

If an edit conflicts with the current file state (file changed since Phase 3), warn and ask user to confirm or skip.

### Phase 6: Log Session

Write to `references/improvement-log.md`:
- Date, target skill, mode (explicit/post-session)
- Count: proposals made, applied, skipped, rejected
- Applied proposals: file + brief description
- Rejected proposals: file + brief description + user's reason

If log exceeds 10 sessions, consolidate: preserve patterns, discard individual session details. Stay under 2,000 words.

## Context Rules

- Load `analysis-heuristics.md`, `improvement-log.md`, and `skill-conventions.md` once at Phase 3 start. Do not reload mid-workflow.
- Drop raw transcript data after signal extraction in Phase 2A. Keep only extracted signals.
- During interactive review (Phase 4), keep only the current proposal and its evidence in active context.
- Update `improvement-log.md` only at session end (Phase 6), never mid-workflow.
- Transcript search: max 20 files scanned, max 5 analyzed.
- Read target skill files fresh before each edit in Phase 5 (not from Phase 2 cache).

## Edge Cases

- **No transcripts found (explicit mode)**: Proceed with learning files (2B) + user input (2C) only. Note reduced confidence in all transcript-dependent proposals.
- **Skill just created (0 sessions)**: Only Phase 2C (user input) is available. Suggest: "This skill has no usage history yet. Run it a few times first for better analysis."
- **Skill has no references/ directory**: SKILL.md proposals only. Skip Phase 2B entirely.
- **Multiple skills used in one session**: Ask which one to improve. Do not batch — one skill per run.
- **Conflicting proposals**: Surface the conflict explicitly. User chooses one.
- **All proposals rejected**: Log rejections and exit: "No changes applied. Rejected proposals logged for future reference."
- **Previously rejected proposal re-emerging**: Only re-propose if 2+ additional evidence sessions accumulated after rejection. Cap at MEDIUM confidence with note: "Previously rejected on {date}."

## When NOT to Use

- Need to create a new skill from scratch — use `writing-skills`
- Need to debug a skill that's throwing errors at runtime — use `systematic-debugging`
- Need to understand what a skill does — just read its SKILL.md
- Need to test a skill against pressure scenarios — use `writing-skills` (TDD methodology)
