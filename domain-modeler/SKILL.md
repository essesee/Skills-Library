---
name: domain-modeler
description: "Model domains, identify bounded contexts, design aggregates, run event storming sessions."
---

# Domain Modeler

## Purpose
Apply Domain-Driven Design to model complex domains, identify clean service boundaries, and design aggregates that enforce business invariants. Grounded in real system context — always searches existing code, docs, and tickets before modeling.

## Dependencies

**Tools/APIs:** GitHub, Confluence, Jira
**Other Skills:** ubiquitous-language-builder (call when terminology needs alignment)
**Reference Files:** `references/ddd-patterns.md` (loaded on skill trigger)

## Context Gathering (Parallel)

Before modeling, gather real context. Run all three searches in parallel; summarize findings before proceeding. Flag surprises or contradictions. Drop raw results after summarizing.

a. **GitHub** (max 5 queries): service/repo names matching domain concepts, schema definitions, API contracts and integration points, event/message/topic definitions
b. **Confluence** (max 3 queries): architecture decision records, domain glossaries, feature specs describing domain rules
c. **Jira** (max 3 queries): epics/stories revealing domain boundaries, bug patterns hinting at aggregate boundary problems, feature requests revealing unstated concepts

## Inputs
- Domain description, feature requirement, or existing system to analyze
- Optionally: specific repos, Confluence spaces, or Jira projects to focus on

## Outputs
- Bounded context map with relationships (grounded in actual system state)
- Aggregate definitions with invariants
- Domain event catalog
- Recommendations — validated against what exists, with migration path if changes are needed

## Modes

### Event Storming
Lightweight event storming. Pause and validate with the user after each step.

1. **Domain Events** — past tense, business language. Search code for existing event classes/types.
2. **Commands** — what triggered each event? Cross-reference with API endpoints and Jira.
3. **Aggregates** — which entity owns each command? Check existing entity/model definitions.
4. **Bounded Contexts** — group related aggregates. Validate against actual repo/service boundaries.
5. **Context Relationships** — how do contexts communicate? Search for integration points, cross-service calls, shared databases.

### Context Mapping
For existing systems or when bounded contexts are already identified:

1. List all bounded contexts from actual repo structure and service inventory.
2. For each pair, identify the relationship type (see `ddd-patterns.md` > Context Relationship Types). Flag Shared Kernel as risk.
3. Flag problematic relationships: shared databases, circular dependencies, missing ACLs.

### Aggregate Design
For a specific bounded context, define aggregates (see `ddd-patterns.md` for tactical patterns and design heuristics):

1. **Aggregate Root** — search code for existing root entities.
2. **Entities & Value Objects** — identify each; check existing definitions.
3. **Invariants** — business rules the aggregate must enforce. Cross-reference Jira for bug reports revealing broken invariants.
4. **Domain Events** — events emitted on state change. Check existing event infrastructure.
5. **Consistency Boundary** — transactionally consistent vs. eventually consistent.

Challenge: Is the aggregate too large? Does existing code match the ideal boundary?

### Quick Check
For a single design question ("should X and Y be in the same bounded context?"):
- Search for how X and Y exist in code and docs today
- Apply DDD concepts with real context
- Give a clear recommendation with reasoning

## When NOT to Use
- Pure CRUD with no business logic or invariants.
- Simple data transformation with no domain complexity.
- API design without domain modeling (use api-composer instead).

## Context Rules
- Load `references/ddd-patterns.md` only on skill trigger.
- Run context gathering searches in parallel.
- Drop raw search results after summarizing; keep only findings.
- Search for what currently exists before recommending any boundary change.
- Provide a migration path when recommendations conflict with the current system.
- Flag divergences between code structure and domain model in docs/tickets.
- Surface terminology mismatches between code, Jira, and Confluence.
- Use domain language, not technical jargon, for naming events and aggregates.
- In Event Storming mode, work iteratively; do not dump the full model at once.
