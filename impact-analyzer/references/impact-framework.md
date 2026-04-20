# Impact Assessment Framework

## Dimensions

Every impact assessment considers these dimensions. Not all apply to every assessment — skip irrelevant ones.

### 1. Breadth
How many users/sessions/transactions are affected?

| Scale | Threshold | Label |
|-------|-----------|-------|
| Widespread | >10% of active users | Critical |
| Significant | 5-10% of active users | High |
| Moderate | 1-5% of active users | Medium |
| Limited | <1% of active users | Low |

### 2. Depth
How severely are affected users impacted?

| Scale | Description | Label |
|-------|------------|-------|
| Broken | Core functionality completely fails | Critical |
| Degraded | Functionality works but poorly (slow, errors, workarounds needed) | High |
| Inconvenient | Minor friction, easy workaround | Medium |
| Cosmetic | Visual only, no functional impact | Low |

### 3. Frequency
How often does this occur?

| Scale | Description | Label |
|-------|------------|-------|
| Every session | Affects every user every time | Critical |
| Frequent | Multiple times per day per user | High |
| Occasional | Weekly or less | Medium |
| Rare | Edge case, hard to reproduce | Low |

### 4. Revenue / Business Impact
Does this affect the business bottom line?

| Scale | Description | Label |
|-------|------------|-------|
| Direct revenue loss | Bookings failing, payments broken | Critical |
| Indirect revenue risk | Customer churn risk, SLA violations | High |
| Efficiency loss | Internal workflow slower, support load higher | Medium |
| Negligible | No measurable business impact | Low |

### 5. Trend
Is this getting better or worse?

| Direction | Signal |
|-----------|--------|
| Getting worse | Error rate increasing, more customer complaints over time |
| Stable | Consistent rate, no change |
| Improving | Error rate decreasing, fewer complaints |
| New | Just started, insufficient data for trend |

### 6. Persona Impact
Which user types are affected?

| Persona | Description |
|---------|------------|
| End Users (Members) | Travel club members using booking/search |
| Agents | Club agents managing bookings |
| Back Office | Internal operations team |
| Partners | External API consumers |
| Engineering | Developer experience impact |

## Severity Calculation

Combine the highest-severity dimension ratings:

| Overall Severity | Criteria |
|-----------------|----------|
| **Critical** | Any dimension rated Critical, OR 3+ dimensions rated High |
| **High** | 2+ dimensions rated High, none Critical |
| **Medium** | 1 dimension High, OR 3+ dimensions Medium |
| **Low** | All dimensions Medium or Low |

Trend acts as a modifier:
- Getting worse: bump severity up one level
- Improving: note but don't reduce severity (the current impact still stands)

## Framing Templates

### For Leadership
```
**Impact:** {X users affected} ({Y}% of active users) over the last {N} days.
**Business risk:** {revenue/SLA/churn implication}
**Trend:** {direction with data}
**Recommendation:** {action level — investigate / prioritize / monitor}
```

### For Engineering
```
**Scope:** {X events}, {Y unique users}, {Z}% error rate on {endpoint/flow}
**Baseline comparison:** {current vs. normal}
**Affected segments:** {persona/geography/club breakdown}
**Trend:** {daily chart description}
```

### For Clubs
```
**What's affected:** {description in their workflow terms}
**How many:** {agent/member count in their terms}
**Workaround:** {if available}
**Resolution:** {timeline/status}
```
