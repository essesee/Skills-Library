# Morning Routine Configuration

## VIP List
Shared with `comms` skill — see `skills/comms/references/vip-config.md`

## Sections
All sections are skippable. Default order (data-first, then planning):

1. Prior Work Review
2. Platform Health
3. Industry Briefing
4. Daily Planning
5. Communications Triage

## Phase 1 Parallel Agents

| Agent | Sources | Skill/Tool |
|-------|---------|------------|
| Calendar + meetings | Google Calendar, Circleback | gcal_list_events, Circleback API |
| Sprint/project state | Jira | JQL search |
| Channel activity | Slack | slack_search, slack_read_channel |
| Email scan | Gmail | gmail_search_messages |
| Platform health | PostHog, Jira | platform-health skill (orchestrated mode) |
| Industry briefing | Web, Slack, Gmail | competitive-intel-scanner, vendor-signal-scanner, customer-signal-scanner, web search |
| Prior work | Local | Memory files, git log, open worktrees |
| Comms triage | Gmail, Slack | Email + Slack scan with VIP filter |

## Preference Learning
Absorbed from daily-planner. Preferences stored in `references/preferences.md`.
Updated at session end based on user actions during walkthrough.

## Industry Briefing Sources
- Web search: travel industry news, industry news
- competitive-intel-scanner: travel tech competitor activity
- vendor-signal-scanner: vendor/provider signals
- customer-signal-scanner: club escalations and pain signals (partner organization focus)

## Roadmap Source of Truth
Platform roadmap in Google Drive. Cached in `references/roadmap-cache.md`.
Refreshed if >24h old or on user request.
