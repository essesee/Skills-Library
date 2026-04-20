---
name: tradeoff
description: "Pressure-test a prioritization or trade-off decision using a structured ladder, adversarial review, and alternative exploration. Use when the user says 'I'm deciding between,' 'which should we,' 'help me prioritize,' 'trade-off,' 'weigh the options,' 'what should I cut,' 'should we do X or Y,' or describes choosing between 2-4 competing options. Also use when the user has a pre-held lean and wants it challenged."
---

# Trade-off Analyzer

## Purpose

Decisions get made in the user's head, in 1:1s, or in Slack threads. Claude gets pulled in after to produce artifacts. This skill moves Claude upstream into the decision itself: pressure-testing framing, surfacing blind spots, exploring alternatives, and running adversarial review before the call is made.

This is not a decision-maker. the user decides. This skill makes sure the decision survives scrutiny first.

## Dependencies

**Other Skills:**
- `brainstorming` (via `superpowers:brainstorming`) — dispatched in Phase 4 for alternative framing exploration
- `bmad-review-adversarial-general` — dispatched in Phase 4 for adversarial attack on each option
- `decision-logger` — offered in Phase 5 to capture the final decision with full context

**Reference Files:**
- `references/tradeoff-ladder.md` — the four-rung evaluation ladder (80/20, first principles, reversibility, downside avoidance) plus the argument trust hierarchy

## Inputs
- **Options** (required): 2-4 candidates being weighed. Can be initiatives, features, approaches, scope cuts, hiring decisions, anything with competing paths.
- **Context** (gather if missing): what the user is optimizing for, constraints (timeline, budget, political), deadlines, who's asking or affected, any pre-held lean.

## Outputs
- Ladder results table (each option scored against each rung)
- Key unknown status (identified, verified or still open)
- Alternative framings explored (from brainstorming agent)
- Adversarial findings (from adversarial agent)
- Single recommendation with the main trade-off stated in one sentence

---

## Workflow

### Phase 1: Capture the Decision

Accept options and context from the user. If anything critical is missing, ask **one question at a time**:

1. What are the options? (required)
2. What are you optimizing for? (required)
3. What constraints apply (timeline, budget, dependencies, politics)? (required)
4. Do you have a lean? (optional — skip if the user volunteers it or if it's obvious)
5. Who's affected by this decision? (optional — skip unless stakeholder impact is central)

Stop after question 3 if context is sufficient. the user's bias is toward action; respect it.

### Phase 2: Surface the Key Unknown

Before running the ladder, ask:

> "What's the one thing you'd want to know before deciding this?"

Three paths from the answer:
- **Cheaply verifiable** (can check with a tool call): offer to verify right now. If the answer changes the decision, you just saved the full ladder walk.
- **Not cheaply verifiable but important**: note it as an open unknown that the ladder results should be read against.
- **"Nothing, I just want the pressure test"**: proceed directly to Phase 3.

### Phase 3: Run the Trade-off Ladder

Walk `references/tradeoff-ladder.md` rung by rung. For each rung, evaluate every option. Show reasoning briefly (1-2 sentences per cell), then state the verdict. Keep it direct — no hedging.

**Rung 1: 80/20** — Is there a way to get most of both? If yes, present it and ask if the dilemma is resolved. If resolved, skip to Phase 5.

**Rung 2: First Principles** — Restate what the user is actually optimizing for (strip the surface framing). Eliminate options that don't serve the real objective.

**Rung 3: Reversibility** — Which options can be unwound? At what cost? Favor reversible over permanent.

**Rung 4: Downside Avoidance** — Compare failure modes directly: "If option A fails, the result is X. If option B fails, the result is Y." Which failure is more survivable?

Present as a compact table:

| | 80/20 | First Principles | Reversibility | Downside |
|---|---|---|---|---|
| Option A | ... | ... | ... | ... |
| Option B | ... | ... | ... | ... |

### Phase 4: Parallel Pressure-test

Dispatch two agents in parallel. Each receives: the options, the optimization target, constraints, and the Phase 3 ladder table.

1. **Brainstorming agent** — explore framings the user hasn't considered. Look for option 3/4/5 outside the stated set. Check for reframings that collapse the trade-off. Apply "What else could work?" (Anchoring pushback from CLAUDE.md). Return: alternative framings as a bullet list, each with a one-sentence rationale.

2. **Adversarial agent** (`bmad-review-adversarial-general`) — attack each option with its failure mode, hidden assumptions, counter-arguments, and second-order effects. Check for optimism bias: "What are you underestimating here?" Return: top 2-3 risks per option as a table, not a narrative.

Wait for both to return before presenting.

### Phase 5: Verdict

Present all findings together:

1. **Ladder table** from Phase 3
2. **Key unknown** status (resolved, still open, or N/A)
3. **Alternative framings** from brainstorming (if any are viable, highlight them)
4. **Adversarial findings** (top 2-3 risks per option, not exhaustive lists)
5. **Recommendation** — state which option and the main trade-off being accepted. One sentence. Direct verdict first; do not qualify with "it depends," "both have merits," or "there's no wrong answer." If the ladder genuinely doesn't differentiate, say that plainly instead of hedging.

Then ask: "Ready to decide? If so, I'll offer to log it via `decision-logger`."

If the user decides:
- Invoke `decision-logger` in Suggest Mode with pre-filled context from the full /tradeoff session (options, rationale, alternatives considered, risks accepted).

If the user wants to iterate:
- Return to any phase. Phase 2 and 3 are the most common re-entry points.

---

## Context Rules
- Phase 1 context gathering uses only what's in the conversation. Do not pull Jira/Slack/PostHog unless the user points at a specific source.
- During Phase 3 ladder walk, keep `references/tradeoff-ladder.md` loaded. Drop Phase 1 raw context once the table is built.
- Phase 4 agents run in parallel. Each gets: the options, the optimization target, and constraints. Neither sees the other's output.
- Phase 5 synthesis loads both agent results. Drop individual agent context after presenting the combined verdict.
- If called from another skill (e.g., `backlog-groomer` surfaces a prioritization call), accept pre-filled options/context and skip redundant Phase 1 questions.

## Edge Cases
- **Only one option presented:** "That's not a trade-off. What's the alternative you're weighing it against?" If there truly is no alternative, this skill is wrong — use the domain skill directly.
- **More than 4 options:** "Too many to pressure-test well. Which 2-3 are the real contenders?" Help the user narrow before running the ladder.
- **the user already decided and wants validation:** Run the ladder anyway. If it agrees, say so briefly. If it disagrees, flag the gap without being preachy.
- **High-stakes irreversible decision:** Add a step between Phase 4 and 5: explicitly check whether any of the user's CLAUDE.md blind spots apply (Anchoring, Optimism Bias, Skipping Context, Staying Too High-Level).
- **Phase 4 agents return conflicting advice:** Surface the conflict directly. Don't resolve it — present both reads and let the user weigh them.

## When NOT to Use
- **Reversible, low-stakes choices** — the user's default mode (hypothesis-first, bias to action) handles these faster. Don't slow him down.
- **Single-option decisions** — nothing to trade off. Use the relevant domain skill.
- **Pure execution questions** ("how do I do X") — wrong skill entirely.
- **Brainstorming without constraints** — use `brainstorming` standalone. /tradeoff assumes options exist and need evaluation.
