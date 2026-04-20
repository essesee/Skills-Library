# Writing and Interaction Rules

## No Dashes
Do not use dashes in output. No em dashes, no en dashes, no hyphens used
as punctuation, and no hyphens in compound adjectives (rephrase instead:
"agent initiated" not "agent-initiated"). Replace with periods, commas, or
restructured sentences. Applies to all output: drafts in the user's voice,
analysis, notes. Ticket IDs (PROJ-1234) are fine.

## Skill Discipline
Always invoke relevant skills before creative work, especially brainstorming
before defining features, architecture, or behavior. Do not skip because the
conversation feels productive. "This feels productive" is a rationalization
for skipping the skill.

**Artifact-specific skill requirements:**
- User stories or Jira story content: invoke `user-story-writer` first
- UX evaluation or design artifacts: invoke `ui-evaluator` and `bmad-ux-designer`
- Plans, specs, or new features: invoke `brainstorming` first
- PRDs: invoke `bmad-create-prd` or `bmad-edit-prd`
- Epics/stories list: invoke `bmad-create-epics-and-stories`
- Outbound communication drafts (email, Slack, stakeholder message, release
  note, change announcement): invoke `cyrano-write` as the drafting step,
  not just as a profile lookup. Applies inside `comms`, `message-drafting`,
  `stakeholder-update`, `change-communicator`, `eod`, and `morning`.

Routine edits (config, settings, CLAUDE.md, memory, minor fixes) need no
skill gate.

## Prompt Modifier Nudging
When the user gives a task and the default mode seems like a mismatch, suggest
the relevant modifier. Keep it lightweight: "Want me to go exhaustive on
this, or quick pass?" Key modifiers:
- **Depth:** "be exhaustive" / "be thorough" / "what am I missing"
- **Brevity:** "just the answer" / "be brief"
- **Position:** "give me your recommendation" / "play devil's advocate" / "steelman this"
- **Scope:** "quick and dirty" / "production ready" / "explore this" vs "just do it"

## Message Recipient Awareness
Before sending any Slack message, email draft, or external communication:
- Identify ALL recipients (To, CC, channel members, thread participants).
- Verify tone is appropriate for every recipient, not just the primary one.
- Flag if any recipient could misread intent or lack context.
- For channel messages, consider whether someone outside the core thread
  could misunderstand.

## Status Update Drafting
When drafting a follow-up to someone who already has context, skip the
problem restatement. Lead with findings and next steps. Save problem
framing for messages where the recipient is being looped in fresh.
