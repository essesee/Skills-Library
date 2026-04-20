# Requirements Synthesis Patterns

## Analysis Framework

### Need Decomposition
For each stakeholder request, decompose into:
1. **Stated Want** — what they asked for (their words)
2. **Underlying Need** — what problem they're solving
3. **Success Criteria** — how they'd know it's working
4. **Constraints** — limitations they mentioned or implied

### Overlap Detection
- **Same need, different expression:** Different stakeholders describe the same underlying need differently. Map to a canonical need.
- **Complementary needs:** Needs that don't conflict and can coexist in one solution.
- **Partial overlap:** Same general area, but different specific requirements.

### Conflict Detection
- **Mutually exclusive:** Can't have both. Requires a decision.
- **Resource competition:** Both are possible but compete for the same effort. Requires prioritization.
- **Contradictory constraints:** "Must be simple" vs. "must handle all edge cases."

## Conflict Resolution Heuristics

When conflicts arise, apply in order:

1. **Can we satisfy both?** Is there a design that handles both needs? (80/20 approach)
2. **Which affects more users?** Breadth of impact.
3. **Which is more critical?** Depth of impact — broken vs. inconvenient.
4. **Which aligns with strategy?** Roadmap and business priority.
5. **Which is reversible?** Favor the option that's easier to change later.
6. **Escalate:** If none of the above resolves it, surface to the user for a judgment call.

## Coverage Scoring

For each stakeholder, calculate coverage:

```
Coverage % = (needs addressed by solution / total needs identified) * 100
```

Weight by need priority:
- HIGH need addressed = 3 points
- MEDIUM need addressed = 2 points
- LOW need addressed = 1 point
- Need partially addressed = half weight

```
Weighted Coverage = (weighted points addressed / total weighted points) * 100
```

## Solution Design Patterns

### Configuration-Based
When stakeholders want the same feature but with different settings.
- Example: "Club A wants email notifications, Club B wants Slack" → configurable notification channel

### Tiered Approach
When stakeholders have different depth requirements.
- Example: "Club A needs basic reports, Club B needs detailed analytics" → basic tier + premium tier

### Phased Delivery
When all needs are valid but can't ship simultaneously.
- Example: MVP covers shared needs, Phase 2 adds stakeholder-specific enhancements

### Abstraction
When specific requests point to a more general capability.
- Example: "Club A wants booking export, Club B wants itinerary export" → general data export framework

## Communication Framing

When explaining the synthesized solution to each stakeholder:

1. **Acknowledge their request** — "You asked for X"
2. **Show how it's addressed** — "The solution includes Y, which gives you X"
3. **Be honest about differences** — "We adjusted Z because [reason]"
4. **Highlight what they get beyond their ask** — "You also get W, which addresses [related need]"
5. **Be transparent about exclusions** — "We chose not to include Q because [reason]"
