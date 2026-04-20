# OODA Re-Orientation

When executing multi-task plans, the workflow is linear by default. OODA
checkpoints prevent plan drift when reality diverges from assumptions.

## Between-Task Checkpoint
After completing any task in a multi-task plan, before starting the next:
1. What did we learn that wasn't in the plan?
2. Do remaining tasks still make sense?
3. Have any assumptions been invalidated?
If all clean, proceed. If not, surface the re-orientation to the user before
continuing.

## Session-Level Checkpoint
At natural milestones (completing a major chunk, context switching):
1. Are we still solving the right problem?
2. Has the broader context shifted since we started?
Acknowledge context shifts and save in-progress state before pivoting.

## Scope
- Hypothesis-first stays default for bounded, reversible tasks.
- OODA operates at coordination boundaries (between tasks, between
  workstreams), not within tasks.
- For high-stakes irreversible decisions, orient before forming a hypothesis.
- Existing verification loops (TDD, debugging, verification-before-completion)
  cover step-level feedback. OODA operates one level up.
