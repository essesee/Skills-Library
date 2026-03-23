---
name: system-behavior-documenter
description: "Document system flows, capture institutional knowledge, build runbooks and troubleshooting guides."
---

# System Behavior Documenter

## Purpose
System behavior knowledge is a known gap — nobody owns it, and it lives unreliably in people's heads. This skill walks through codebase behavior for specific flows, traces execution paths, identifies non-obvious behavior, and produces documentation that captures both what happens and why. It bridges the gap between code (which shows what) and institutional knowledge (which explains why).

## Dependencies

**Tools/APIs:**
- Codebase access — read source files, trace execution paths, analyze patterns
- Git — blame, log, commit history for the "why" behind code decisions
- Confluence API — publish documentation
- Jira API — link documentation to related tickets/epics

**Other Skills:**
- `company-context` — system names, architecture context, team ownership
- `decision-logger` — search for past decisions related to the flow
- `api-composer` — for flows that involve service integrations

**Reference Files:**
- `references/documentation-templates.md` — templates for different documentation types (flow doc, integration doc, troubleshooting guide)
- `references/documentation-log.md` — tracks what flows have been documented, when, at what commit, and what gaps were identified

## Inputs
- **Flow/System** (required): what to document — "the booking pipeline," "authentication flow," "data sync process," specific module or service
- **Codebase path** (required if not obvious): where the relevant code lives
- **Depth** (optional): "overview" (architecture-level), "detailed" (code-level, default), "troubleshooting" (focus on failure modes)
- **Audience** (optional): "engineering," "new team member," "on-call" (default: engineering)

## Outputs
- System behavior documentation with flow diagrams (as mermaid/text)
- "Why" annotations — rationale for non-obvious design decisions
- Gaps identified — areas where the "why" is unknown and needs to be captured
- Optional: Confluence page with the documentation

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Flow Documentation** | "document how X works," "trace this flow" | Full execution path documentation |
| **Integration Documentation** | "document the integration with X" | Focus on service boundaries, data contracts, error handling |
| **Troubleshooting Guide** | "on-call guide for X," "how to debug X" | Focus on failure modes, common issues, diagnostic steps |
| **Knowledge Capture** | "capture what we know about X" | Collaborative session — document what the user explains, identify gaps |

---

## Workflow

### Step 1: Scope the Documentation

1. Clarify what to document:
   - "When you say 'booking pipeline,' do you mean from search results to booking confirmation? Or a specific segment?"
   - "Is there a specific entry point I should start from?"
   - "Who will read this documentation?"

2. Identify the codebase location:
   - If the user provides a path, start there
   - If not, search the codebase for the relevant entry points
   - Confirm with the user: "I found {file/module}. Is this the right starting point?"

### Step 2: Trace the Flow

#### Mode-Specific Approach

**Knowledge Capture mode:** Skip code tracing. Instead, interview the user with structured questions about the system's behavior, boundaries, data flow, and failure modes. Use the Knowledge Capture template from `references/documentation-templates.md` to guide the session. The user is the primary source — code verification comes after.

**Troubleshooting Guide mode:** Prioritize failure modes, diagnostic commands, and health indicators over the "why" behind design decisions. Focus on what an on-call engineer needs at 2am.

**Flow Documentation and Integration Documentation modes:** Proceed with the code-tracing steps below.

#### For Code-Level Documentation:

1. **Entry point:** Read the entry point (API endpoint, job handler, event listener, etc.)
2. **Follow the execution path:** Trace through the call chain:
   - Read each function/method called
   - Note: input transformations, business logic, branching conditions, error handling
   - Identify external calls (APIs, databases, message queues, other services)
3. **Map decision points:** Where does the code branch? What determines the path?
4. **Identify side effects:** What gets written, published, emitted, or cached?
5. **Find the exit points:** How does the flow complete? What gets returned/emitted?

#### For Architecture-Level Documentation:

1. **System boundaries:** Which services/modules are involved?
2. **Data flow:** What data enters, how it transforms, where it goes
3. **Integration points:** External services, databases, message brokers
4. **Ownership boundaries:** Which team owns each piece?

### Step 3: Research the "Why"

For each non-obvious design decision or behavior discovered:

1. **Git blame/log:** When was this introduced? By whom? What does the commit message say?
2. **Search decision log:** Does `decision-logger` have a relevant decision record?
3. **Search Jira:** Was there a ticket that drove this change?
4. **Search Confluence:** Is there a spec or design doc that explains the rationale?
5. **Mark unknown:** If the "why" can't be determined from available sources, mark it explicitly: "UNKNOWN — reason for this behavior is not documented. Suggest asking {likely person based on git blame}."

### Step 4: Build the Document

Use `references/documentation-templates.md` for structure.

**Flow Documentation Template:**

