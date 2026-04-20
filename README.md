# Skills Library

A library of 40 Claude Code skills and 4 behavioral rules for product management, engineering, and leadership workflows.

## What Are Skills?

[Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills) are structured prompts that teach Claude how to perform specific workflows. Each skill is a `SKILL.md` file with optional reference materials. When Claude Code detects a matching trigger phrase, it loads the skill and follows its instructions.

Skills are different from one-off prompts. They encode repeatable processes, quality standards, and domain knowledge so Claude performs complex workflows consistently.

## Installation

**Full library:**

```bash
git clone https://github.com/essesee/Skills-Library.git ~/.claude/skills
```

**Cherry-pick individual skills:**

Copy any skill directory (e.g., `user-story-writer/`) into your `~/.claude/skills/` folder.

**Rules (optional):**

Copy `rules/` into `~/.claude/rules/`. These load automatically every session and provide behavioral defaults (ambiguity handling, output format, assumption discipline, etc.).

## Structure

Each skill follows this layout:

```
skill-name/
  SKILL.md                    # Main skill definition (trigger, workflow, inputs/outputs)
  references/                 # Supporting materials (optional)
    templates.md
    patterns.md
    examples.md
```

Skills reference each other by name. For example, `user-story-writer` can call `company-context` for persona definitions.

## Skill Catalog (40 skills)

### Communication and Drafting (8 skills)

| Skill | Description |
|-------|-------------|
| message-drafting | Triage inbox, draft email or Slack replies, process unread messages |
| comms | Orchestrated communication triage: scan email and Slack, rank by priority and staleness, draft responses |
| stakeholder-update | Send tailored project status updates to stakeholders via Slack or email |
| change-communicator | Draft audience-specific comms for shipped features, deprecations, rollouts, or breaking changes |
| style-editor-expanded | Edit or generate formal documents, reports, proposals with editorial discipline |
| technical-to-business-translator | Translate technical concepts for business audiences or business requests for technical teams |
| voice-analyzer | Build/update voice profiles for drafting communications in the user voice |
| release-notes | Summarize delivered work as polished release notes or changelog for publishing |

### Product and Discovery (11 skills)

| Skill | Description |
|-------|-------------|
| discover-design-deliver | Guide product development through discovery, design, and delivery phases. PRDs, scoping, phase checks, post-ship retro handoff |
| post-ship-retro | Evaluate shipped features against their original hypothesis. Pull analytics data, compare to success metrics, capture learnings |
| tradeoff | Pressure-test prioritization decisions using a structured ladder, adversarial review, and alternative exploration |
| context-briefer | Reload context on a specific epic, project, or topic when switching workstreams |
| domain-modeler | Model domains, identify bounded contexts, design aggregates, run event storming sessions |
| domain-sme-evaluator | Evaluate product artifacts (PRDs, APIs, data models, UIs) through an industry domain expert lens |
| ubiquitous-language-builder | Align terminology across code, docs, and business language. Build domain glossaries |
| requirements-synthesizer | Find common patterns across competing stakeholder requirements, resolve conflicts, propose unified solutions |
| decision-logger | Capture decisions with context, search past decisions, log decision points from other skills |
| impact-analyzer | Quantify business impact of features, bugs, or changes using analytics, Jira, and Slack data |
| demo-curator | Prepare demos and showcases. Pull recently shipped work, generate narrative with talking points |

### Engineering and Operations (7 skills)

| Skill | Description |
|-------|-------------|
| api-composer | Design APIs, evaluate service integrations, analyze data pipelines, and check composability between systems |
| data-model-reviewer | Review schemas, data exchange formats, API contracts, and standards compliance |
| sql-data-analysis | Answer data questions, write/audit SQL, query PostHog events, create dashboards |
| conventional-commits | Format commit messages as Conventional Commits automatically |
| incident-commander | Full incident lifecycle: first report through postmortem and action item tracking |
| system-behavior-documenter | Document system flows, capture institutional knowledge, build runbooks and troubleshooting guides |
| ui-evaluator | Review UI for usability, accessibility, and design consistency. Heuristic evaluation, WCAG checks |

### Team and Process (10 skills)

| Skill | Description |
|-------|-------------|
| morning | Dispatch parallel agents across 8+ data sources (Jira, Slack, Gmail, Calendar) for daily briefing and priority framing |
| eod | End-of-day wrap: capture accomplishments, surface unfinished work, walk through carry-forward decisions, write tomorrow plan |
| daily-planner | Aggregate today's priorities across Jira, Gmail, Slack, Calendar, and Circleback into a daily plan |
| backlog-groomer | Systematic backlog health check: groom tickets, prep for sprint planning, identify stale or misprioritized items |
| bug-consolidator | Triage bug backlogs: cluster related bugs, merge duplicates, remove stale items, score priorities |
| dependency-tracker | Track cross-team dependencies, surface stale blockers, generate nudge communications |
| performance-review | Prepare quarterly self-reviews and performance conversations. SKS format |
| signal-scanner | Scan Slack, Gmail, and Circleback for signals by source type: team (internal pain), customer (escalations), or vendor (integration issues) |
| competitive-intel-scanner | Monitor competitive landscape with three modes: full scan, incremental update, or read-only positioning summary |
| platform-health | Proactively discover platform problems: full health checks, focused area scans, or signal validation |

### Meta and System (4 skills)

| Skill | Description |
|-------|-------------|
| skill-evolver | Tune skills based on real usage. Runs automatically after skill completions or explicitly on request |
| ticket-proposer | Convert meeting notes or Slack discussions into structured Jira tickets with action items |
| user-story-writer | Write or improve story descriptions for Jira, Linear, or GitHub Issues. Auto-validates when writing to trackers |
| problem-discoverer | Proactively discover platform problems via full health checks, focused area scans, or signal triage |

## Rules (4 files)

Behavioral rules load automatically every session alongside CLAUDE.md. They provide consistent defaults:

| Rule | Purpose |
|------|---------|
| default-behaviors.md | Ambiguity handling, output consistency, format calibration, parallel dispatch, assumption discipline, grounded claims |
| ooda-reorientation.md | Between-task checkpoints to prevent plan drift when reality diverges from assumptions |
| writing-and-interaction.md | Editing discipline, skill invocation requirements, prompt modifier nudging, recipient awareness |
| skill-routing.md | Priority order for skill selection when multiple skills could handle a task |

## Customization

### Company-specific context

Many skills reference a `company-context` directory for team-specific information (personas, terminology, approval gates, etc.). To use this:

1. Create a `company-context/` directory in `~/.claude/skills/`
2. Add your company-specific files (SKILL.md, references, profiles)
3. Add `company-context/` to `.gitignore` to keep it local-only

The same pattern applies to other gitignored paths like stakeholder profiles and developer preferences.

### Adapting skills

Skills are designed to be forked and modified. Common customizations:

- **Personas** in `user-story-writer`: update the persona lookup table for your team's roles
- **Domain profiles** in `domain-sme-evaluator`: create profiles for your industry in `references/domains/`
- **Templates**: modify any `references/templates.md` to match your team's standards
- **Tool integrations**: skills reference Jira, Slack, Gmail, PostHog, etc. Swap for your stack.
- **Signal patterns** in `signal-scanner`: customize the signal detection patterns for your organization
- **Competitive config** in `competitive-intel-scanner`: set your competitors, focus areas, and scan cadence

## See Also

[EsseseOS](https://github.com/essesee/EsseseOS) is the full Claude Code operating system that includes these skills plus a cognitive model (CLAUDE.md), culture system, hooks, and plugins.
