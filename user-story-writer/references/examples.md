# Story Examples

Real examples following the skill's proven format. One per type variant. Use for calibration — these represent the quality bar.

---

## Feature Example

### User story

* As a customer service agent, when processing a return request, I want a unified "Return Details" and "Refund Summary" form so I can complete the entire return in one pass with minimal errors.

### Context

Maria, an experienced agent, enters return details and needs order-level fields plus a line-by-line refund breakdown in one session with no backend delays.

### Design

* **Figma:** [Agent Console — Return Details](link)
* **Prototype:** [Return Flow Prototype](link)

### Acceptance criteria

- [ ] Return Details section contains all required fields: Order Number, Reason Code, Return Method, Item Selection, Condition Assessment
- [ ] All required fields show validation errors when empty on submit
- [ ] Item selection enforces at least one item selected constraint
- [ ] Refund Summary section calculates refund amounts per line item automatically
- [ ] Form saves complete return in a single submit action

### Other information

* **Environment**: Staging only. Do NOT release to Prod.

**What makes this good:** Specific persona with name, clear situation trigger, concrete ACs with validation rules, design references included, scope constraint clearly stated.

---

## Tech Debt Example

### User story

* As a developer, when I am working on the server code, I want to replace synchronous file reads with async alternatives so that the event loop is not blocked during file operations.
* As a developer, when I review middleware dependencies, I want to remove redundant packages so that the codebase is cleaner and more efficient.
* As a developer, when I analyze log verbosity, I want to reduce unnecessary logging so that production logs are more manageable.

### Context

This issue focuses on improving server code quality and addressing technical debt. Key tasks include replacing synchronous file reads, removing redundant dependencies, reducing log verbosity, and scoping middleware to appropriate routes. These changes aim to enhance performance and maintainability.

### Acceptance criteria

- [ ] `readFileSync` in `src/server/routes/checkout.tsx` is replaced with `fs.promises.readFile()` or startup-time caching
- [ ] The test page still loads correctly after the change
- [ ] `body-parser` import is removed from `src/server/main.ts`
- [ ] `bodyParser.json()` is replaced with `express.json()`
- [ ] `body-parser` and `@types/body-parser` are removed from `package.json`
- [ ] Verbose logs ("Starting dataMiddleware", "Successfully read cookie value") are at `debug` level instead of `info`

**What makes this good:** Multiple user stories for distinct concerns, ACs reference specific files and functions, existing behavior preservation included ("test page still loads"), checkbox format for engineering tracking.

---

## Spike Example

### User story

* As a developer, when planning multi-item checkout, I want to investigate how the receipt panel should display 2+ items so that we can design the correct UI before building.

### Context

The current checkout receipt panel only supports a single item. We need to determine the UX for multiple items before committing to implementation.

### Questions

1. How will multiple line items appear in the receipt panel — stacked blocks with subtotals or combined view?
2. How are taxes and fees broken down — per item or aggregated?
3. How does "Due Later" vs "Due Today" display when items have different payment terms?
4. Does trip/order protection show as one line or per item?
5. Does "Remove Item" need to appear for each item listed?

### Output

* Design recommendation from UX
* API sketch from backend for multi-item receipt data structure

### Timebox

2 days

### Answered Questions

* Input forms do NOT need to repeat for each item — same details assumed.

**What makes this good:** Specific, answerable questions (not vague). Clear output deliverables with owners. Timebox present. Pre-answered questions included to avoid re-investigation.

---

## Bug Consolidation Example (synthetic — based on team patterns)

### User story

* As a user, when using SSO to access the platform, I want authentication to work reliably across all entry points so that I don't lose work or get locked out during active sessions.

### Context

Multiple bugs report authentication failures across different SSO flows — initial login, session refresh, and cross-app navigation. The root cause appears to be inconsistent token handling between the identity provider and platform session management. 8 users reported issues in the last 30 days.

**Consolidated bugs:**
| Ticket | Summary | Severity |
|--------|---------|----------|
| PROJ-3001 | SSO login fails intermittently on Chrome | High |
| PROJ-3045 | Session expires during active workflow | Critical |
| PROJ-3067 | Cross-app navigation loses auth state | Medium |

### Acceptance criteria

- [ ] Token refresh logic handles all three SSO entry points (login, session refresh, cross-app)
- [ ] Session does not expire during active user interaction (no idle timeout during form entry)
- [ ] Cross-app navigation preserves authentication state without requiring re-login
- [ ] All three original bug scenarios no longer reproduce
- [ ] No regressions in standard login/logout flow

### Other information

* **Priority score**: 8.2 (composite from bug-consolidator)
* **Related tickets**: PROJ-3001, PROJ-3045, PROJ-3067 (become sub-defects)
* **Root cause hypothesis**: Token refresh timer doesn't account for cross-app iframe context

**What makes this good:** User story describes the root problem, not individual symptoms. Consolidated bugs table for quick reference. ACs cover each child bug's scenario plus regression check. Root cause hypothesis included for developer context.
