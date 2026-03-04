---
name: data-model-reviewer
description: "Use when reviewing schemas, designing data exchange formats, evaluating API contracts, or checking standards compliance. Trigger on: 'review this schema,' 'does this follow UBL,' 'data model review,' 'standardize this format,' 'travel data structure,' 'exchange format,' 'document schema,' or any data modeling question involving business documents or travel data."
---

# Data Model Reviewer

## Purpose
Review and design data models against UBL patterns and industry standards. Ensures your data exchange formats are well-structured, standards-aligned, and maintainable. Grounded in what actually exists in the codebase.

## Dependencies

**Tools/APIs:** GitHub, Confluence, Jira
**Other Skills:** api-composer (call when review reveals composition problems), ubiquitous-language-builder (call when field naming is inconsistent)
**Reference Files:** `references/ubl-travel-patterns.md` (loaded on skill trigger)

## Context Gathering (Parallel)

Search for real context before reviewing or designing. Run all three in parallel; summarize findings before proceeding. Drop raw results after summarizing.

a. **GitHub** (max 5 queries): schema definitions (JSON Schema, OpenAPI, Protobuf, DB migrations), data model classes/types, serialization code, external provider integrations, shared types across services
b. **Confluence** (max 3 queries): data dictionaries, partner/provider data specs, integration docs, data flow diagrams
c. **Jira** (max 3 queries): data mapping bugs, schema migration tickets, partner integration issues, feature requests requiring new fields

## Inputs
- A schema, data model, or API contract to review
- Or: a domain area to design data models for
- Or: a specific standards compliance question

## Outputs
- Schema review with issues flagged by severity
- UBL alignment assessment
- Recommendations with migration path
- Field-level analysis when relevant

## Modes

### Schema Review
Review an existing schema against best practices and UBL patterns:

1. **Structure Check** — entity/value object separation, ID consistency, ISO 8601 dates, monetary values (amount + currency), address structure.
2. **UBL Alignment** — UBL document types where they fit, common components (Party, Address, Period, Amount, Quantity), standard code lists. See `ubl-travel-patterns.md`.
3. **Travel Industry Specifics** — product type categorization, booking/reservation lifecycle states, provider format abstraction, traveler info structure.
4. **Practical Issues** — nullable vs. required intentionality, versioning strategy, backward compatibility.

### Design Guidance
Design new data models for a given domain:

1. Search existing models in the codebase first — extend, don't reinvent
2. Identify applicable UBL document types and components
3. Propose a schema that:
   - Follows existing codebase conventions
   - Aligns with UBL where practical
   - Handles the known use cases from Jira/Confluence
4. Flag trade-offs (UBL purity vs. practical simplicity)

### Standards Check
For a specific question ("should this field be X or Y?"):
- Check UBL/ISO standards for guidance
- Check existing codebase conventions
- Give a direct recommendation with reasoning

### Migration Review
When a schema change is proposed:
1. Identify all consumers of the current schema (search code)
2. Assess backward compatibility
3. Propose a migration strategy (versioning, deprecation period, adapter layer)
4. Cross-reference with Jira for related tickets

## Context Rules
- Load `references/ubl-travel-patterns.md` only when this skill triggers.
- Run context gathering searches in parallel. Drop raw results after summarizing.
- Always search for current consumers before recommending schema changes.
- Show practical benefit when recommending UBL alignment — don't standardize for standardization's sake.
- Flag where existing schemas diverge from each other across repos.
- Recommend an anticorruption layer for non-standard provider data rather than polluting internal models.
- For large schema reviews, work section by section.

## When NOT to Use
- API design without data modeling — use `api-composer`.
- Field naming alignment — use `ubiquitous-language-builder`.
- Pure code refactoring with no schema impact.
