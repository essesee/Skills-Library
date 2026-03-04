# Domain Profile Template

Use this template when creating a new domain profile at `references/domains/{domain}.md`.

```markdown
# {Domain Name} Domain Profile

## Industry Overview

{1-2 paragraphs: what this industry does, key players, business model, value chain}

## Core Domain Concepts

{Key terminology and concepts an SME would know — bulleted list with brief definitions}

- **{Term}** — {definition}
- **{Term}** — {definition}

## Established Patterns

{How things are typically done in this industry — architectural patterns, data flows, business processes}

### Data & Architecture
- {pattern}: {description}

### Business Processes
- {process}: {how it typically works}

### Standards & Protocols
- {standard}: {what it covers, when to use}

## Anti-Patterns & Pitfalls

{Known mistakes, common failures, things that seem right but aren't}

- **{Pitfall}** — {why it's wrong, what happens}

## Regulatory & Compliance

{Industry-specific regulations, standards, certifications that affect product design}

- **{Regulation/Standard}** — {what it requires, who it applies to, consequences of non-compliance}

## Industry Systems & Integrations

{Third-party systems, standards bodies, data formats, protocols common in this industry}

- **{System/Standard}** — {what it does, how products typically integrate}

## Evaluation Heuristics

{Domain-specific questions to ask when reviewing any artifact}

### For PRDs
- {question to ask}

### For API Specs
- {question to ask}

### For Data Models
- {question to ask}

### For UIs
- {question to ask}
```

## Guidelines for Profile Authors

- **Be specific over comprehensive.** A focused profile with 20 sharp heuristics beats an encyclopedia. Prioritize the things people actually get wrong.
- **Ground in real failures.** Anti-patterns should come from things that have actually gone wrong, not theoretical concerns.
- **Include the "obvious" stuff.** What's obvious to an SME is invisible to a generalist. Spell it out.
- **Name systems and standards explicitly.** Don't say "industry standard protocol" — say "IATA NDC" or "HL7 FHIR."
- **Update as you learn.** Profiles should grow from evaluation findings. When a review surfaces a new pitfall, add it.
