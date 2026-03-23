---
name: session-recap
description: "End-of-session review — capture cross-session learnings as memory files and catch missed skill-evolver runs."
---

# Session Recap

## Purpose

Capture persistent knowledge from the current session before it's lost. Sessions generate decisions, stakeholder preferences, project state changes, and context that would save time in future sessions — but only if saved to memory. Also catches any skill-evolver runs that were missed during the session.

## When to Run

- End of meaningful sessions (manually invoked or prompted)
- After sessions involving decisions, stakeholder interactions, or project state changes
- When you notice context that keeps getting re-explained across sessions

## Workflow

### Phase 1: Scan Conversation for Learnings

Review the current conversation and extract:

| Signal Type | What to Look For |
|-------------|-----------------|
| **Decisions** | Tool/process/architecture choices made with reasoning |
| **Stakeholder preferences** | How someone likes tickets, comms, meeting formats |
| **Project state changes** | Initiative status updates, deadlines, blockers, scope changes |
| **Recurring context** | Information that had to be explained or re-discovered |
| **Developer preferences** | New ticket formatting preferences, workflow styles |
| **Feedback on Claude** | Corrections to approach, confirmed good patterns |

Skip ephemeral details — only flag things useful in *future* sessions.

### Phase 2: Propose Memory Writes

For each finding:

1. **Classify** by memory type: `user`, `feedback`, `project`, `reference`
2. **Check for existing memories** — update rather than duplicate
3. **Draft the memory content** with frontmatter:
   ```markdown
   ---
   name: {descriptive name}
   description: {one-line description for relevance matching}
   type: {user|feedback|project|reference}
   ---

   {content — for feedback/project: rule/fact, then **Why:** and **How to apply:**}
   ```
4. **Present all proposals** to the user in a numbered list:
   ```
   [1] NEW project: {name} — {one-line summary}
   [2] UPDATE user_workflow_preferences.md — add {detail}
   [3] NEW feedback: {name} — {one-line summary}
   ```

**Never write memories without user approval.** Wait for: `Apply all`, `Apply {n}`, `Skip {n}`, or `Done`.

### Phase 3: Detect Memory Path

- If CWD is `/Users/jesseshedd/Documents/work/` or a subdirectory:
  - Memory path: `~/.claude/projects/-Users-jesseshedd-Documents-work/memory/`
- If CWD is `/Users/jesseshedd/` (home directory):
  - Memory path: `~/.claude/projects/-Users-jesseshedd/memory/`
- For other directories, derive the path from the Claude Code project key convention.

### Phase 4: Write Approved Memories

For each approved proposal:
1. Write or update the memory file
2. Update `MEMORY.md` index if a new file was created
3. Show brief confirmation: `Saved: {filename} — {description}`

### Phase 5: Skill-Evolver Batch Check

1. List all skills invoked during the session
2. Check which ones had skill-evolver run afterward
3. For any missed skills, run skill-evolver in automatic mode
4. Most will be silent gate-check exits (just incrementing usage_count)

### Phase 6: Session Summary

Brief output:
- What was accomplished this session
- Memories saved (count and types)
- Open threads or follow-ups for next session

## What NOT to Save

- Code patterns derivable from reading the codebase
- Git history or who-changed-what
- Debugging solutions (the fix is in the code)
- Anything already in CLAUDE.md files
- Current task details only useful within this session

## Edge Cases

- **No learnings found**: "No persistent learnings detected this session. This is fine — not every session generates cross-session knowledge."
- **Memory file conflict**: If a proposed update conflicts with existing content, show both versions and ask the user to choose.
- **Very long session**: Prioritize the most impactful 3-5 learnings rather than exhaustively cataloging everything.
