---
name: qa-testing
description: "Use when the user needs to test a feature, generate test plans, run tests, check for regressions, or identify edge cases. Trigger on: 'test this feature,' 'generate a test plan,' 'run the tests,' 'check for regressions,' 'QA this ticket,' 'what edge cases am I missing,' or any testing and quality assurance task."
---

# QA & Testing

## Purpose
Generate test plans from requirements (not code), run tests in batches, catch visual regressions, and produce QA documentation.

## Dependencies

**Tools/APIs:** Jira API (read ticket descriptions and AC)
**Other Skills:** code-writer (local environment and repo context)

## Inputs
- Jira ticket with feature description + AC, local environment, before/after screenshots (for visual regression)

## Outputs
- Test plan, automated test results, visual regression report, edge case list, QA summary attachable to Jira

## Workflow

### Step 1: Generate Test Plan
Derive from Jira ticket description and AC — not from code. Structure: happy path, error/failure, boundary conditions, integration touchpoints.

### Step 2: Run Automated Tests
Execute in batches by suite (unit, integration, e2e). Return results per batch, drop batch before loading next.

### Step 3: Visual Regression
Diff before/after screenshots locally. Send only changed regions (cropped) for analysis. Flag layout shifts, color changes, missing elements.

### Step 4: Edge Case Generation
Standalone focused prompt: feature spec in, edge cases out. Cover null/boundary/concurrency/failure/permission/locale edge cases.

### Step 5: QA Summary
Test coverage, pass/fail results, visual regression findings, edge cases, severity, go/no-go recommendation.

## Context Rules
- Derive test plans from ticket description and AC only, not from full codebase.
- Run tests in batches. Drop batch before loading next.
- Send only cropped changed regions for visual diffs, not full screenshots.
- Generate edge cases as a standalone pass, not combined with test execution.

## When NOT to Use
- Writing production code — use `code-writer`.
- Evaluating UI design — use `ui-evaluator`.
