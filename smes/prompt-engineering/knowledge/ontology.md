# Ontology — Teaching Prompt Engineering Through Play

## The Core Insight

Prompt engineering is the skill of writing specific, structured instructions that produce desired outcomes from an AI system. For adults, this is taught through documentation, examples, and practice with chat interfaces. For kids 7-11, this must be taught through PLAY — specifically, through a game where:

1. **Vague input → Funny chaos** (the kid sees what happens when instructions are incomplete)
2. **Specific input → Satisfying magic** (the kid sees what happens when instructions are complete)
3. **The gap between these two outcomes** is the entire curriculum

The kid never hears the words "prompt engineering." They just learn that **specific words create specific results**.

## The Specificity Ladder

```
Level 1 — "do the thing"
│  → FUNNY_FAIL: Characters stand around confused
│  → Teaches: Something is needed beyond the bare minimum
│
Level 2 — "make the monster eat cake"
│  → PARTIAL_SUCCESS: Monster eats, but it's a tiny generic cake
│  → Teaches: WHAT you describe matters
│
Level 3 — "give the monster a giant chocolate cake"
│  → PARTIAL_SUCCESS+: Big chocolate cake appears, but monster doesn't know what to do
│  → Teaches: HOW is as important as WHAT
│
Level 4 — "give the monster a giant chocolate cake and have it blow out the candles gently"
│  → FULL_SUCCESS: Monster carefully blows candles, cake is perfect, confetti!
│  → Teaches: Covering ALL dimensions creates magic
│
Level 5 — (rare, delightful) "give the monster a cake made of pizza because it's never had birthday cake and doesn't know the difference"
│  → FULL_SUCCESS + surprise bonus: Claude generates a creative scene with the pizza-cake
│  → Teaches: Creativity + specificity = extraordinary results
```

## Specificity Dimensions

Every task has 3-5 dimensions that a "complete" description should cover:

| Dimension | What It Means | Example (Monster Party) |
|-----------|--------------|------------------------|
| **WHAT** | The core object/action | What kind of cake? |
| **HOW** | The method/approach | How does the monster eat it? |
| **WHERE** | The spatial context | Where is the party? |
| **WHO** | Other characters/elements | Who else is at the party? |
| **WHEN/SEQUENCE** | Order of events | What happens first? Then what? |

A FUNNY_FAIL covers 0-1 dimensions. A PARTIAL_SUCCESS covers 2-3. A FULL_SUCCESS covers 4+.

## The Three-Tier Response System

```
Kid types description
    │
    ├── Tier 1: Cache (instant)
    │   Fuzzy match against pre-generated responses
    │   Covers common/demo inputs
    │
    ├── Tier 2: Live API (1-8 seconds)
    │   Claude Opus 4.6 generates a fresh scene script
    │   Result is cached for future matches
    │
    └── Tier 3: Fallback (instant)
        Pre-written generic funny-fail response
        Always available, never shows an error
```

## The Evaluation Rubric

Claude's system prompt contains a rubric for scoring kid descriptions:

```
FULL_SUCCESS (4+ specificity dimensions covered):
- Kid described WHAT, HOW, and at least 2 other dimensions
- Scene script shows complete, satisfying outcome
- Celebration effects triggered

PARTIAL_SUCCESS (2-3 dimensions):
- Kid covered the basics but missed important details
- Scene plays but with visible gaps
- Feedback points to what's missing

FUNNY_FAIL (0-1 dimensions):
- Kid was vague or only covered one aspect
- Scene produces hilarious chaos
- Comedy demonstrates what was missing better than words could
```

## The Comedy-Curriculum Link

| Comedy Element | What It Teaches |
|---------------|-----------------|
| Monster eats the table (no cake specified) | The WHAT dimension matters |
| Robot walks into river (no crossing plan) | Anticipating obstacles matters |
| T-Rex breaks the chair (no size consideration) | Context and constraints matter |
| Octopus tangles in guitar strings (no plan for 8 arms) | Complex situations need structured approaches |
| Knight swings sword at control panel (wrong tool for context) | Matching solutions to the situation matters |

Every funny fail is a CONCRETE demonstration of a missing specificity dimension. The comedy IS the lesson.
