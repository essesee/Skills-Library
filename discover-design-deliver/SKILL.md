---
name: discover-design-deliver
description: "Guide product development through discovery, design, and delivery phases. PRDs, scoping, phase checks."
---

# Discover, Design, Develop, Deliver (4D Process)

## Purpose
A product development lifecycle emphasizing customer problem identification and collaborative design and delivery.

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
- `references/prd-template.md` — PRD structure (unified template, PRD tail)
- `references/idea-template.md` — Idea structure (unified template, Idea tail)
- `references/epic-template.md` — Epic structure (unified template, Epic tail)

All three artifact templates share a core (Problem, Desired End State, Principles, Out of Scope, Why This Matters) and add type-specific tails. When an initiative produces more than one artifact, the shared core should be mirrored across them. Source of truth: `~/.claude/projects/-Users-you-work-project/memory/feedback_unified_artifact_template.md`.

## Context Gathering (Parallel — Always Do First)

Search all four sources simultaneously before starting any phase. Batch: Jira 20, Confluence 10, GitHub 20, PostHog 5 queries.

- **2A: Jira** — Existing tickets/epics, customer-reported issues, past attempts, related bugs
- **2B: Confluence** — Prior research, specs, design docs, meeting notes
- **2C: GitHub** — Existing code in this area, related PRs, technical constraints
- **2D: PostHog** — Current usage patterns, drop-off/friction points, feature adoption

Present a summary of prior art before starting. Don't repeat work already done.

## Phase 1: Discover

**Goal**: Validate the problem is real, worth solving, and clearly articulated.

**Hypothesis framing** (before gathering evidence): State the bet. What outcome would confirm this problem is worth solving? What would falsify it? What's the minimum data point that would change the decision to pursue? Is this a watch signal (ongoing) or a verdict question (one-time answer)? If the user already has a clear problem statement, confirm the hypothesis in 1-2 sentences. If vague, this framing IS the discovery starting point.

**Artifact: Problem Statement** (required to exit Discover). Use `references/problem-statement-template.md`. Must include: problem articulation, personas, examples, 5 Whys (down to root cause AND up to business case), and conclusion.

**Approval gate**: Problem Statement approved by Product Team and relevant stakeholders. On approval, move Idea from Research to Discovery in Jira.

## Phase 2: Design

**Goal**: Develop a validated solution to the approved problem.

**Artifact: PRD** (required to exit Design). Use `references/prd-template.md`. The PRD follows the unified template: shared core (Problem, Desired End State, Persona Impact, Goals, Principles, Out of Scope, Why This Matters) plus PRD-specific tails (Standard Template, Rollout Mechanics, Success Metrics, Stakeholders, Open Questions, Related Work).

**Paired artifacts**: Initiatives that rise to the PRD level usually also have a parent Idea in MDP (use `references/idea-template.md`) and an implementing Epic in your Jira project (use `references/epic-template.md`). The shared core (Problem, Desired End State, Principles, Out of Scope, Why This Matters) should be mirrored verbatim across all three. Write the core once and copy it across.

**Approval gate**: PRD approved by Advisory Group and Product Team.

## Phase 3: Develop

Build the PRD solution via 2-week sprints. Work intake: roadmap initiatives, production bugs, tech requirements, security/compliance. Artifacts: Jira work items delivered biweekly.

## Phase 4: Deliver

**Goal**: Release the solution and validate business value.

Release and review with stakeholders by work type (roadmap → stakeholders, bugs → reporters, tech → leads, security → DevOps).

**Post-ship evaluation**: After the feature has been live long enough to generate data (typically 1-2 weeks), invoke `/post-ship-retro` to close the learning loop. Compare PostHog adoption data against the PRD's success metrics and the Phase 1 hypothesis. Capture surprises and learnings in memory so they inform future initiatives.

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
Generate a PRD using the unified template. Load `references/prd-template.md`. Build on the approved Problem Statement. Search GitHub for existing systems. Output a versioned document ready for review. If the initiative also has a parent Idea or implementing Epic, offer to mirror the shared core sections into those artifacts at the end.

### Write Idea
Generate an MDP Idea using the unified template. Load `references/idea-template.md`. Appropriate when an initiative needs a strategic narrative for the roadmap or advisory group before (or alongside) a full PRD. If a PRD exists, the Idea's shared core should match the PRD's.

### Write Epic
Generate an Epic description using the unified template. Load `references/epic-template.md`. Appropriate when an initiative is moving into engineering execution. The Epic should link to its parent Idea and PRD in the header, and its shared core sections should match them verbatim.

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
- Load reference templates only when entering the phase or mode that uses them (problem-statement for Phase 1 / Write Problem Statement, prd for Phase 2 / Write PRD, idea for Write Idea, epic for Write Epic). Do not preload all four.
- When producing more than one artifact for the same initiative, draft the shared core sections once and copy them across the artifacts to keep them aligned.
- Run context gathering searches in parallel. Drop raw search results after summarizing.
- At each gate, present a clear go/no-go recommendation with evidence.
- When calling other skills, let them run their full workflow — do not abbreviate.

## When NOT to Use
- **Ill-defined problem** — ask clarifying questions to narrow scope before starting
- **Bug triage or backlog cleanup** — use `backlog-groomer` or `bug-consolidator`
- **Status reporting on existing work** — use `atlassian:generate-status-report`
- **Domain modeling without a product development context** — use `domain-modeler` directly
