---
name: api-composer
description: "Design APIs, evaluate service integrations, analyze data pipelines, and check composability between systems."
---

# API Composer

## Purpose
Apply category theory thinking (composability, morphisms, functors) to design APIs and service integrations that compose cleanly. Not abstract math — practical patterns for building systems where pieces fit together without friction.

## Dependencies

**Tools/APIs:** GitHub, Confluence, Jira
**Other Skills:** domain-modeler (call when service boundaries need DDD analysis first)
**Reference Files:** `references/composition-patterns.md` (loaded on skill trigger)

## Context Gathering (Parallel)

Search for real context before analyzing or designing. Run all three in parallel; summarize findings before proceeding. Drop raw results after summarizing.

a. **GitHub** (max 5 queries): API definitions (OpenAPI, route files, controllers), transformation code (mappers, serializers, adapters), integration points (HTTP clients, message producers/consumers), shared types/contracts
b. **Confluence** (max 3 queries): API docs and integration guides, data flow diagrams, external provider contracts
c. **Jira** (max 3 queries): integration bugs, data mapping issues, cross-service workflow requests

## Inputs
- Services, APIs, or data flows to analyze or design
- Optionally: specific repos, endpoints, or transformation chains to focus on

## Outputs
- Composition analysis: does it compose? Where are the breaks?
- API design recommendations grounded in what exists
- Transformation chain diagrams (text-based)
- Migration paths when recommending changes

## Modes

### Composition Check
Analyze whether a set of services/APIs compose cleanly:

1. **Identify objects** — services and types involved.
2. **Map morphisms** — transformations connecting them.
3. **Test composition** — chain A->B->C; trace data at each step.
4. **Find breaks** — information loss, type mismatches, ordering dependencies.
5. **Check identity** — round-trip preservation.

For each break: what breaks, practical impact, fix (see `composition-patterns.md` > Common Composition Failures).

### API Design
Design new APIs or redesign existing ones for composability:

1. **Search existing APIs** first — extend, don't reinvent.
2. **Define types** — input, output, error for each endpoint.
3. **Ensure composition** — response types usable as inputs to the next call?
4. **Check functor property** — does structure survive across domain mappings?
5. **Design for identity** — serialize/deserialize without loss? Canonical forms?

### Pipeline Analysis
For data transformation pipelines (e.g., data flowing through your system):

1. **Map the full chain** — from source to destination, every transformation step
2. **Check each arrow** — is the transformation well-defined? Total or partial? What happens on failure?
3. **Check composition** — does the pipeline produce the same result regardless of how you group the steps?
4. **Find lossy steps** — where does information get dropped or mangled?
5. **Identify coupling** — which steps assume things about other steps?

### Quick Check
For a specific question ("can service A call service B's output directly?"):
- Search for the actual types/contracts in code
- Analyze compatibility
- Give a direct answer with evidence

## Context Rules
- Load `references/composition-patterns.md` only when this skill triggers.
- Run context gathering searches in parallel. Drop raw results after summarizing.
- Always search existing APIs before designing new ones.
- Show current state and proposed state side by side when recommending changes.
- Flag where existing code already composes well — don't fix what isn't broken.
- Cross-reference composition breaks in production code with Jira bugs.
- Use text-based diagrams for transformation chains: `A --f--> B --g--> C`

## When NOT to Use
- Domain modeling without API focus — use `domain-modeler`.
- Pure CRUD with no composition concerns.
- Single-service internal refactoring with no integration points.
