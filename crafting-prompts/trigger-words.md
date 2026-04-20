# Trigger Words Reference

A trigger word is a phrase that controls how a model reasons, formats output, or scopes its work. Embed them naturally into candidate prompts — don't list them as a menu. Annotate usage in the rationale line.

---

## 1. Clarity & Framing

**Principle:** Control how the model reasons and presents output.

| Phrase | When to embed |
|--------|--------------|
| "Think step by step" | Multi-part problems, logical chains, debugging |
| "Show your reasoning" | Decisions where visible logic builds trust |
| "Be concise" | Output that will be scanned, not read |
| "Be thorough" | High-stakes or comprehensive coverage needed |
| "Don't explain, just do it" | Simple transformations; explanation adds no value |

---

## 2. Constraints & Scope

**Principle:** Define what's in and out of bounds. Do NOT paste verbatim phrases like "minimal changes" — translate the principle into task-specific language.

Examples of translation:
- "Only update the summary section, leave the rest intact"
- "Focus on tone adjustments, not content restructuring"
- "Do not add new sections or change the structure"

---

## 3. Output Format

**Principle:** Shape the structure of the model's output so it fits directly into the user's workflow.

| Phrase | When to embed |
|--------|--------------|
| "Return only the revised text" | When surrounding explanation is noise |
| "As a table" | Comparisons, structured data |
| "As bullet points" | Scannable lists, action items |
| "Give me 3 options" | When alternatives improve decision quality |
| "In under [N] words" | Hard length constraints |

---

## 4. Expertise & Role

**Principle:** Prime domain-specific knowledge and calibrate depth/tone to the audience.

| Phrase | When to embed |
|--------|--------------|
| "You are an expert in X" | Technical or specialized domains |
| "Assume I am a [persona]" | Calibrate depth (e.g., "a busy executive", "a first-year engineer") |
| "What would a [role] say?" | Force a perspective shift (security, legal, UX) |

---

## 5. Quality & Verification

**Principle:** Build self-review and risk-surfacing into the prompt so the model critiques its own output.

| Phrase | When to embed |
|--------|--------------|
| "What are the tradeoffs?" | Decisions with real costs/benefits |
| "What could go wrong?" | Risk-surfacing before implementation |
| "What am I missing?" | Blind-spot detection |
| "Double-check this" | When accuracy matters and errors are costly |

---

## 6. Task Shaping

**Principle:** Control the model's process — when it acts, when it pauses, and how it structures its work.

| Phrase | When to embed |
|--------|--------------|
| "Explain your plan before executing" | High-stakes or irreversible actions |
| "Before answering, ask me clarifying questions" | Ambiguous inputs where wrong assumptions are costly |
| "Work through this in sections" | Long or complex tasks needing structure |
| "Do this without asking for confirmation" | Low-stakes tasks where back-and-forth wastes time |

---

## 7. Unconstrained (Follow-up only)

**Principle:** Surface the ideal design the user didn't know to ask for.

Reserved for the optional follow-up line at the end of Step 5:
> "Want a version with no restrictions?"

Do NOT embed this in candidate prompts. It is an offer, not a constraint.

---

## How to Apply When Writing Candidates

1. **Choose 1–2 trigger categories** most relevant to the goal.
2. **Embed naturally** — trigger words should read as intentional instructions, not bolted-on phrases.
3. **Rationale annotation** — note the category and why in one line: e.g., "Uses Expertise & Role + Output Format: primes domain depth and returns clean text."

## How to Apply During Critique (Step 4)

For each candidate, evaluate:
- Which trigger categories are represented?
- Do the chosen triggers fit the goal?
- What's missing? (e.g., no Quality & Verification trigger on a high-stakes task)
- Are any triggers redundant or fighting each other?
