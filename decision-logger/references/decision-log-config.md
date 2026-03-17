# Decision Log Configuration

## Confluence Settings
<!-- Fill in on first run -->
- **Space Key:**
- **Parent Page Title:**
- **Page Structure:** entries-on-page | child-pages
  <!-- entries-on-page: all decisions appended to one page -->
  <!-- child-pages: each decision is a child page under parent -->

## Tag Taxonomy
<!-- Default categories. Add custom tags as needed. -->
### Domain Areas
- booking, search, auth, payments, integrations, infrastructure, data, admin, member-portal

### Teams
- platform, backend, frontend, data-engineering, devops

### Decision Types
- architecture, product, process, tooling, vendor, policy

## Search Patterns
<!-- CQL patterns for searching the decision log in Confluence -->
- By keyword: `space = "{space_key}" AND ancestor = "{parent_page_id}" AND text ~ "{keyword}"`
- By label: `space = "{space_key}" AND ancestor = "{parent_page_id}" AND label = "{tag}"`
- By date: `space = "{space_key}" AND ancestor = "{parent_page_id}" AND lastmodified >= "{date}"`

## Cross-Linking Rules
- When a decision relates to a Jira ticket: add a comment on the ticket linking to the Confluence decision record
- When a decision supersedes another: update the old decision's status to "Superseded by DEC-XXXX"
- When posting to Slack: include the decision title, a 1-sentence rationale, and the Confluence link

## ID Cache
- **Last Used ID:** <!-- Updated automatically after each decision is logged -->

## Session Log
<!-- Track usage for learning -->
