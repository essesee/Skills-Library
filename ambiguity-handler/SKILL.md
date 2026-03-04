---
name: ambiguity-handler
description: "Use at the start of any complex or vague task, when scope is unclear, terms are undefined, or context is missing. Trigger on open-ended requests like 'help me with X,' 'figure out,' 'look into,' 'do something about,' or when you detect unstated assumptions, missing context, or scope that could go multiple directions."
---

# Ambiguity Handler

## Purpose
Catch ambiguity early and mid-task. Vague scoping leads to wasted work.

## Inputs
- Task description (pre-task) or in-progress work where uncertainty has surfaced (mid-task)

## Outputs
- Clarifying questions (one at a time), documented assumptions with rationale, risk flags

## Behavior

### Pre-Task Clarity Check
Scan for: vague scope, undefined terms, unstated assumptions, missing context, multiple valid interpretations. Generate clarifying questions one at a time — prioritize the question that would most change the approach.

### Mid-Task Halt
If ambiguity surfaces during execution: stop immediately, surface the specific uncertainty ("I'm about to decide X, but it could also be Y"), wait for the answer.

### Assumption Documentation
For low-stakes tasks: document every assumption and rationale, present before delivering, flag which assumptions would significantly change the result if wrong.

### Risk Flagging
Always flag proactively: logical gaps, potential failure modes, dependencies that might not hold, scope creep risks.

## Context Rules
- Lightweight — no reference files. Logic is procedural.
- Minimal context footprint — just the task description and surfaced uncertainties.
- One question at a time — do not overload the user.

## When NOT to Use
- Reviewing completed outputs — use `output-consistency-reviewer`.
- Clear, specific requests with no ambiguity.
