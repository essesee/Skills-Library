---
name: skill-evolver
description: "Tune skills based on real usage. Runs automatically after skill completions or explicitly on request."
---

# Skill Evolver

## Purpose

Continuously improve skills based on real usage. Two modes: automatic (lightweight post-use assessment) and explicit (deep analysis). Auto-applies safe changes, proposes larger edits, tracks cross-skill patterns.

## Dependencies

**Reference Files:**
- `references/evolution-log.md` — cross-skill log: trust calibration, patterns, recent sessions
- `references/analysis-heuristics.md` — thresholds, signal keywords, confidence rules, auto-apply tiers, gate thresholds
- `references/skill-conventions.md` — structural patterns for well-formed skills

**Per-Skill State (created on demand):**
- `{target-skill}/references/optimizer-observations.md` — per-skill usage count, friction signals, pending observations

## Modes

### Automatic Mode
Triggered after any skill completes (via CLAUDE.md Default Behavior). Runs a lightweight gate check. If warranted, analyzes and improves. If not, exits silently.

### Explicit Mode
Invocation: `/skill-evolver {skill-name}` or "improve that skill" (or similar)

Deep analysis with all eight lenses, user input, and cross-skill scan. Absorbs everything skill-improver previously did.

## Workflow

### Phase 0: Identify Target Skill

1. **Automatic mode**: Extract the skill name from the most recent `Skill` tool_use block in the current conversation.
2. **Explicit mode**: Validate the named skill exists at `~/.claude/skills/{skill-name}/SKILL.md`. If not found, list available skills and ask. If invoked as "improve that skill" after using a skill, extract from conversation. If multiple skills were used, ask which one.
3. Read the target skill's `SKILL.md` fully.
4. Read or create `{target-skill}/references/optimizer-observations.md`.

### Phase 1: Gate Check (Automatic Mode Only)

Lightweight decision: should I act? Check four signals:

1. **Friction in this session?** — 2+ signals from analysis-heuristics keywords (frustration, correction, error) detected in the current conversation around skill usage
2. **Learning period?** — `usage_count < 5` in optimizer-observations.md (still gathering baseline)
3. **Stale?** — `last_optimized` > 30 days ago
4. **Cross-skill flag pending?** — Any unresolved flag in evolution-log.md for this skill

**If ALL four are NO** → silent exit. Increment `usage_count` and update `last_observed` in optimizer-observations.md. Zero user-visible output.

**If ANY signal is YES** → proceed to Phase 2.

### Phase 2: Gather Evidence (Parallel)

Run all evidence gathering simultaneously. Each source is independent.

#### 2A: Conversation / Transcript Analysis

**Automatic mode**: Extract signals directly from the current conversation. No transcript scanning — the conversation IS the evidence.

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

#### 2B: Reference File Analysis

Read all files in `{skill}/references/` (typically 2-5 files). Flag:
- Empty sections that remain empty after 3+ sessions
- Stale entries (30+ days unmodified) or contradictions with SKILL.md
- Missing learning categories vs. workflow steps
- Batch size mismatches between inline limits and Context Rules

If the skill has no `references/` directory, skip this sub-step.

#### 2C: User Input (Explicit Mode Only)

Ask: **"What's working well? What's not? Anything you always skip or wish was different?"**

Treat response as ground truth — auto-promotes any resulting proposal to HIGH confidence.

#### 2D: Cross-Skill Scan (Explicit Mode Only, or if Gate Signal 4 Triggered)

Compare the target skill against 5 most-recently-used other skills:
- **Duplicate reference detection** — compare reference file content. Flag >70% overlap for consolidation into shared reference.
- **Skill chaining** — check if this skill runs in sequence with others across sessions. If pattern repeats 3+ times, check output/input format alignment at handoff points.
- **Conflict detection** — extract directive statements from SKILL.md files, compare, flag contradictions.
- **Pattern extraction** — when multiple skills solve the same sub-problem differently, propose standardizing on the strongest version.

Findings log as flags in evolution-log.md. Always propose tier, never auto-apply.

### Phase 3: Analyze and Generate Proposals

**Automatic mode**: Load only target SKILL.md + optimizer-observations.md + evolution-log.md trust section. Run only lenses relevant to observed signals.

**Explicit mode**: Load `analysis-heuristics.md`, `evolution-log.md`, and `skill-conventions.md` once at start. Run all eight lenses.

Apply analysis lenses (thresholds and scoring in `analysis-heuristics.md`):

| # | Lens | Key Checks |
|---|------|------------|
| 1 | **Convention Compliance** | Structure vs. `skill-conventions.md`: batch limits, consolidation rules, Context Rules, section order, frontmatter |
| 2 | **Description / Triggers** | Actual trigger phrases vs. listed triggers; CSO anti-pattern (description summarizes workflow) |
| 3 | **Workflow Steps** | Skipped >60% → remove/optional; edited >50% → change default; errors >30% → add handling; unlisted steps → add |
| 4 | **Reference Files** | Empty sections, stale overrides, batch size complaints, rules user manually adds each session |
| 5 | **Edge Cases** | Unhandled situations → add; never-triggered cases → flag as dead weight |
| 6 | **Context Rules** | Premature data drops, batch size issues, file update timing |
| 7 | **Prompt Clarity** | Ambiguous instructions, redundant statements, passive voice, unclear conditionals |
| 8 | **Token Efficiency** | Verbose sections (500+ words), repeated concepts, oversized references (3000+ words without consolidation), tables vs. prose trade-offs |

