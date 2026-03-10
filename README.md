# EsseseOS

A library of 34 Claude Code skills for product management, engineering, architecture, project management, and communication workflows.

## What Are Skills?

[Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills) are structured prompts that teach Claude how to perform specific workflows. Each skill is a `SKILL.md` file with optional reference materials. When Claude Code detects a matching trigger phrase, it loads the skill and follows its instructions.

Skills are different from one-off prompts — they encode repeatable processes, quality standards, and domain knowledge so Claude performs complex workflows consistently.

## Installation

**Full library:**

```bash
git clone https://github.com/essesee/EsseseOS.git ~/.claude/skills
```

**Cherry-pick individual skills:**

Copy any skill directory (e.g., `user-story-writer/`) into your `~/.claude/skills/` folder.

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

Skills reference each other by name — for example, `user-story-writer` can call `company-context` for persona definitions.

## CLAUDE.md

The included `CLAUDE.md` is a cognitive model — it tells Claude how you think, decide, communicate, and where to push back. It's fully generic and project-agnostic.

To adapt it: edit the values, blind spots, and communication style sections to match your own patterns. The structure works as-is; the content should reflect you.

## Skill Catalog (34 skills)

### Product (9 skills)
Lifecycle, planning, signals, and data analysis.

| Skill | Description |
|-------|-------------|
| discover-design-deliver | Guide product development through discovery, design, and delivery phases |
| problem-discoverer | Proactively discover platform problems via health checks, area scans, or signal triage |
| daily-planner | Prioritize work across Jira, Gmail, Slack, Calendar, and Circleback for the day |
| stakeholder-update | Send tailored project status updates to stakeholders via Slack or email |
| performance-review | Prepare quarterly self-reviews or performance conversations |
| sql-data-analysis | Answer data questions, write/audit SQL, query PostHog events, or create dashboards |
| customer-signal-scanner | Scan Gmail and Slack for customer and partner escalations and pain signals |
| team-signal-scanner | Scan Slack and Gmail for internal team signals — pain points, friction, workaround culture |
| vendor-signal-scanner | Scan Gmail and Slack for vendor/provider signals — integration problems, service issues, contract changes |

### Engineering (6 skills)
Code, deploy, test, and UI.

| Skill | Description |
|-------|-------------|
| code-writer | Write code, review PRs, understand unfamiliar code, generate from screenshots, or set up dev environments |
| deployment-assistant | Deploy code, push to staging, ship to production, or troubleshoot failed deployments |
| qa-testing | Test features, generate test plans, run tests, check for regressions, or identify edge cases |
| conventional-commits | Format commit messages as Conventional Commits |
| ui-evaluator | Review a UI for usability, accessibility, or design consistency before shipping |
| ui-mockup-generator | Visualize feature concepts, create mockups, prototype UIs, or iterate on existing screens |

### Architecture (5 skills)
DDD, APIs, data models, and domain language.

| Skill | Description |
|-------|-------------|
| domain-modeler | Model domains, identify service boundaries, design aggregates, or run event storming sessions |
| api-composer | Design APIs, evaluate service integrations, analyze data pipelines, or check composability |
| data-model-reviewer | Review schemas, design data exchange formats, evaluate API contracts, or check standards compliance |
| ubiquitous-language-builder | Align terminology between code, docs, and business language, or build a domain glossary |
| domain-sme-evaluator | Evaluate product artifacts (PRDs, API specs, data models, UIs) through an industry domain lens |

### Project Management (5 skills)
Backlogs, bugs, tickets, and stories.

| Skill | Description |
|-------|-------------|
| backlog-groomer | Systematic backlog health checks, sprint planning prep, or cleanup |
| bug-consolidator | Triage bug backlogs — cluster related bugs, merge duplicates, remove stale items, prioritize |
| jira-template-builder | Standardize Jira templates, epic mapping rules, or ticket hygiene analysis |
| ticket-proposer | Extract action items from meeting notes or Slack discussions and propose Jira tickets |
| user-story-writer | Write or improve story descriptions for any tracker — Jira, Linear, GitHub Issues |

### Communication (4 skills)
Messaging, voice, editing, and release notes.

| Skill | Description |
|-------|-------------|
| message-drafting | Triage inboxes, draft email or Slack replies, or process unread messages |
| voice-analyzer | Draft communications in the user's voice, or build/update a voice profile |
| style-editor-expanded | Edit or generate formal documents, reports, proposals, or polished external-facing content |
| release-notes | Summarize delivered work as a polished release notes document |

### Meta (5 skills)
Quality checks, reasoning, and skill management.

| Skill | Description |
|-------|-------------|
| output-consistency-reviewer | Catch inconsistencies in complex output before sharing |
| priority-format-calibrator | Lightweight check on output quality before delivery |
| ambiguity-handler | Resolve scope, terms, or context at the start of complex or vague tasks |
| mental-models | Evaluate proposals, stress-test reasoning, or assess risk using thinking frameworks |
| skill-improver | Tune skills based on real usage feedback |

## Customization

### Company-specific context

Many skills reference a `company-context` directory for team-specific information (personas, terminology, approval gates, etc.). To use this:

1. Create a `company-context/` directory in the repo root
2. Add your company-specific files (SKILL.md, references, profiles)
3. Add `company-context/` to `.gitignore` to keep it local-only

The same pattern applies to other gitignored paths like stakeholder profiles and developer preferences — see `.gitignore` for the full list.

### Adapting skills

Skills are designed to be forked and modified. Common customizations:

- **Personas** in `user-story-writer` — update the persona lookup table for your team's roles
- **Domain profiles** in `domain-sme-evaluator` — create profiles for your industry in `references/domains/`
- **Templates** — modify any `references/templates.md` to match your team's standards
- **Tool integrations** — skills reference Jira, Slack, Gmail, PostHog, etc. Swap for your stack.
