---
name: output-consistency-reviewer
description: "Use after producing any complex output to catch inconsistencies before sharing. Trigger on: 'review this for consistency,' 'does this make sense,' 'check my logic,' 'anything contradictory,' 'validate this document,' 'sanity check this,' or proactively after completing any multi-section deliverable."
---

# Output Consistency Reviewer

## Purpose
Catch contradictions, logic gaps, unstated assumptions, and constraint violations in complex deliverables before they ship.

## Dependencies

**Other Skills:** mental-models (model definitions loaded during review)

## Inputs
- Completed output to review (document, report, proposal, analysis)
- Stated constraints, goals, or requirements (if any)

## Outputs
- Issue list with severity levels
- Corrected version (for low-stakes issues)
- Stop-and-ask flags (for high-stakes issues)
- Surfaced assumptions for user validation

## Review Passes

### Pass 1: Contradiction Scan
Identify conflicting statements — direct contradictions, implicit contradictions, and tone contradictions across sections.

### Pass 2: Logic Chain Validation
Trace arguments from premises to conclusions. Flag non-sequiturs, assumed causation, and evidence that doesn't support its claim.

### Pass 3: Constraint Check
Cross-reference recommendations against stated constraints, goals, and requirements. Flag violations and unaddressed goals.

### Pass 4: Assumption Surfacing
Identify unstated assumptions — what's taken for granted, what environmental conditions are assumed stable, what behaviors are assumed predictable. Make each explicit.

### Pass 5: Mental Model Integration
Load mental model definitions. If any model surfaces a concern, name the model, explain the insight, recommend action.

## Severity Triage
- **Low-stakes**: Fix and note the change.
- **High-stakes**: Stop and surface for user decision. If being wrong could embarrass, change a decision, or mislead — ask.

## Context Rules
- Load only the output being reviewed and stated constraints. Do not pull in background context.
- Load mental model definitions once at review start. Do not reload mid-workflow.
- Summarize findings concisely — reference sections, don't repeat the full output.

## When NOT to Use
- Reviewing code — use `code-writer` Evaluator Mode.
- Format/tone calibration — use `priority-format-calibrator`.
