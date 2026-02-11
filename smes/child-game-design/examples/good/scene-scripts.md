# Good Scene Scripts

## FULL_SUCCESS — Skeleton Birthday
```json
{
  "success_level": "FULL_SUCCESS",
  "narration": "The knight and mage arrive with presents as the skeleton cheers by the decorated table!",
  "actions": [
    { "type": "spawn", "target": "skeleton_warrior", "position": "center" },
    { "type": "spawn", "target": "banner_blue", "position": "left" },
    { "type": "spawn", "target": "cake_birthday", "position": "right" },
    { "type": "move", "target": "knight", "to": "left", "style": "arc" },
    { "type": "animate", "target": "skeleton_warrior", "anim": "Cheering" },
    { "type": "react", "effect": "confetti-burst", "position": "center" }
  ],
  "missing_elements": [],
  "prompt_feedback": "The skeleton's got guests, decorations, AND cake — this is one amazing dungeon party!"
}
```

## FUNNY_FAIL — Skeleton Birthday
```json
{
  "success_level": "FUNNY_FAIL",
  "narration": "The skeleton stands alone in an empty dungeon. It waves at nobody.",
  "actions": [
    { "type": "spawn", "target": "skeleton_warrior", "position": "center" },
    { "type": "animate", "target": "skeleton_warrior", "anim": "Waving" },
    { "type": "animate", "target": "skeleton_warrior", "anim": "Idle_A" },
    { "type": "react", "effect": "question-marks", "position": "center" }
  ],
  "missing_elements": ["party guests", "decorations", "cake or presents"],
  "prompt_feedback": "The skeleton doesn't know who to invite or what to set up! Try describing which adventurers should come and what the dungeon needs for a party."
}
```

## Key Patterns
- FULL_SUCCESS uses 5-6 actions with variety (spawn + move + animate + react)
- FUNNY_FAIL uses fewer actions (3-4) — emptiness IS the comedy
- Narration describes the SCENE, never the kid's input
- Feedback references the game world, gives ONE concrete next step
- Missing elements are game-world terms ("party guests"), not abstract ("more detail")
