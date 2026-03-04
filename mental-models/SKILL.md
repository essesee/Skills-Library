---
name: mental-models
description: "Use when the user faces a complex decision, needs to evaluate a proposal, stress-test reasoning, or assess risk. Trigger on: 'should we,' 'what are the risks,' 'evaluate this proposal,' 'think this through,' 'challenge my reasoning,' 'what am I missing,' 'decision framework,' 'stress test this.'"
---

# Mental Models Reasoning Engine

## Purpose
Apply proven mental models to complex decisions. Surface only models that reveal something useful — don't force-fit.

## Dependencies

**Reference Files:** `references/models.md` (10 model definitions, loaded at skill trigger)

## Modes

### Analysis Mode
Run a problem through the 10 models. Skip models that add no insight.

For each relevant model:
- Name the model explicitly
- State the specific concern or insight
- Rate confidence: high / medium / low
- Recommend action if applicable

### Challenge Mode
User presents their reasoning. Stress-test it:
- Push back on gaps and hidden assumptions
- Surface unexamined second-order effects
- Identify reasoning by analogy instead of first principles
- Name the model that surfaces each concern

### Decision Framing
For multi-option decisions:
- Run each option through the relevant models
- Produce a comparative analysis with confidence levels per option
- Recommend a path with stated assumptions

## Context Rules
- Load `references/models.md` at skill trigger.
- Skip models that add no insight to the current problem.
- Always name the model when surfacing a concern.

## When NOT to Use
- Simple factual questions that don't involve tradeoffs.
- Requests for code, data queries, or other execution tasks.
