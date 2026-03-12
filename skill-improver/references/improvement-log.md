# Improvement Log

*Tracks proposals made, applied, skipped, and rejected per skill. Consolidate every 10 sessions. Stay under 2,000 words.*

## Recent Sessions

### 2026-03-12 — Full Library Review (all 39 skills)
Mode: explicit (batch)
Proposals: ~25 | Applied: ~25 | Skipped: 0 | Rejected: 0

**Deleted 9 skills** (redundant with native Claude Code or folded into defaults):
- `ambiguity-handler` → folded into global CLAUDE.md Default Behaviors
- `output-consistency-reviewer` → folded into global CLAUDE.md Default Behaviors
- `priority-format-calibrator` → folded into global CLAUDE.md Default Behaviors
- `mental-models` → extracted to `~/.claude/references/mental-models.md`, referenced from global CLAUDE.md
- `code-writer` → redundant with native capabilities
- `deployment-assistant` → redundant with native git capabilities
- `qa-testing` → redundant with `superpowers:test-driven-development`
- `ui-mockup-generator` → redundant with `frontend-design` superpower
- `jira-template-builder` → replaced by `user-story-writer` (tracker-agnostic templates)

**Edited skills:**
- `global CLAUDE.md`: Added Default Behaviors section (Ambiguity Handling, Output Consistency, Format Calibration, Mental Models Reference)
- `company-context/SKILL.md`: Fixed frontmatter name (`tst-context` → `company-context`), fixed internal paths (`tst-context/references/` → `company-context/references/`)
- `company-context/references/jql-overrides.md`: Added board filter with Team ID
- `problem-discoverer/SKILL.md`: Simplified — made scanners optional, removed structural analysis, inlined mental-models/ambiguity-handler references
- `domain-sme-evaluator/SKILL.md`: Slimmed down — removed Quick Check/Create Profile modes, removed mental-models dependency
- `discover-design-deliver/SKILL.md`: Replaced `ambiguity-handler` reference with inline guidance
- `ticket-proposer/SKILL.md`: Replaced all `jira-template-builder` references with `user-story-writer`
- `user-story-writer/SKILL.md`: Added TDD follow-up suggestion at Step 5
- `voice-analyzer/SKILL.md`: Added Step 6 iterative refinement flow
- `daily-planner/SKILL.md`: Updated dependencies — delegates replies to `message-drafting`, removed `jira-template-builder`

**Key pattern**: User prefers fewer skills with clear scope over many narrow skills. Behavioral defaults belong in global CLAUDE.md, not standalone skills.

## Consolidated Patterns

<!-- After 10 sessions, consolidate recurring themes here:

### Commonly Applied
- {pattern}: {which skills, how often}

### Commonly Rejected
- {pattern}: {why users reject this}

### Cross-Skill Trends
- {observation spanning multiple skills}
-->
