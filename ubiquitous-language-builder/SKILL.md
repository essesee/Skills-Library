---
name: ubiquitous-language-builder
description: "Use when terminology is inconsistent between code, docs, and business language, or when building a domain glossary. Trigger on: 'define the language for,' 'align terminology,' 'what do we call,' 'glossary,' 'naming is inconsistent,' 'terminology audit,' 'domain language,' or when other skills surface terminology confusion."
---

# Ubiquitous Language Builder

## Purpose
Build and enforce a shared vocabulary between business stakeholders, documentation, and code. When the code says "TravelProduct" but Jira says "Offering" and Confluence says "Package" — that's a bug in the language, and it causes real bugs in the system.

## Dependencies

**Tools/APIs:** GitHub, Confluence, Jira
**Other Skills:** domain-modeler (call when terminology confusion reveals a bounded context problem)

## Context Gathering (Parallel)

Search all three sources for the domain area being audited. Run in parallel; present findings as a terminology comparison before making recommendations. Drop raw results after building the comparison table.

a. **GitHub** (max 5 queries): class/type names, variable/field names, API endpoint and field names, enum values, doc comments
b. **Confluence** (max 3 queries): spec/design doc terminology, existing glossaries, external-facing docs (what customers/partners call it)
c. **Jira** (max 3 queries): ticket titles and descriptions, acceptance criteria language, bug reports stemming from naming confusion

## Inputs
- A domain concept or area to audit (e.g., "what do we call the thing an agent books?")
- Or: a broader scope ("audit all terminology in the booking domain")
- Or: triggered by another skill that surfaced a naming conflict

## Outputs
- Terminology comparison table: concept → code name → doc name → Jira name → recommendation
- Mismatches flagged with severity (cosmetic vs. likely-to-cause-bugs)
- Recommended canonical terms with rationale
- Migration checklist if renaming is needed

## Modes

### Concept Audit
For a single domain concept:

1. Search all sources for how the concept is referenced
2. Build a comparison table:

| Source | Term Used | Where Found |
|--------|-----------|-------------|
| Code (repo X) | `TravelProduct` | `src/models/TravelProduct.ts` |
| Code (repo Y) | `Offering` | `src/types/offering.go` |
| Confluence | "Travel Package" | Product Spec v2.3 |
| Jira | "product," "offering," "package" | Various tickets |

3. Flag mismatches and assess severity:
   - **High**: Different terms in code across services (causes integration bugs)
   - **Medium**: Code and docs diverge (causes misunderstanding, wrong implementations)
   - **Low**: Jira informal usage differs (cosmetic, but worth standardizing)
4. Recommend a canonical term with rationale

### Domain Glossary
Build a full glossary for a bounded context:

1. Identify all key domain concepts by scanning code models, Confluence specs, and Jira epics
2. For each concept, run the Concept Audit process
3. Compile into a structured glossary:
   - Term
   - Definition (in business language)
   - Code representation (class/type name)
   - Aliases (other names used, flagged for eventual cleanup)
   - Bounded context it belongs to
4. Present for review before recommending changes

### Conflict Resolution
When two teams or services use different terms for the same thing:

1. Identify the conflict scope — is it within one bounded context or across contexts?
2. If within one context: one term must win. Recommend based on:
   - Which term is closer to business language?
   - Which term is more widely used?
   - Which term is more precise?
3. If across contexts: different terms may be correct (DDD allows this). Check whether an anticorruption layer exists to translate between them.

### Quick Check
For a single naming question ("should we call this X or Y?"):
- Search for existing usage of both terms
- Give a direct recommendation with evidence

## Context Rules
- Run all source searches in parallel. Drop raw results after building comparison table.
- Always show current usage across all sources before recommending a term change.
- Estimate blast radius (files/repos) when a term change requires code changes.
- Cross-reference with bounded contexts — the same concept can legitimately have different names in different contexts.
- Flag when customer/partner-facing terms differ from internal terms (matters for APIs).
- Keep glossary outputs compact — link to sources rather than embedding full context.

## Edge Cases
- Same concept legitimately has different names in different bounded contexts (DDD anti-corruption layer) — this is correct, not a mismatch.

## When NOT to Use
- Bounded context boundary problems, not just naming — use `domain-modeler`.
- Naming conflict is purely in code with no business terminology involved — standard refactoring.
