---
name: voice-analyzer
description: "Use when drafting communications on the user's behalf, or when building/updating a voice profile. Trigger on: 'learn my voice,' 'analyze my writing style,' 'build a voice profile,' 'make it sound like me,' or when any other skill needs to draft a message in the user's voice."
---

# Voice Analyzer

## Purpose
Analyze real sent communications to build a cached, reusable voice profile. Any skill that drafts communication on behalf of the user should load this skill's output (the voice profile).

## Dependencies

**Tools/APIs:** Gmail, Slack
**Reference Files:** `references/voice-profile-template.md` (profile structure template), `references/style-rules-template.md` (Elements of Style overlay template)

## Inputs
- 20-30+ real sent emails and Slack messages
- Segmented by audience (exec, peer, vendor, engineer) and channel (email, Slack)

## Outputs
- Reusable voice profile document (under 2,000 words), saved as a file
- Separate Elements of Style rules file layered on top

## Workflow

### Step 1: Collect Samples (Parallel)
Gather 20-30+ sent messages across audience types and channels.

a. **Gmail** (10 messages per audience segment): executive, peer/team, vendor/external, engineer/technical communications
b. **Slack** (10 messages per audience segment): same audience segments, different channel tone

### Step 2: Analyze Patterns
For each audience segment, analyze: sentence length, vocabulary/jargon, punctuation habits, openers/closers, formality range, emoji usage, capitalization, paragraph structure, disagreement handling, bad-news vs. good-news delivery.

### Step 3: Build the Voice Profile
Build the voice profile using `references/voice-profile-template.md`. Target: Core Voice (~400 words), up to 4 Audience Sub-Profiles (~300 words each), Never Do This section (~200 words). Total under 2,000 words.

### Step 4: Layer Elements of Style
Create a style rules overlay using `references/style-rules-template.md`. Note tensions between user voice and Strunk & White. Sharpen, don't replace. Style filter toggleable for casual messages.

### Step 5: Validate with Test Scenarios
Run 3-4 test drafts:
1. Vendor pushback reply
2. Team Slack update
3. Executive status response
4. Casual Slack message

Present each to the user for calibration. Log corrections as refinements.

### Step 6: Iterative Refinement
After the initial profile is built and validated:

1. Ask: "Want to refine any aspect of the profile? You can provide additional samples, flag specific issues, or request adjustments."
2. If user provides feedback:
   a. Apply corrections directly to the voice profile
   b. Re-run the affected test scenario(s) from Step 5 to confirm the fix
   c. Repeat until user is satisfied
3. If user says done, save the final profile and style rules

This loop ensures the profile converges on accuracy rather than shipping a first-pass approximation.

## Context Rules
- Load voice profile only when drafting or updating.
- Load style rules at draft time, layered on top of voice profile.
- Drop raw email and Slack message samples after pattern analysis is complete.
- Keep profile under 2,000 words — trim least-used patterns if it grows past that.
- Store voice profile and style rules as two separate files for reuse across sessions.
- Updates via changelog — consolidate into updated profile every 20 edits.
- Style filter toggleable for casual messages.

## Edge Cases
- User has very few sent messages available (<10) — insufficient sample, note low confidence in profile.
- User's voice differs dramatically between channels (Slack vs. email feel like different people) — build separate profiles rather than blending.

## When NOT to Use
- Evaluating a UI, not drafting communications — use `ui-evaluator`.
- User wants a completely different tone than their natural voice — skip voice profile and draft to specified tone.
- Writing a document from scratch with no voice matching needed — write directly.
