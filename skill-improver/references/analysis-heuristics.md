# Analysis Heuristics

*Tunable thresholds, signal keywords, confidence rules, and transcript search strategy for skill improvement analysis.*

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

## Confidence Scoring

| Level | Criteria |
|-------|----------|
| HIGH | 3+ sessions with consistent signal, OR explicit user statement (Phase 2C), OR clear single-session pattern with strong evidence |
| MEDIUM | 2 sessions with consistent signal, OR moderate behavioral signal in 1 session |
| LOW | 1 session only, OR ambiguous signal. Never auto-applied to core workflow changes |

### Modifiers
- User verbal input (Phase 2C) → auto-promotes to HIGH
- Previously rejected proposal → capped at MEDIUM, even with new evidence
- Post-session mode (current conversation only) → treated as 1 strong session; can reach HIGH if user explicitly confirms

## Transcript Search Strategy

### Search Location
`.jsonl` files in `~/.claude/projects/-Users-jesseshedd-Documents-work/`

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

- Rejected proposals logged in `improvement-log.md` with date and reason
- NOT re-proposed unless 2+ additional evidence sessions accumulate after rejection date
- Re-proposed at MEDIUM max confidence with note: "Previously rejected on {date}."
- If rejected twice, requires explicit user request to reconsider
