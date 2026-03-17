# Decision Record Template

## Format

Each decision record follows this structure when written to Confluence:

```
## DEC-{YYYY}-{NNN}: {Decision Title}

| Field | Value |
|-------|-------|
| **Date** | {YYYY-MM-DD} |
| **Status** | Active / Superseded by DEC-XXXX / Reversed |
| **Participants** | {Decision makers and key consultees} |
| **Tags** | {domain area, team, decision type} |

### Decision
{Clear, unambiguous statement of what was decided}

### Context
{Why this decision was needed — the problem or question that prompted it}

### Alternatives Considered
1. **{Alternative A}** — {brief description}. Rejected because: {reason}
2. **{Alternative B}** — {brief description}. Rejected because: {reason}

### Rationale
{Why this option was chosen over the alternatives. Include key factors: technical constraints, business requirements, timeline pressure, risk tolerance}

### Constraints
{What limited the options — timeline, budget, technical dependencies, organizational, political}

### Risks & Trade-offs
{What we're accepting by making this decision. Known downsides.}

### Reversibility
{Easy / Moderate / Hard} — {What it would take to reverse this decision}

### Related Work
- Jira: {ticket links}
- Confluence: {spec/doc links}
- Roadmap: {item references}
```

## ID Generation

- Format: `DEC-{YYYY}-{NNN}` where NNN is zero-padded sequential within the year
- On first use: start at DEC-{current_year}-001
- On subsequent uses: increment from the last ID found on the Confluence page

## Status Values

| Status | Meaning |
|--------|---------|
| **Active** | Currently in effect |
| **Superseded** | Replaced by a newer decision (link to it) |
| **Reversed** | Decision was reversed (explain why in a note) |

## Tag Taxonomy (extensible)

### Domain Area
- booking, search, auth, payments, integrations, infrastructure, data, admin, member-portal

### Team
- platform, backend, frontend, data-engineering, devops

### Decision Type
- architecture, product, process, tooling, vendor, policy
