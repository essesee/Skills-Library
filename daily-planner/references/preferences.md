# Daily Planner — Learned Preferences

## Priority Signals
- Production bugs affecting customers: P0 — always surface (actioned MBS XML bug immediately)
- External partner emails needing reply: P1 — actioned Allianz touchbase same session
- Jira AC gaps: actioned — user writes AC and pushes to Jira directly
- Stale epics blocked on backend work: skip — user knows the status, don't nag
- Calendar accept/decline notifications: noise — never surface
- Jira status update emails (moved to In Progress, etc.): FYI only — don't surface as action items

## Slack Relevance

### High-Priority Channels
- #po: high — Launch Lab coordination, team announcements

### High-Priority People
- Arthur Banks: high — data/reporting, Looker dashboards
- Cory Figueroa: high — checkout work, AB testing
- Michael Henderson: high — VP Eng, escalation path
- Kevin Truong: medium — FE work, trip builder
- George Porrata: medium — BE work, agent attribution
- Firdous Qazi: medium — config, partner issues
- Jack Henahan: medium — engineering

### Relevance Keywords
- checkout: high — active epic TST-348 + AB flip work
- MBS / manual booking: high — epics TST-444, TST-35 + active production issue
- trip builder / trip details: medium — epic TST-332
- validation endpoints: low — epic TST-425 (Not Ready)
- Allianz / insurance / trip protection: medium — active partner thread
- PostHog / A/B testing / experimentation: medium — car checkout flip + Launch Lab topic
- platform analytics / dashboard: medium — roadmap item
- consumer hotel: high — active epic TST-348
- proposal PDF: medium — TST-3600 active story
- vendor code: medium — TST-3882 active story
- agent attribution / agent ID: medium — TST-3053 chain

### Negative Signals (Noise)
- Calendar accept/decline notifications: always skip
- Jira status transitions (Ready -> In Progress): FYI, don't surface as action items
- Stale epics user already knows are blocked: skip

## Communication Preferences
- Slack (internal): very casual, lowercase, short sentences, no punctuation, direct
- Email (external/partners): warm but short, "Regards, JB", doesn't explain reasoning back to the other person, quick acks
- Prefers drafts over direct sends for email
- Jira comments: uses skills to draft AC, pushes directly when approved
- Voice correction: don't over-explain or be analytical in replies — keep it natural

## Jira Focus
- TST-348 (Checkout Consumer Hotel): active — checks stories, writes AC
- TST-444 (MBS Current State): blocked on backend — skip until unblocked
- TST-35 (MBS New Booking Details): blocked on backend — skip until unblocked
- TST-425 (Validation Endpoints): backlog — skip
- TST-332 (Trip Builder Refinement): backlog — skip
- Gap types actioned: missing AC (wrote AC for TST-4331)
- Gap types skipped: unestimated, stale epics with known blockers

## Roadmap URL
https://docs.google.com/spreadsheets/d/1YEtn6-x6B6OdZETmNUG47jDrrw5HUbhXdrP3hNEXwaM/edit?usp=sharing

## Session Log
<!-- Consolidate every 10 sessions. Target: <3,000 words total. Next consolidation: session 11. -->
- 2026-03-03: First run. Bootstrapped preferences. 6 actioned, 7 skipped, 5 deferred. User acts on production bugs, partner emails, AC gaps. Skips stale epics with known blockers. Casual Slack voice, warm-but-short email voice. Corrected email draft twice for voice — don't over-explain.
