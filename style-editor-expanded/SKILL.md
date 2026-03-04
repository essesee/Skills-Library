---
name: style-editor-expanded
description: "Use when editing or generating formal documents, reports, proposals, or polished external-facing content. Trigger on: 'edit this document,' 'tighten this up,' 'make this clearer,' 'formal report,' 'write a proposal,' 'clean up this draft,' 'Strunk & White,' 'style edit.'"
---

# Style Editor (Expanded)

## Purpose
Enforce Strunk & White standards in both generation and editing modes. First drafts should already be tight — not bloated for later editing.

## Dependencies

**Other Skills:** voice-analyzer (when drafting in user's voice)
**Reference Files:** `references/deadwood.md` (deadwood phrases and replacements, loaded at edit start)

## Modes

### Generation Mode
Apply all core rules during creation. The first draft should be clean.

### Editing Mode
Six-pass process applied to existing text. Returns a clean version plus a changelog.

**Pass 1: Deadwood Elimination** — Cut needless words and phrases. Load `references/deadwood.md`.

**Pass 2: Active Voice Conversion** — Convert passive to active. Exceptions: actor genuinely unknown, action is the emphasis, passive aids sentence flow.

**Pass 3: Positive Form & Specificity** — Positive assertions over negative constructions. Replace vague language with specifics. Flag gaps with `[SPECIFY: what's needed]`.

**Pass 4: Structure & Parallelism** — Parallel structure in lists and series. Consistent heading hierarchy. Logical flow between sections.

**Pass 5: Word Choice** — Nouns and verbs over adjectives and adverbs. Plain words over ornate. Delete qualifiers — see `deadwood.md` Hedging & Qualifier Phrases. No careless superlatives.

**Pass 6: Final Polish** — Read for plainness, simplicity, orderliness, sincerity. Substance over performance. Does every sentence earn its place?

## Core Rules (Both Modes)

1. Omit needless words.
2. Active voice by default.
3. Positive form — say what things are, not what they aren't.
4. Specific and concrete — names, numbers, percentages.
5. Parallel structure in lists and series.
6. Nouns and verbs carry the weight.
7. Plain words over fancy words.
8. No qualifiers or hedging words.
9. No careless superlatives without evidence.
10. Substance over performance.

## Context Rules
- Load `references/deadwood.md` at edit start.
- In generation mode: load Core Rules at task start, apply inline during writing.
- In editing mode: apply six-pass process and produce a changelog.
- Scorecard is the final deliverable — summarize findings concisely.

## When NOT to Use
- Casual Slack messages or internal chat — match conversational tone instead.
- Code, SQL, or Jira ticket descriptions.
- Content where the user explicitly wants an informal register.
