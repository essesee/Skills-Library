---
name: discover-design-deliver
description: "Guide product development through discovery, design, and delivery phases. PRDs, scoping, phase checks."
---

# Discover, Design, Develop, Deliver (4D Process)

## Purpose
A product development lifecycle. The 4D process emphasizes customer problem identification and collaborative design and delivery. Each diamond phase follows a divergent-convergent approach — expanding to explore possibilities, then narrowing to a specific outcome that initiates the next phase.

The four phases:
1. **Discover**: Research customer behavior, define the problem or opportunity
2. **Design**: Explore and define solutions to the identified problem
3. **Develop**: Build the solution with specified goals
4. **Deliver**: Test and deliver the solution to provide business value

**Inflection points between phases:**
- Customer Signals → triggers Discover
- Problem Statement (approved) → gates Discover → Design
- PRD (approved) → gates Design → Develop/Deliver
- Business Value → completes the cycle

## Dependencies

**Tools/APIs:**
- Jira — existing tickets, related epics, customer feedback
- Confluence — specs, research docs, prior art
- GitHub — existing code, related features, technical feasibility
- PostHog — usage data, feature adoption metrics, funnel analysis

**Other Skills:**
- `domain-modeler` — when the feature touches domain boundaries
- `api-composer` — when new service integrations are needed
- `ubiquitous-language-builder` — when new domain concepts are introduced
- `data-model-reviewer` — when data exchange formats are involved
- `problem-discoverer` — may hand off pre-compiled evidence packages

**Reference Files:**
- `references/problem-statement-template.md` — Problem Statement structure and checkpoints
- `references/prd-template.md` — PRD structure and sections

## Context Gathering (Parallel — Always Do First)

Search all four sources simultaneously before starting any phase. Batch: Jira 20, Confluence 10, GitHub 20, PostHog 5 queries.

- **2A: Jira** — Existing tickets/epics, customer-reported issues, past attempts, related bugs
- **2B: Confluence** — Prior research, specs, design docs, meeting notes
- **2C: GitHub** — Existing code in this area, related PRs, technical constraints
- **2D: PostHog** — Current usage patterns, drop-off/friction points, feature adoption

Present a summary of prior art before starting. Don't repeat work already done.

## Phase 1: Discover

**Goal**: Validate the problem is real, worth solving, and clearly articulated.

**Methodologies**: User interviews, data analytics (PostHog), vendor meetings for integration understanding.

**Artifact: Problem Statement** (required to exit Discover). Use `references/problem-statement-template.md`. Must include: problem articulation, personas, examples, 5 Whys (down to root cause AND up to business case), and conclusion.

**Approval gate**: Problem Statement approved by Product Team and relevant stakeholders. On approval, move Idea from Research to Discovery in Jira.

## Phase 2: Design

**Goal**: Develop a validated solution to the approved problem.

**Methodologies**: Design workshops, user testing with prototypes, prompt framing for AI-assisted content.

**Artifact: PRD** (required to exit Design). Use `references/prd-template.md`. Must include: executive summary, problem statement, goals, persona impact (before/after), background research, recommendations (MVP + success metrics + rollout), appendix.

**Approval gate**: PRD approved by Advisory Group and Product Team.

## Phase 3: Develop

Build the PRD solution via 2-week sprints. Work intake: roadmap initiatives, production bugs, tech requirements, security/compliance. Artifacts: Jira work items delivered biweekly.

## Phase 4: Deliver

Release and validate business value. Review with stakeholders by work type (roadmap → stakeholders, bugs → reporters, tech → leads, security → DevOps).

## Modes

### Full Lifecycle
Walk through all four phases. Pause at each gate for approval.

### Write Problem Statement
Generate a Problem Statement using the template. Load `references/problem-statement-template.md`. Search Jira, PostHog, and Confluence for evidence. Output a versioned document ready for review.

**Evidence package handoff**: When called by `problem-discoverer`, this mode may receive a pre-compiled evidence package containing signal summaries, quantitative data, persona impact assessments, and trend analysis. When an evidence package is provided:
- Skip context gathering for areas already covered by the package (don't re-search what's already been found)
- Use the package evidence to populate the Problem Statement template directly
- Still run context gathering for any gaps — if the package lacks PostHog data or Confluence prior art, gather those independently
- Attribute evidence sources in the Problem Statement (e.g., "per customer signal scan," "per bug pattern analysis")

### Write PRD
Generate a PRD using the template. Load `references/prd-template.md`. Build on the approved Problem Statement. Search GitHub for existing systems. Output a versioned document ready for review.

### Phase Check
Assess where a feature currently is:
1. Search Jira for the epic/tickets
2. Search Confluence for specs/docs
3. Search GitHub for PRs/code
4. Determine: Discovery done? Design done? Develop in progress? Delivered?
5. Flag gaps — did we skip a phase? Missing approval?

### Quick Scope
For small features: compressed discovery, lightweight design, ticket creation with acceptance criteria.

## Validation Rules
- Never skip Discover → Design → Develop → Deliver sequence
- Problem Statements require the 5 Whys pattern in BOTH directions (down to root cause, up to business case)
- PRDs must include Background Research on existing systems before Recommendations
- If prior art exists, build on it — don't start from zero
- Always search PostHog for usage data before sizing impact
- When a build reveals a design gap, pause. Don't power through.
- Version documents (v1, v2 [WIP], etc.) — preserve history

## Context Rules
- Load reference templates only when entering the phase that uses them (problem-statement at Phase 1, prd at Phase 2). Do not preload both.
- Run context gathering searches in parallel. Drop raw search results after summarizing.
- At each gate, present a clear go/no-go recommendation with evidence.
- When calling other skills, let them run their full workflow — do not abbreviate.

## When NOT to Use
- **Ill-defined problem** — ask clarifying questions to narrow scope before starting
- **Bug triage or backlog cleanup** — use `backlog-groomer` or `bug-consolidator`
- **Status reporting on existing work** — use `atlassian:generate-status-report`
- **Domain modeling without a product development context** — use `domain-modeler` directly
