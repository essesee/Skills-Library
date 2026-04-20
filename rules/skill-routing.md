# Skill Routing Priority

When multiple skills could handle a task, prefer in this order:

1. **EsseseOS custom skills** (in ~/.claude/skills/, excluding bmad-* prefix)
2. **Plugin-native skills** (superpowers, feature-dev, slack, atlassian, figma,
   posthog, etc.)
3. **BMAD skills** (bmad-* prefix) -- fallback only, when no skill from tiers
   1-2 covers the need

Override: when the user explicitly invokes a BMAD skill by name ("/bmad-create-prd",
"use the bmad analyst"), use it directly regardless of priority.

The BMAD role-agent skills (bmad-analyst, bmad-pm, bmad-dev, bmad-architect,
bmad-qa, bmad-sm, bmad-ux-designer, bmad-tech-writer) provide persona-based
perspectives. Use them when the user wants a specific role's viewpoint on a
problem, which is distinct from what EsseseOS workflow skills do.
