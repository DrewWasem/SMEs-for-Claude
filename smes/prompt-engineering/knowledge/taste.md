# Taste — Prompt Engineering Education

## Good Task Design

### GOOD — "Skeleton's Surprise Birthday Party"
- **Why it works:** Skeletons don't have lungs (can't blow candles), don't know what birthdays are (inherent comedy), and the mismatch between "scary skeleton" + "birthday party" is funny before the kid types anything.
- **Specificity dimensions:** (1) What kind of cake, (2) Who's invited, (3) How to handle candles without lungs, (4) Decorations, (5) Entertainment
- **Best funny fail:** "The skeleton tries to blow out candles. Nothing happens. It tries harder. Its jaw falls off."

### BAD — "Plan a Party"
- **Why it fails:** Too generic. No inherent comedy in the premise. No character-specific obstacles. The funny fail would just be "the party is boring" — which isn't funny, it's just... accurate.
- **Missing:** A character with specific limitations that create comedy. The GAP between the character and the situation is where the humor lives.

## Good Feedback

### GOOD
"The skeleton opened its present but there was nothing inside! What should the present be? A new bone? A party hat? A tiny drum?"

**Why:** References the game world (skeleton, present). Points to what's MISSING (the present contents). Offers 3 concrete, funny options that model the KIND of specificity needed. Doesn't blame the kid.

### BAD
"Try to include more details about the gifts in your description."

**Why:** Abstract ("more details"). References the kid's description, not the game world. Gives no concrete options. Sounds like a teacher's margin note.

## Good System Prompt Design

### GOOD evaluation rubric
```
FUNNY_FAIL triggers when:
- Kid only names the task ("throw a party") with no specifics
- Scene: Skeleton stands alone in empty dungeon, looks around confused,
  a single deflated balloon drifts past
- The humor: The CONTRAST between "party" expectation and sad reality
```

**Why:** The rubric describes a SCENE, not just a label. It tells Claude what the funny fail LOOKS like, so Claude can generate a visual comedy beat.

### BAD evaluation rubric
```
FUNNY_FAIL triggers when:
- Description is vague or lacks detail
- Response should be humorous
```

**Why:** Too abstract. Claude doesn't know what "humorous" means in this specific context. It needs to know that the humor comes from VISUAL CONTRAST — the gap between expectation and result.

## Good Cache Entries

### GOOD spectrum for one task
```
"party"                    → FUNNY_FAIL (skeleton alone, sad)
"birthday cake for skeleton" → PARTIAL (cake exists but skeleton doesn't know what to do)
"throw a big party with cake and friends and music" → FULL_SUCCESS
"give the skeleton a cake made of bones" → FULL_SUCCESS + creativity bonus
```

**Why:** Covers the full specificity ladder. Each input is a REALISTIC kid input (short, informal, sometimes creative). The responses get progressively more satisfying.

### BAD cache entries
```
"Please plan a birthday party with decorations and cake" → FULL_SUCCESS
```

**Why:** No kid types like this. It's an adult sentence. Cache entries should sound like actual 7-11 year olds: fragments, misspellings, creative tangents, single words.
