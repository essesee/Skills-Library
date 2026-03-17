# System Documentation Templates

## Flow Documentation

```markdown
# {Flow Name}

## Overview
{2-3 sentences: what this flow does, why it exists, who uses it}

## Entry Points
| Entry Point | Trigger | Source |
|-------------|---------|--------|
| {endpoint/handler} | {what triggers it} | {file:line} |

## Flow Diagram
<!-- Mermaid or text-based diagram -->

## Step-by-Step Execution

### Step 1: {Name}
- **What:** {What happens at this step}
- **Where:** `{file:line_number}`
- **Why:** {Rationale — from git history, decision log, or user input}
- **Inputs:** {Data coming in}
- **Outputs:** {Data going out}
- **Error Handling:** {What happens on failure}
- **Side Effects:** {Writes, emits, caches}

### Step 2: {Name}
{Same structure}

## Decision Points
| Condition | Path A | Path B | Rationale |
|-----------|--------|--------|-----------|
| {condition} | {what happens} | {alternative} | {why} |

## External Dependencies
| Dependency | What It Provides | Failure Behavior | SLA/Notes |
|-----------|-----------------|-------------------|-----------|
| {service} | {purpose} | {what happens when it's down} | {notes} |

## Data Flow
{Description of data transformations through the flow}

## Side Effects
| Side Effect | When | Where | Reversible? |
|------------|------|-------|-------------|
| {write/emit/cache} | {trigger condition} | {destination} | {yes/no} |

## Failure Modes
| Failure | Symptoms | Detection | Recovery |
|---------|----------|-----------|----------|
| {what goes wrong} | {what it looks like} | {how to detect} | {how to fix} |

## Known Gaps
- [ ] {Area where "why" is unknown — suggest who to ask (from git blame)}
- [ ] {Undocumented behavior}

## Related
- Decision Records: {links}
- Jira: {ticket links}
- Confluence: {spec/doc links}
- Git History: {key commit references}

## Metadata
- **Last Updated:** {date}
- **Documented By:** {who}
- **Code Commit:** {hash the documentation reflects}
- **Owner Team:** {team}
```

## Integration Documentation

```markdown
# Integration: {System A} <-> {System B}

## Overview
{What this integration does and why it exists}

## Architecture
{Diagram showing the integration boundary}

## Data Contract
### {System A} → {System B}
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| {field} | {type} | {yes/no} | {notes} |

### {System B} → {System A}
{Same format}

## Authentication
{How systems authenticate with each other}

## Error Handling
| Error | Response | Retry? | Alerting |
|-------|----------|--------|----------|
| {error type} | {what happens} | {policy} | {who gets notified} |

## Failure Modes
{What happens when the integration is down}

## Monitoring
{How to tell if the integration is healthy}
```

## Troubleshooting Guide

```markdown
# Troubleshooting: {System/Flow}

## Quick Reference
| Symptom | Likely Cause | Quick Fix |
|---------|-------------|-----------|
| {symptom} | {cause} | {fix} |

## Common Issues

### {Issue 1: Descriptive Title}
**Symptoms:** {What the user/operator sees}
**Likely Cause:** {Root cause}
**How to Diagnose:**
1. {Step}
2. {Step}
**How to Fix:**
1. {Step}
2. {Step}
**Prevention:** {How to avoid this in the future}
**Escalation:** {When to escalate, to whom}

## Diagnostic Commands
| Command | What It Shows | When to Use |
|---------|--------------|-------------|
| {command} | {output description} | {scenario} |

## Health Indicators
| Indicator | Healthy | Unhealthy | Where to Check |
|-----------|---------|-----------|----------------|
| {metric/log} | {normal range} | {concerning range} | {dashboard/log location} |

## Runbooks

### {Scenario: e.g., "High Error Rate"}
1. {Step — check X}
2. {Step — if Y, then Z}
3. {Step — escalate if...}
```

## Knowledge Capture (Collaborative Session)

```markdown
# Knowledge Capture: {Topic}

## What We Know
{Documented understanding from the session}

## What the Code Shows
{Behavior traced from the codebase}

## What {Person} Explained
{Institutional knowledge provided during the session}
- "{Quote or paraphrase}" — {context}

## Gaps Identified
- [ ] {Unknown that needs investigation}
- [ ] {Suspected behavior that needs verification}

## Action Items
- [ ] {Follow-up to fill a gap}
- [ ] {Verification to confirm a hypothesis}
```
