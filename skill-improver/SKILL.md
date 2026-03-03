---
name: skill-improver
description: "Use when a skill needs tuning based on real usage — steps getting skipped, triggers that don't match, preferences that drifted, or reference files that went stale. Use after a skill session to propose edits, or target a specific skill for transcript-based analysis. Trigger on phrases like 'improve that skill', 'tune the daily-planner skill', 'skill-improver daily-planner', 'that skill needs work', 'fix that workflow'."
---

# Skill Improver

## Purpose

Observe how skills are actually used and propose concrete edits to SKILL.md, reference files, preferences, and rules. Every change requires user approval before writing. Closes the feedback loop between skill design and real-world usage.

## Dependencies

- **Reference files**:
  - `references/improvement-log.md` — session history (proposals made/applied/rejected)
  - `references/analysis-heuristics.md` — thresholds, signal keywords, confidence rules
  - `references/skill-conventions.md` — structural patterns for well-formed skills
- **Other skills**: None (no runtime dependencies)

## Modes

### Explicit Mode
Invocation: `/skill-improver {skill-name}`

Targets a named skill. Scans transcripts in `~/.claude/projects/-Users-jesseshedd-Documents-work/` for past sessions where the skill was used, plus the skill's own reference files.

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
- Grep `.jsonl` files in `~/.claude/projects/-Users-jesseshedd-Documents-work/` for `Skill` tool_use matching the target skill name.
- Scan max 20 files, analyze max 5 matching transcripts (most recent first).
- Extract per transcript:
  - Which workflow steps were executed vs. skipped
  - User corrections or overrides (exact quotes)
  - Error messages and recovery paths
  - Trigger phrase that invoked the skill
  - Preference language ("always", "never", "I prefer")
  - Frustration signals (see `analysis-heuristics.md` for keywords)

#### 2B: Learning File Analysis

Read all files in `{skill}/references/`. Flag:
- Empty sections that remain empty after 3+ sessions
- Zero-weight or stale entries (30+ days unmodified)
- Contradictions between reference files and SKILL.md
- Missing learning categories vs. workflow steps (e.g., a gathering phase with no corresponding preference tracking)
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

Apply six analysis lenses:

**1. Convention Compliance**
Compare the target skill's structure against `skill-conventions.md`. Flag missing patterns:
- No batch limits on a gathering phase
- No consolidation rule on a learning/log file
- Missing Context Rules reinforcement for parallel steps
- Section order deviations
- Frontmatter issues (description summarizes workflow = CSO anti-pattern)

**2. Description / Triggers**
Compare actual trigger phrases from transcripts vs. listed triggers. Check:
- Trigger phrases users say that aren't in the description
- Listed triggers nobody uses
- Description summarizing workflow instead of triggering conditions (CSO anti-pattern)

**3. Workflow Steps**
- Skipped >60% of sessions → propose remove or make optional
- Heavily edited by user >50% → propose change default
- Causes errors >30% → propose add error handling
- User does unlisted steps → propose add to workflow
- Execution order mismatches → propose reorder

**4. Reference Files**
- Empty learning sections → investigate cause
- Stale overridden rules → propose update defaults
- Batch size complaints → propose adjustment
- Rules user manually adds each session → propose as defaults

**5. Edge Cases**
- Unhandled situations from transcripts → propose new entries
- Never-triggered edge cases → flag as potential dead weight

**6. Context Rules**
- Premature data drops causing re-fetches
- Batch size problems (too large = context overflow, too small = excessive calls)
- File update timing issues (mid-workflow vs. session end)

### Phase 4: Interactive Review

Present proposals grouped by target file, sorted by confidence (HIGH first):

```
[1] SKILL.md > Workflow Step 3: Make optional (skipped 4/5 sessions)
    Confidence: HIGH | Evidence: transcripts 2026-02-28, 2026-03-01

[2] SKILL.md > Description: Add trigger phrase "tune my planner"
    Confidence: MEDIUM | Evidence: transcript 2026-03-01

[3] references/preferences.md > Batch size: Increase Jira batch from 10 to 20
    Confidence: LOW | Evidence: 1 session
```

**Actions:**
- **"Apply {n}"** — Apply this proposal. Reads target file fresh, applies edit, shows confirmation.
- **"Edit {n}"** — Modify the proposal before applying. User provides adjusted wording.
- **"Skip {n}"** — Defer without logging rejection. Not counted against re-proposal.
- **"Reject {n}"** — Log as rejected in `improvement-log.md`. Affects re-proposal rules.
- **"Show diff {n}"** — Preview exact changes before deciding.
- **"Apply all"** — Apply all remaining proposals in order.
- **"Done"** — End review. Unapplied proposals treated as skipped.

**Navigation Commands** (available at any point):
- **"Next"** — Move to next proposal
- **"Back"** — Return to previous proposal
- **"Jump to {n}"** — Skip to specific proposal
- **"Show overview"** — Re-display all proposals with status
- **"Remaining"** — Show count of unreviewed proposals

**Warning**: If a proposal modifies the skill's `description` field, warn: "This changes skill discovery. Other sessions may find/miss this skill differently."

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
