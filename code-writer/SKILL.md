---
name: code-writer
description: "Use when the user needs to write code, review a PR, understand unfamiliar code, generate from a screenshot, or set up a local dev environment. Trigger on: 'write code for,' 'review this PR,' 'explain this code,' 'build this feature,' 'set up the project locally,' 'what does this code do,' 'generate from this screenshot,' or any coding task beyond simple one-liners."
---

# Code Writer & Evaluator

## Purpose
Handle code generation, evaluation, explanation, and local environment management with repo-aware context.

## Dependencies

**Tools/APIs:** GitHub API (repo read, PR review, push)
**Other Skills:** voice-analyzer (for review comments in user's voice)

## Inputs
- GitHub repo URL or local codebase, plain-language description, screenshots/mockups, or code snippets for review

## Outputs
- Working code with optional inline comments
- Running local preview
- Review summaries in user's voice
- Plain-language explanations

## Modes

### Repo Ingestion (runs once per repo, cached)
1. Read file tree + config files first. Load specific source files on demand per task.
2. Generate a cached repo profile for future sessions.

### Writer Mode
1. Generate code following repo's existing patterns, naming, and style.
2. Include proper error handling. Auto-apply linting/formatting rules.
3. Inline comments togglable: ON for learning, OFF for speed (saves 30-40% tokens).
4. Generate unit tests as a follow-up step — never in the same call as code generation.

### Screenshot-to-Code
Parse screenshot into structured text (layout, components, styles). Drop image from context. Generate code from the structured description.

### Evaluator Mode
Review PRs for bugs, performance, readability, security, and style guide violations. Output review in user's voice.

### Explain Mode
Line-by-line walkthrough. Flag unusual patterns, non-obvious side effects, and hidden dependencies.

### Auto Local Environment
After code generation: install dependencies, set up env vars, start dev server, verify it runs.

## Context Rules
- Load file tree + configs first. Load specific files only as needed.
- Cache repo profile after first ingestion. Do not re-read the whole repo each time.
- Parse screenshots to structured text, then drop images from context.
- Generate unit tests in a follow-up call, not the same call as code generation.

## When NOT to Use
- Simple one-line fixes or config changes.
- Data queries — use `sql-data-analysis`.
