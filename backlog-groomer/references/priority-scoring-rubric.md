# Priority Scoring Rubric

Reference file for the backlog-groomer skill. Weighted scoring formula for priority rebalancing (Step 5). Applied to non-bug tickets only.

## Scoring Dimensions

| Dimension | Weight | Source |
|-----------|--------|--------|
| Current priority | 20% | Jira priority field |
| Business alignment | 25% | Epic/roadmap alignment, labels |
| Age | 15% | Days since created |
| Blocking impact | 20% | Number of downstream blocked tickets |
| Sprint proximity | 10% | In current sprint vs. backlog |
| User impact | 10% | Labels, components, description keywords |

## Divergence Flags

Flag tickets where computed priority significantly diverges from Jira priority:

| Flag | Condition |
|------|-----------|
| **Over-prioritized** | Jira says Critical/High but composite score suggests Medium/Low |
| **Under-prioritized** | Jira says Low/Lowest but composite score suggests High/Critical |

## Scoring Notes

- Normalize each dimension to a 1-5 scale before applying weights.
- Business alignment: score higher if the ticket's epic aligns with current roadmap priorities or has strategic labels.
- Blocking impact: each downstream blocked ticket adds +0.5 to raw score (cap at 5).
- Sprint proximity: In current sprint = 5, in next sprint = 3, in backlog = 1.
- Age: Older tickets score higher (they've been waiting longer). Scale: 0-7 days = 1, 8-30 = 2, 31-60 = 3, 61-90 = 4, 90+ = 5.