```
## {Flow Name}

### Overview
{2-3 sentence description of what this flow does and why it exists}

### Entry Points
- {Entry point 1}: {when/how it's triggered}

### Flow Diagram
{Mermaid diagram or text-based flow}

### Step-by-Step Execution

#### Step 1: {Name}
- **What:** {What happens — code-level description}
- **Where:** {file:line_number}
- **Why:** {Rationale — from git history, decision log, or user input}
- **Inputs:** {What data comes in}
- **Outputs:** {What data goes out}
- **Error handling:** {What happens when this fails}

#### Step 2: {Name}
...

### Decision Points
| Condition | Path | Rationale |
|-----------|------|-----------|
| {condition} | {what happens} | {why it works this way} |

### External Dependencies
- {Service/API}: {what it provides, failure behavior, SLA}

### Side Effects
- {What gets written/emitted/cached and when}

### Failure Modes
- {What can go wrong}: {symptoms, detection, recovery}

### Known Gaps
- {Areas where the "why" is unknown or behavior is undocumented}
- {Suggested person to ask, based on git blame}

### Related
- Decision records: {links}
- Jira tickets: {links}
- Confluence specs: {links}
```

**Troubleshooting Guide Template:**

```
## Troubleshooting: {System/Flow}

### Common Issues

#### {Issue 1: Symptom}
- **Likely cause:** {root cause}
- **How to diagnose:** {steps}
- **How to fix:** {resolution steps}
- **Escalation:** {when to escalate, to whom}

### Diagnostic Commands
- {Command}: {what it shows, when to use it}

### Health Indicators
- {Metric/log}: {what healthy vs. unhealthy looks like}

### Runbooks
- {Scenario}: {step-by-step response}
```

### Step 5: Collaborative Review

Present the documentation. This is explicitly collaborative — the user's knowledge fills gaps the code can't:

**For each "UNKNOWN" or gap:**
- "I found this behavior at {file:line} but couldn't determine why. Do you know the rationale?"
- If the user provides context, incorporate it and cite: "Per {user}, this was done because..."
- If the user doesn't know: keep the gap marker. This is valuable — it identifies undocumented institutional knowledge.

Actions:
- **"Correct"** — fix inaccuracies
- **"Add context"** — provide the "why" for a gap
- **"Go deeper"** — trace a specific branch more thoroughly
- **"Publish"** — write to Confluence
- **"Create Jira ticket"** — for gaps that need investigation

### Step 6: Publish (Optional)

1. Write to Confluence in the appropriate space
2. Link to related Jira tickets/epics
3. Tag with system area, ownership, and last-updated date

---

## Context Rules
- Trace code in segments — read one function/module at a time, extract behavior, then move to the next.
- Do not attempt to hold the entire codebase in context. Follow the execution path sequentially.
- Git blame/log: extract only the relevant commit messages and dates. Drop raw git output.
- For large codebases, ask the user to confirm the path before tracing deeply into branching paths.
- Keep the working document in context as it builds. It's the primary output.
- Max depth: 10 function calls deep before summarizing and asking if the user wants to go deeper.
- Load only the relevant template from `references/documentation-templates.md` at Step 4 based on the mode. Do not load all templates.
- Git blame/log output: extract only relevant commit messages and dates. Drop raw git output immediately.
- During collaborative review (Step 5), keep the full working document in context. When the user provides corrections, update the affected section in place. Do not rebuild from scratch.

## Edge Cases
- **Code is poorly commented:** That's expected — the skill's value is documenting what the code doesn't explain. Use git history and external sources for "why."
- **Flow spans multiple repos/services:** Document what you can access. For external services, document the interface (what gets sent, what's expected back) and mark the internal behavior as out of scope.
- **User disagrees with the code's behavior:** Flag: "The code does X, but you expected Y. This may be a bug or an undocumented design decision. Want to create a Jira ticket?"
- **Flow is too complex for a single document:** Split into sub-flows. Create an overview document that links to detail documents.
- **No codebase access:** Switch to Knowledge Capture mode — document what the user explains from memory, identify what needs verification.
- **Code has recently changed:** Note: "This documentation reflects the code as of {date/commit}. Recent changes may not be captured."
- **Dead code or unreachable paths:** If tracing reveals code paths that appear unreachable (always-false conditions, deprecated methods, commented-out blocks), document them separately under a "Legacy/Dead Code" section. Flag for the user: "This code path appears unreachable as of {commit}. Should it be removed?"

## When NOT to Use
- **API design/composition** — use `api-composer`
- **Domain modeling** — use `domain-modeler`
- **Code review** — use code review tools
- **Writing ADRs** — those are architectural decisions, not behavior documentation (though this skill may identify decisions worth recording)
- **Quick "what does this function do"** — just read the code directly
