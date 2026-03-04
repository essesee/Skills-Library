---
name: ui-evaluator
description: "Use when reviewing a UI for usability, accessibility, or design consistency before shipping. Trigger on: 'review this design,' 'is this accessible,' 'audit the UI,' 'check usability,' 'heuristic evaluation,' 'WCAG compliance,' 'is this ready to ship,' or any request to validate a UI against best practices."
---

# UI Evaluator

## Purpose
Run systematic UI evaluations across usability, accessibility, and design frameworks, producing an actionable scorecard with fix recommendations.

## Dependencies

**Tools/APIs:** Figma (design file access)
**Other Skills:** ui-mockup-generator (can evaluate its output or feed fix suggestions back)
**Reference Files:** `references/nng-heuristics.md` (Nielsen heuristics), `references/wcag-aa.md` (AA criteria), `references/wcag-aaa.md` (AAA criteria), `references/gestalt-fitts.md` (Gestalt + Fitts's Law), `references/mobile-heuristics.md` (mobile-specific)

## Inputs
- Screenshots, live URLs, Figma frames, or ui-mockup-generator output

## Outputs
- Heuristic evaluation report
- Accessibility audit (AA baseline + AAA gap analysis)
- Design consistency check
- Overall scorecard: usability score (1-10), AA pass/fail with issue count, AAA gap summary, top 5 issues by severity, what's working well
- Fix recommendation per flagged issue (feedable back into ui-mockup-generator)

## Workflow

Run each pass in order, loading the relevant reference file for that pass.

### Pass 1: NN/g Heuristic Evaluation
Evaluate all 10 Nielsen heuristics. Load `references/nng-heuristics.md` for criteria and common violations. Flag violations with severity and fix recommendations.

### Pass 2: WCAG Accessibility Audit
AA as baseline (must pass), then AAA gap analysis. Load `references/wcag-aa.md` and `references/wcag-aaa.md` for criteria. Label each issue as AA failure vs. AAA improvement opportunity.

### Pass 3: Gestalt & Fitts's Law Analysis
Evaluate proximity, similarity, continuity, visual hierarchy, target sizes, action placement, and edge/corner leverage. Load `references/gestalt-fitts.md` for criteria.

### Pass 4: Information Architecture
- Content organization logical?
- Labels clear and unambiguous?
- Navigation patterns consistent?
- Cognitive load reasonable?

### Pass 5: Mobile-Specific Heuristics
Evaluate thumb zones, scroll depth, touch gestures, bottom sheets, and keyboard behavior. Load `references/mobile-heuristics.md` for criteria.

### Pass 6: Design System Consistency
Cross-check against the Figma library / design system profile: component usage, color palette, typography scale, spacing grid.

## Context Rules
- Load one reference file per pass — do not load all at once.
- Complete one framework pass before loading the next.
- Parse screenshots to structured text. Drop images after parsing.
- Scorecard is the final deliverable — summarize findings concisely, not repeated verbatim.

## When NOT to Use
- Need to generate a UI, not evaluate one — use `ui-mockup-generator`.
- Copy/content review, not usability — use `voice-analyzer` or `style-editor-expanded`.
