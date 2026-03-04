---
name: deployment-assistant
description: "Use when the user needs to deploy code, push to staging, ship to production, or troubleshoot a failed deployment. Trigger on: 'deploy this,' 'push to staging,' 'ship to prod,' 'deployment failed,' 'rollback,' 'CI/CD pipeline,' or any request involving getting code to a running environment."
---

# Deployment Assistant

## Purpose
Walk through deployments step-by-step with verification at each checkpoint. Procedural support, not generative logic.

## Dependencies

**Tools/APIs:** GitHub API (push, branch management), CI/CD pipeline access
**Other Skills:** code-writer (repo context and environment awareness)

## Inputs
- Target environment, repo context (branch, recent commits), CI/CD pipeline profile (cached)

## Outputs
- Step-by-step deployment walkthrough with verification checkpoints, blocker flags, rollback instructions

## Workflow

### Pre-Deployment
1. Load only the target environment config.
2. Check cached CI/CD pipeline profile for triggers, stages, and typical failure points.
3. Verify branch is clean, tests passing, and ready to deploy.
4. Present numbered deployment plan.

### Deployment Execution
Walk through each step: present, execute/instruct, verify completion, flag blockers, proceed only after verification. Typical steps: git push, CI/CD trigger, env var verification, DB migration, health check, smoke test.

### Error Handling
Reactive — load error logs only when failure occurs. Diagnose, recommend fix or rollback with exact steps.

### Rollback Procedure
Maintain per-environment rollback steps: revert deploy, restore previous version, verify rollback, post-rollback health checks.

## Context Rules
- Load only target environment config, not all environments.
- Cache CI/CD pipeline profile. Do not re-read each deployment.
- Load error logs only when a failure occurs.
- Store checklist templates as reference files, loaded per environment.

## When NOT to Use
- Writing code — use `code-writer`.
- CI/CD pipeline design — out of scope.
