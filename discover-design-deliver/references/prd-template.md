# Product Requirements Document (PRD) Template

Follows the unified artifact template (shared core plus PRD-specific tails). Use this for any PRD. The same Problem, Desired End State, Principles, Out of Scope, and Why This Matters content should be mirrored into the parent Idea (in roadmap project) and the implementing Epic (in Jira).

## Workflow

This template produces a formal deliverable for stakeholder approval. Follow this skill-assisted workflow:

1. **Before writing** — Run `ambiguity-handler` on the approved Problem Statement and any design workshop outputs. Surface unclear requirements, undefined terms, and scope boundaries before drafting.
2. **During Goals and Standard Template sections** — Run `mental-models` to stress-test. Inversion: how could this fail? Second-Order Thinking: what happens after the first effect? Probabilistic Thinking: are the success metrics realistic?
3. **During Problem and Desired End State** — Apply Chesterton's Fence (`mental-models`): understand why existing systems work the way they do before proposing changes.
4. **After drafting** — Run `output-consistency-reviewer` across the full document. Check that the Standard Template serves the stated Principles, Success Metrics measure the Goals, and nothing in the Rollout Mechanics contradicts the Out of Scope section.
5. **Final edit** — Run `style-editor-expanded` in editing mode. This goes to the advisory group — apply all six passes.
6. **Format check** — Run `priority-format-calibrator` to calibrate depth per section.

---

## Version

`v1 [WIP]` — Increment on each revision. Mark `[WIP]` until approved.

---

## Shared Core (mirror to Idea and Epic)

The first five sections are shared verbatim with the parent Idea (in roadmap project) and the implementing Epic (in Jira). Write them once, then copy across artifacts. Small voice adjustments are fine but the content should agree.

### Problem

What's broken, who it hurts, why it matters now.

For compounding issues, use numbered or named sub-points. Name the forcing functions explicitly (framework end-of-life, compliance deadline, contractual dependency) because executives need those for prioritization.

Link to any root-cause analyses, research docs, or prior art that support the problem statement.

### Desired End State

What "done" looks like from the user's perspective. One or two paragraphs. Concrete enough that a reader can visualize the future state. Avoid implementation language.

### Persona Impact

For each affected persona, show the quote and the outcome. Use company-context personas where they apply (Tricia, Barbra, Support Team, Back Office, Engineering Teams). Pull quotes from Slab or other canonical persona documentation.

### Goals

Business outcomes this initiative targets. Each goal should be:

- Specific (what changes)
- Measurable (maps to a Success Metric below)
- Time-bound or outcome-bound

Goals are outcomes. Principles (next section) are guard rails. They are different.

> **mental-models checkpoint**: Apply Inversion — for each goal, ask "what would cause us to fail at this?" If there's no answer, the goal may not be specific enough.

### Principles

The guard rails that shape every decision downstream. Four to six principles is usually right. These are shared verbatim with the Idea and the Epic.

Format each as a bold lead sentence plus a short elaboration. The lead is the rule, the elaboration is what it means in practice and what it explicitly rejects.

> **output-consistency-reviewer checkpoint**: Does every principle appear in the Standard Template section as a concrete design choice? If a principle shows up here but nowhere in the template, either the principle is aspirational or the template is under-specified.

### Out of Scope

What this initiative explicitly does not do. Include the reason for each exclusion so future readers understand whether it's deferred, separately owned, or permanently out.

### Why This Matters

Business value and strategic fit. One or two paragraphs. Why this initiative, and why now. Name the forcing function if there is one.

---

## PRD-Specific Tails

These sections live only in the PRD. They are the design reference and the operational reference.

### Standard Template

The detailed design reference the engineering team builds against. Include:

- **Page structure** — section order, defaults, which sections are gated
- **Interaction patterns** — form behavior, validation sequencing, navigation
- **Third-party integrations** — what your platform owns versus what the vendor widget owns
- **Audience and context variations** — how the same template serves consumers and agents, different product types, or different tiers
- **Per-product deltas** — where product-specific fields slot in without breaking the shared shape

The Standard Template is the PRD's biggest section. It's the reason engineers read the PRD. Write it precisely.

### Rollout Mechanics

How the initiative ships to users. Include:

- **Audience and segment mapping** — which audiences, which cohorts, which feature flags
- **Audience order** — which audiences first and why
- **Product or surface rollout** — which surfaces, in what order, and what triggers each step (platform-scheduled, demand-driven, etc.)
- **Turn-on model** — per-customer opt-in versus data-gated global turn-on versus scheduled flip
- **Performance gate** — what metrics must clear before the next step
- **Evaluation cadence** — how long between checkpoints

### Success Metrics

Quantitative measures tied to Goals. For each metric, include:

- **Name**
- **Current baseline**
- **Target or gate criteria**
- **Measurement method and source** (PostHog event, support ticket query, etc.)
- **Instrumentation dependencies** — if a metric can't be measured today, say so and flag it in Open Questions

> **output-consistency-reviewer checkpoint**: Does every Goal have at least one Success Metric? Does every Success Metric map to a Goal?

### Stakeholders and Approval Gates

Who reviews, who approves, and who runs the rollout.

- **Internal reviewers and approvers** (product team, advisory group, relevant leadership)
- **Executive sponsor** — the named individual accountable for the initiative
- **Customer organizations participating** — the clubs, partners, or teams who are directly involved in the rollout

Use `company-context` for the company-specific defaults on reviewers and approval gates.

### Open Questions

Unresolved items that need answers. For each:

- The question
- Why it matters (which decision or metric it blocks)
- What the answer would enable

Name the ones that are currently blocking measurement or decision-making. These are often the most exec-relevant section of the PRD.

### Related Work

Upstream dependencies, downstream consumers, and related initiatives.

- **Upstream** — what has to exist for this to function
- **Downstream** — what becomes possible once this lands
- **Parallel** — other initiatives that share infrastructure or audience with this one

Include explicit Jira or MDP identifiers when available.

---

## Final Review Checklist

Before submitting for approval, verify:

- [ ] `ambiguity-handler` — All ambiguous requirements resolved
- [ ] `mental-models` — Goals stress-tested, failure modes identified, existing systems understood
- [ ] `output-consistency-reviewer` — No contradictions between sections, all goals have metrics, all principles show up in the Standard Template
- [ ] `style-editor-expanded` — Six-pass edit complete
- [ ] `priority-format-calibrator` — Depth matches complexity per section
- [ ] The shared core (Problem, Desired End State, Principles, Out of Scope, Why This Matters) matches the parent Idea and the implementing Epic

## Approval

- **Reviewers**: Advisory group, product team
- **Gate**: Approved PRD initiates Develop/Deliver phases

## Reference

- Unified template source of truth: `~/.claude/projects/-Users-you-work-project/memory/feedback_unified_artifact_template.md`
- Reference implementation: PROJ-5 Example Epic (Epic), ROADMAP-35 (Idea), Confluence page (PRD)
