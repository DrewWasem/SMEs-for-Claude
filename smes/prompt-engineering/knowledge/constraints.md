# Hard Constraints — Prompt Engineering Education

## The Invisibility Rule

- **The word "prompt" NEVER appears in kid-facing text** — say "description" or "plan"
- **NEVER explicitly teach prompt engineering** — the learning happens through play, not instruction
- **NEVER tell kids they're "learning to use AI"** — they're playing a game
- **NEVER reference "AI", "Claude", "language model", or any technical term** in kid-facing text
- **NEVER use abstract meta-cognitive language** — "be more specific" is abstract; "the monster didn't know which cake" is concrete

## Feedback Rules

- **Feedback MUST reference the game world** — characters, props, scenes. Never the child's input.
- **Feedback MUST point to what was MISSING** — not what was wrong. Missing ≠ wrong.
- **Feedback MUST suggest 2-3 concrete options** — "a bridge? a jetpack? a really long pizza?" gives the kid a mental model of specificity
- **Feedback MUST be max 2 sentences** — kids won't read more
- **Feedback NEVER assigns blame** — "the robot didn't know" not "you forgot to tell"

## Comedy Requirements

- **Funny fails MUST be funnier than success** — if the fail isn't genuinely funny, redesign it
- **Comedy comes from the GAP** — between what was intended and what happened
- **Comedy MUST be visual** — the 3D animation tells the joke, not the text
- **Comedy NEVER mocks the child** — the game laughs WITH them at the absurd situation
- **Every funny fail MUST demonstrate a missing element** — comedy IS the curriculum

## Task Design

- **Every task MUST have a clear "specificity ladder"** — a spectrum from vague (1 detail) to specific (4+ details)
- **Vague descriptions MUST produce entertaining results** — never errors or empty scenes
- **Specific descriptions MUST produce satisfying results** — the kid should feel like a wizard
- **The task scenario MUST be inherently funny** — the comedy is baked into the PREMISE, not just the fail states
- **Maximum 4-5 "specificity dimensions" per task** — more than that overwhelms 7-11 year olds

## System Prompt Design (for Claude evaluation)

- **System prompts MUST list the vocabulary contract** — every actor, prop, animation, and effect that exists
- **System prompts MUST define success levels** — FULL_SUCCESS, PARTIAL_SUCCESS, FUNNY_FAIL with clear criteria
- **System prompts MUST enforce the 6-action limit** — scene scripts max 6 actions
- **System prompts MUST request concrete feedback** — instructing Claude to reference the game world, not the child
- **System prompts MUST include few-shot examples** — at least 1 per success level