Filter out previously rejected proposals (see re-proposal rules in `analysis-heuristics.md`).

### Phase 4: Auto-Apply or Propose

#### Auto-Apply Tier

Changes that can be applied without asking. Tier expands based on approval history tracked in evolution-log.md:

- **Tier 0 (always auto-apply)**: Typo fixes, markdown formatting, metadata corrections
- **Tier 1 (graduated)**: Duplicate sentence removal, meaning-preserving compression. Requires 10 consecutive approvals of that change type to graduate.

After auto-applying, show brief confirmation: `Auto-applied: {description}`

#### Propose Tier

Everything else requires user approval:

- **Single proposal** → 2-3 sentence evidence brief with the proposed change
- **Multiple proposals** → Grouped numbered list by target file, sorted by confidence (HIGH first):

```
[n] {file} > {section}: {proposed change}
    Confidence: {level} | Evidence: {source}
```

**Actions:** **Apply {n}** / **Edit {n}** / **Skip {n}** (defer, no log) / **Reject {n}** (log to evolution-log.md) / **Show diff {n}** / **Apply all** / **Done** (unapplied = skipped)

**Navigation:** **Next** / **Back** / **Jump to {n}** / **Show overview** / **Remaining**

If a proposal modifies the skill's `description` field, warn: "This changes skill discovery. Other sessions may find/miss this skill differently."

#### Never Auto-Apply
- Cross-skill consolidation proposals
- Workflow step additions, removals, or reordering
- Description or trigger phrase changes

### Phase 5: Apply Changes

For each approved proposal:
1. Read the target file fresh (not from earlier cache)
2. Apply the edit
3. Show brief confirmation: `Applied [n]: {description}`

If an edit conflicts with the current file state (file changed since Phase 3), warn and ask user to confirm or skip.

### Phase 6: Update State

Update two locations:

#### Per-Skill: `{target-skill}/references/optimizer-observations.md`
- Increment `usage_count`
- Update `last_optimized` (if changes were made) and `last_observed`
- Log friction signals and positive signals from this session
- Clear resolved pending observations
- Consolidation rule: every 5 sessions, stay under 500 words

#### Central: `references/evolution-log.md`
- Log session: date, skill, mode, proposal counts (made/applied/skipped/rejected)
- Update trust calibration: increment approval streaks for applied change types, reset for rejections
- Log any cross-skill flags from Phase 2D
- Consolidation rule: every 10 sessions, stay under 500 words in Recent Sessions

## Context Rules

- **Automatic mode**: Load only target SKILL.md + optimizer-observations.md + evolution-log.md trust section. Do not load analysis-heuristics.md or skill-conventions.md unless proceeding past the gate.
- **Explicit mode**: Load `analysis-heuristics.md`, `evolution-log.md`, and `skill-conventions.md` once at Phase 3 start. Do not reload mid-workflow.
- Drop raw transcript data after signal extraction in Phase 2A. Keep only extracted signals.
- During proposal review (Phase 4), keep only the current proposal and its evidence in active context.
- Update state files only at session end (Phase 6), never mid-workflow.
- Transcript search: max 20 files scanned, max 5 analyzed.
- Read target skill files fresh before each edit in Phase 5 (not from Phase 2 cache).
- One skill per automatic run. Explicit mode also handles one skill per run.
- Silent exit if gate fails (zero user-visible output) — only increment usage_count and update last_observed.

## Edge Cases

- **Skill not found**: List available skills and ask. Do not guess.
- **No transcripts found (explicit mode)**: Proceed with reference files (2B) + user input (2C) only. Note reduced confidence in all transcript-dependent proposals.
- **Skill just created (0 sessions)**: Only Phase 2C (user input) is available. Suggest: "This skill has no usage history yet. Run it a few times first for better analysis."
- **Skill has no references/ directory**: SKILL.md proposals only. Skip Phase 2B entirely.
- **Multiple skills used in one session (automatic mode)**: Analyze the most recently invoked skill only. Others can be analyzed in subsequent automatic runs.
- **Multiple skills used in one session (explicit mode)**: Ask which one to improve. Do not batch.
- **Conflicting proposals**: Surface the conflict explicitly. User chooses one.
- **All proposals rejected**: Log rejections and exit: "No changes applied. Rejected proposals logged for future reference."
- **Previously rejected proposal re-emerging**: Only re-propose if 2+ additional evidence sessions accumulated after rejection. Cap at MEDIUM confidence with note: "Previously rejected on {date}."
- **Auto-apply trust demotion**: If user rejects an auto-applied change, immediately revert that change type to propose tier, reset counter, require 15 consecutive approvals to re-graduate.

## When NOT to Use

- Need to create a new skill from scratch — use `writing-skills`
- Need to debug a skill that's throwing errors at runtime — use `systematic-debugging`
- Need to understand what a skill does — just read its SKILL.md
- Need to test a skill against pressure scenarios — use `writing-skills` (TDD methodology)
