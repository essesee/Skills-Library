---
name: ui-mockup-generator
description: "Use when the user needs to visualize a feature concept, create a mockup, prototype a UI, or iterate on an existing screen. Trigger on: 'mock this up,' 'prototype this feature,' 'what would this look like,' 'create a UI for,' 'show me a design for,' 'build a mockup,' 'iterate on this screen.'"
---

# UI/Product Mockup Generator

## Purpose
Go from concept to prototype using the team's component library and design system.

## Dependencies

**Tools/APIs:** Figma API (component library read)
**Other Skills:** code-writer (generating functional HTML/React prototypes)

## Inputs
- Plain-language description, screenshots of existing UI (for iteration), Figma library reference (cached)

## Outputs
- Functional HTML/React prototype, responsive variants (desktop + mobile), annotated specs, exportable screenshots

## Modes

### Description-to-Mockup
1. Load cached design system profile (colors, typography, spacing, components).
2. Generate high-fidelity prototype using actual design system components.
3. Produce desktop and mobile variants by default.

### Screenshot-to-Iteration
Parse screenshot into structured text. Drop image from context. Apply described changes. Generate updated prototype.

### Annotation Mode
Add product-intent annotations explaining why each element exists. Useful for designer handoff and stakeholder reviews.

### Export Options
Live HTML preview, static screenshot (for Slack/decks), structured spec (for designer handoff).

## Context Rules
- Cache design system profile. Do not re-read Figma library each time.
- Parse screenshots to structured text, then drop images from context.
- Do not evaluate UI in this skill — use `ui-evaluator` for evaluation.
- One screen at a time in context during generation.

## When NOT to Use
- Evaluating existing UI — use `ui-evaluator`.
- Generating production code — use `code-writer`.
