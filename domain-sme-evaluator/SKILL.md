---
name: domain-sme-evaluator
description: "Use when evaluating product artifacts (PRDs, API specs, data models, UIs) through an industry domain lens. Trigger on: 'review this as a travel expert,' 'domain review,' 'does this make sense for our industry,' 'SME evaluation,' 'expert review of this spec,' or when any artifact needs domain-specific scrutiny."
---

# Domain SME Evaluator

## Purpose

Evaluate product artifacts through the eyes of an industry domain expert. Surfaces domain-specific risks, pattern violations, and missed opportunities that generic evaluation frameworks miss.

Load the domain profile and run 5 evaluation passes against the artifact.

## Dependencies

**Tools/APIs:** Confluence (project context search), Jira (related tickets/requirements), GitHub (existing implementations)
**Other Skills:** `company-context` (optional overlay for company-specific context)
**Reference Files:** `references/evaluation-framework.md`, `references/domains/{domain}.md`

## Inputs

- Product artifact to evaluate (PRD, API spec, data model, UI screenshot/mockup, Confluence page URL, or pasted content)
- Domain identifier (e.g., "travel") — must match a profile in `references/domains/`

## Outputs

- **Advisory narrative:** 2-4 paragraph domain expert assessment (authoritative, specific, grounded in industry knowledge)
- **Structured findings:** severity-rated list (Critical / High / Medium / Advisory), each with context, domain rationale, and recommended action
- No numerical scores

## Workflow

### Phase 1: Classify & Load

1. Identify artifact type (PRD, API spec, data model, UI, or mixed)
2. Load `references/domains/{domain}.md`
3. If artifact type is ambiguous, ask the user before proceeding

### Phase 2: Gather Live Context (Parallel) — Full Review Only

Run all three in parallel. Extract domain-relevant context, then drop raw data.

- **2A: Confluence** — Search for related specs, architecture docs, domain glossaries (batch: 10 pages)
- **2B: Jira** — Pull related tickets, requirements, known issues in the domain area (batch: 20 tickets)
- **2C: GitHub** — Search existing implementations for relevant patterns and conventions (batch: 10 files)

### Phase 3: Evaluation Passes

Load `references/evaluation-framework.md` once. Run passes sequentially:

1. **Domain Fit** — Does this artifact align with how this industry works? Are the core concepts right?
2. **Industry Pattern Conformance** — Does it follow established patterns, or reinvent the wheel?
3. **Regulatory & Compliance** — Industry-specific compliance, legal, or regulatory considerations?
4. **Domain Pitfalls** — Known gotchas, edge cases, and failure modes specific to this industry?
5. **Integration Realities** — How does this interact with typical industry systems, standards, and third parties?

Each pass: load relevant section of domain profile, evaluate artifact against those heuristics, generate findings with severity.

### Phase 4: Synthesize

1. Write advisory narrative in domain expert voice
2. Compile structured findings (Critical > High > Medium > Advisory)
3. Each finding: **title**, **severity**, **domain rationale**, **recommended action**
4. Critical/High findings: stop and surface for user decision
5. Medium/Advisory findings: include with recommendation

## Context Rules

- Load domain profile once at Phase 1 — do not reload mid-workflow
- Load evaluation framework once at Phase 3 — do not reload per pass
- Drop raw Confluence/Jira/GitHub data after Phase 2 extraction
- Domain profiles are read-only during evaluation — never modify during a review

## When NOT to Use

- UI-specific heuristic evaluation — use `ui-evaluator`
- Data model standards compliance — use `data-model-reviewer`
- API composability analysis — use `api-composer`
- Modeling the domain itself — use `domain-modeler`
