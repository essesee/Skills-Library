---
name: release-notes
description: "Summarize delivered work as polished release notes or changelog for publishing."
---

# Release Notes

## Purpose

Produce a publish-ready release notes document describing user impact in plain language, with inline links to source tickets and PRs. Learns categorization, tone, and inclusion preferences over time.

## Dependencies

**Tools/APIs:**
- Jira — resolved tickets via JQL
- GitHub CLI (`gh`) — merged PRs
- Slack — deployment and release messages

**Reference Files:**
- `references/release-notes-preferences.md` — config template (first-run schema)
- `references/<org>-release-notes-preferences.md` — active org config and session log (created on first run; gitignored)
- `references/category-rules.md` — classification heuristics and learned overrides
- `references/release-notes-template.md` — output markdown template

## Inputs

- **Date range** (optional): Defaults to last 14 days. User can override with explicit dates or "last sprint."
- **Version label** (optional): If provided, replaces the date-range header (e.g., "v2.4.0" instead of "Feb 17 to Mar 3, 2026").
- **Output path** (optional): Override for where to write the file. Falls back to preferences config, then asks.

## Outputs

A single markdown file written to the configured output path containing:
- Thematic summary line
- 2–3 highlight paragraphs on the most impactful items
- Categorized item lists with inline Jira/PR links
- Footer with generation metadata (date, range, source counts)

## Workflow

### Phase 1: Load Configuration

1. Read the org-specific preferences file (e.g., `references/<org>-release-notes-preferences.md`) and `references/category-rules.md`.
2. If no org-specific preferences file exists, use `references/release-notes-preferences.md` as template. Prompt user for: Jira project keys, GitHub org/repos, Slack channels, output path, audience label. Write to a new org-specific file (naming convention: `<org>-release-notes-preferences.md`).
3. Resolve the date range: use explicit input, or default to `today - 14 days` through `today`.
4. Confirm the date range and config with the user before proceeding.

### Phase 2: Gather Data (Parallel)

Run all data pulls simultaneously. Each source is independent.

#### 2A: Jira — Resolved Work

```
project IN ({projects}) AND resolved >= "{start_date}" AND resolved <= "{end_date}" AND statusCategory = Done ORDER BY priority ASC, resolved DESC
```

Extract per ticket: key, summary, issue type, priority, resolution, epic link/name, labels, fix versions, breaking change indicators. Batch: 20.

#### 2B: GitHub — Merged PRs

```bash
# Org-wide:
gh search prs --merged --merged-after={start_date} --merged-before={end_date} --owner={org} --limit=100 --json number,title,body,url,repository,mergedAt,labels
# Or per-repo:
gh pr list --repo {org/repo} --state merged --search "merged:>={start_date}" --json number,title,body,url,mergedAt,labels --limit=100
```

Extract per PR: number, title, body (first 500 chars), URL, repo name, Jira keys from title/body (regex: `[A-Z]{2,10}-\d+`), labels. Batch: 20.

#### 2C: Slack — Deployment & Release Messages

Search configured channels for deployment keywords: `deployed`, `shipped`, `released`, `hotfix`, `rollback` (and similar).

Extract per message: channel, timestamp, author, text, Jira keys/PR numbers, rollback indicators. Batch: 10.

### Phase 3: Cross-Reference

After all three sources return:

1. **Link Jira tickets to PRs.** Match ticket keys found in PR titles/bodies to Jira results. Create a unified item per ticket with linked PR(s).
2. **Flag orphan PRs.** PRs with no matching Jira ticket become standalone items. Include them — they may represent undocumented work.
3. **Enrich with Slack context.** Attach deployment messages to their corresponding tickets/PRs. Note rollbacks or incidents.
4. **Deduplicate.** If a single piece of work appears across multiple tickets or PRs, merge into one item. Prefer the Jira summary as the canonical description.
5. **Drop raw data.** Keep only the unified item list from this point forward.

### Phase 4: Categorize

Classify each item using `references/category-rules.md`. Apply learned overrides first, then default rules. When ambiguous, default to **Improvements**.

### Phase 5: Select Highlights

Choose 2–3 items as highlights based on:

1. Highest Jira priority among the resolved items.
2. Most user-facing impact (features and high-severity bug fixes over internal improvements).
3. Mentioned in Slack deployment announcements (signals visibility/importance).
4. Learned highlight preferences from past sessions (if any).

### Phase 6: Generate Draft

Compose using the template in `references/release-notes-template.md`. Key rules:
- Write for the audience defined in preferences (default: informed but non-technical)
- Lead with user impact, not engineering changes
- Group related items (multiple PRs for one feature = one line item)
- Omit empty sections entirely
- Apply learned tone preferences from org-specific preferences file

### Phase 7: Interactive Review

Present the full draft to the user. Offer these actions:

- **"Approve"** — Accept the draft as-is. Proceed to write.
- **"Edit"** — User provides specific edits (reword an item, change the summary, etc.). Apply and re-present.
- **"Re-categorize"** — Move an item to a different category. (Logged as category override.)
- **"Remove"** — Drop an item from the notes. (Logged as removal signal.)
- **"Add"** — User wants to include something not captured by the data pull. Add it manually.
- **"Rewrite"** — Regenerate a specific section with different framing or tone. (Logged as tone signal.)
- **"Change highlights"** — Swap highlight items. (Logged as highlight preference.)

Navigation commands: Show overview / Done / Next section / Back

Continue the review loop until the user approves or says Done.

### Phase 8: Write File

1. Write the approved markdown to the configured output path.
2. Confirm the file path and a brief summary of what was written.

### Phase 9: Learn

After the file is written, update the org-specific preferences file (e.g., `references/<org>-release-notes-preferences.md`) following the format comments in each section (category overrides, removal patterns, tone adjustments, highlight preferences). Log a session entry.

**Consolidation:** Every 5 sessions, review the Session Log. Remove zero-signal entries, merge duplicates, strengthen confirmed patterns. Trim to under 2,000 words.

---

## Context Rules

- Load config and category-rules at Phase 1. Load template at Phase 6.
- Run Phase 2 data pulls in parallel — each source is independent.
- After Phase 3, drop raw source data. Keep only the unified item list.
- Update preferences file only after Phase 8 (file written), never mid-workflow.
- Batch: Jira 20, GitHub 20, Slack 10.
- If a source returns zero results or is unreachable, note the gap and continue with available sources.

## Edge Cases

- **No Jira tickets resolved in range:** Generate notes from PRs and Slack only. Flag the gap to the user.
- **No merged PRs:** Generate from Jira only. Orphan PR section is empty.
- **Massive volume (>50 items):** Summarize minor items as "{N} additional {category} improvements" rather than listing every one. Focus detail on highlights and high-priority items.
- **Rollback detected in Slack:** Flag the item in Known Issues with context from the rollback message, even if the original ticket is marked resolved.
- **PR references ticket from a non-configured project:** Include the PR as an orphan item. Do not expand to query unconfigured projects.
- **Version label provided but date range also given:** Version label takes precedence for the header; date range is still used for querying.

## When NOT to Use

- **Stakeholder-specific updates** — Use `stakeholder-update` instead. It tailors content per audience.
- **Sprint retrospective or velocity reporting** — Use `backlog-groomer` or Jira dashboards.
- **Incident postmortems** — Use a dedicated incident review process, not release notes.
- **Daily or weekly team standups** — Use `daily-planner` for personal planning or `team-signal-scanner` for team awareness.
- **Customer-facing changelog with SLA/contractual language** — This skill produces general-audience notes, not compliance documents.
