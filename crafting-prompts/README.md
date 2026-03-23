# crafting-prompts Skill

A Claude Code skill that turns Claude into a **Prompt Design Assistant** — a structured tool for crafting, refining, and evaluating prompts for any purpose.

## What It Does

When triggered, Claude follows a 5-step workflow:

1. **Clarification** — Asks one question at a time to understand your goal, audience, tone, format, and constraints before generating anything.
2. **Candidate Prompt Generation** — Produces 3–4 named candidates in different styles, each drawing from a library of 15 named prompt patterns and Strunk & White writing principles.
3. **Predicted Output Simulation** — Simulates what a typical LLM would produce for each candidate, including tone, structure, and likely failure modes.
4. **Critique** — Compares candidates, flags issues (ambiguity, verbosity, lack of control), and evaluates writing quality.
5. **Recommendation** — Presents the best or a hybrid prompt, adds a Fact Check List if needed, and repeats the recommended prompt verbatim for immediate use (Tail Generation).

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill file — workflow steps and ground rules |
| `prompt-patterns.md` | Reference for all 15 named prompt patterns with examples |
| `writing-style.md` | Strunk & White writing principles applied to prompt evaluation |
| `README.md` | This file |

## Installation

### Claude Code

Copy the entire folder to your skills directory:

```bash
cp -r crafting-prompts ~/.claude/skills/
```

Verify it's installed by asking Claude to craft a prompt — it should trigger automatically.

### Other AI CLI tools

Check your tool's documentation for the skills directory location, then copy the folder there.

## How to Use

The skill triggers automatically when you use phrases like:

- "Help me write a prompt for..."
- "Create a prompt that..."
- "Design a prompt for X"
- "I need a prompt to..."
- "Craft a prompt that..."
- "Refine this prompt..."

Claude will ask ONE clarifying question first. Answer it. Claude will ask another if needed. Once it has enough information, it delivers all 5 steps in a single response.

## Customization Tips

**Add more patterns:** Extend `prompt-patterns.md` with your own patterns following the same format (name, fundamental statements, examples).

**Swap the style guide:** Replace `writing-style.md` with your own writing principles — house style, brand voice guidelines, or domain-specific conventions.

**Adjust candidate count:** Edit the "3–4 candidates" line in `SKILL.md` if you want more or fewer options.

**Skip simulation/critique:** If you want a fast one-shot result without the full workflow, just tell Claude: "Give me a single production-ready prompt for X." Steps 3–4 will be skipped.

## Credits

- Prompt patterns based on established prompt engineering patterns from academic research in large language model prompting.
- Writing principles derived from Strunk & White, *The Elements of Style* (4th ed.).
