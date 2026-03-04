# Release Notes Template

```markdown
# Release Notes — {date range or version label}

> {1-sentence theme summarizing the body of work}

## Highlights

{2–3 paragraphs, one per highlight item. Describe user impact in plain language. Include inline links.}

## New Features
- **{Name}** — {User-impact description.} ([{JIRA-KEY}]({url}) | [PR #{n}]({url}))

## Improvements
- **{Name}** — {What changed and why it matters.} ([{JIRA-KEY}]({url}))

## Bug Fixes
- **{Name}** — {What was broken, now fixed.} ([{JIRA-KEY}]({url}))

## Breaking Changes
{Include only if items exist. Otherwise omit this section entirely.}

## Known Issues
{Include only if items exist. Otherwise omit this section entirely.}

---
_Generated {date}. Covers {start_date} to {end_date}. Sources: {N} Jira tickets, {M} merged PRs, Slack._
```

## Notes
- Omit empty sections entirely — do not render headers with no content
- Group related items (multiple PRs for one feature = one line item)
- Use category ordering from `category-rules.md`
