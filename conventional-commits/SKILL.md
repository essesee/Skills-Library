---
name: conventional-commits
description: Use when committing code, creating commit messages, or when user says commit, /commit, git commit. Formats all commit messages as Conventional Commits automatically.
---

# Conventional Commits

Format **every** commit message using the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) spec. This is non-negotiable — never write a freeform commit message.

## Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

**All parts of the subject line are lowercase** (no sentence case).

## Types

| Type | When |
|------|------|
| `feat` | New functionality (MINOR in semver) |
| `fix` | Bug fix (PATCH in semver) |
| `refactor` | Code change that neither fixes nor adds |
| `docs` | Documentation only |
| `test` | Adding or updating tests |
| `chore` | Maintenance, deps, config |
| `ci` | CI/CD changes |
| `build` | Build system or external deps |
| `perf` | Performance improvement |
| `style` | Formatting, whitespace, semicolons |

## Scope Detection

Infer scope from changed files. Use the most specific common ancestor:

- Single module: `fix(auth): handle expired tokens`
- Single component: `feat(search-filters): add destination type dropdown`
- API layer: `refactor(api): extract booking validation`
- Multiple unrelated areas: omit scope — `chore: update dependencies`

**Scope = the thing being changed**, not the file path. Think domain, not directory.

## Breaking Changes

Only for **consumer-facing** API or contract changes. Internal refactors are NOT breaking changes even if they restructure code significantly.

Two signals required — both `!` AND footer:

```
refactor(api)!: rename /agents endpoint to /users

BREAKING CHANGE: /api/v2/agents removed. Use /api/v2/users instead.
```

Do NOT add BREAKING CHANGE for: internal module restructuring, renaming private functions, extracting helper classes, or moving code between internal files.

## Mixed Changes

Pick the **primary** type. Mention secondary in body:

```
refactor(auth): extract token validation into dedicated service

Also fixes a bug where expired tokens were not properly rejected.
```

Do NOT list changed files in the body — git tracks that.

## Description Rules

- Lowercase, no period at end
- Imperative mood: "add filter" not "added filter" or "adds filter"
- Under 50 characters (subject line)
- Focus on **why** or **what**, not **how**

## Quick Reference

```
feat(search): add destination type filter
fix(bookings): handle missing check-in date
docs: add getting started guide and fix broken links
refactor(auth): extract token validation service
ci: add staging deployment workflow
perf(api): cache frequently accessed club data
test(bookings): add coverage for edge cases
build: upgrade typescript to 5.4
chore: clean up unused dependencies
feat(api)!: replace /agents with /users endpoint
```
