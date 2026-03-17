# Dependency Tracker Configuration

## Team Mappings
<!-- Fill in on first run -->
- **Your team's Jira project keys:**
- **Key dependency teams:**
  <!-- Format: team_name: project_key(s), slack_channel, key_contacts -->

## Staleness Thresholds
- **Stale:** 3 days without activity on blocking ticket
- **At Risk:** 7 days without activity
- **Critical:** 14 days without activity

## Jira Link Types to Track
- "blocks" / "is blocked by" (primary)
- "depends on" (secondary)
- "relates to" (weak signal — track but don't alert)

## Escalation Rules
- **First nudge:** Stale threshold reached. Casual Slack DM to assignee.
- **Second nudge:** At Risk threshold. Firm follow-up to assignee + their lead.
- **Escalation:** Critical threshold. Message to engineering leadership with business impact.
- **Cool-down:** Don't nudge the same person about the same ticket more than once per 3 days.

## Escalation Contacts
<!-- Fill in on first run -->
<!-- Format: team_name: escalation_contact, channel -->

## Patterns and Learning
<!-- Tracked over time -->
### Frequently Blocking Teams
<!-- team: count of blocking instances, average resolution time -->

### Nudge Effectiveness
<!-- date, ticket, nudge type, outcome (resolved/no response/escalated) -->

## Session Log
<!-- Date, dependencies tracked, actions taken -->
