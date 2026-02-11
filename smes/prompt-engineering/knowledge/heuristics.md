# Heuristics — Prompt Engineering Education

## Task Design

- **When designing a new task:** Start with the premise comedy. "A skeleton at a birthday party" is inherently funny because skeletons don't have lungs to blow out candles. The comedy should exist BEFORE the kid types anything. Then design the specificity dimensions: what 4-5 details does a "perfect" description include?
- **When the specificity ladder feels unclear:** Map out the levels explicitly:
  - 1 detail (FUNNY_FAIL): "throw a party" → monster shows up to empty room
  - 2-3 details (PARTIAL_SUCCESS): "big cake and balloons" → cake is there but monster doesn't know what to do with it
  - 4+ details (FULL_SUCCESS): "chocolate cake, party hats, monster blows candles gently" → full celebration
- **When a funny fail isn't funny:** The comedy comes from the CONSEQUENCE of vagueness, not just the absence. "The robot stops at the river" isn't funny. "The robot walks INTO the river, pizza turns soggy, robot short-circuits and starts doing the robot dance" IS funny. The funnier the consequence, the more memorable the lesson.

## Feedback Writing

- **When writing a feedback tip:** Start with what the CHARACTER experienced ("The monster didn't know...") then offer 2-3 specific options ("what kind of cake? chocolate? vanilla? one made of pizza?"). The options model the KIND of specificity the kid should add.
- **When feedback sounds like a teacher:** Remove any mention of the child ("you", "your"), remove any educational language ("try to", "remember to", "think about"), and reframe as a game observation. "You forgot the bridge" → "The robot didn't know how to cross the river!"
- **When feedback is too abstract:** Check if it references something VISIBLE in the 3D scene. "Be more descriptive" → abstract, invisible. "The monster's plate is empty — what should it eat?" → concrete, visible in the scene.
- **When you need to hint without lecturing:** Use curiosity questions. "What happens if the rocket doesn't have a door?" is better than "You need to describe how the dog gets into the rocket."

## System Prompt Design

- **When writing a system prompt for Claude:** Structure it as: (1) Role ("You are the director of a 3D comedy theater"), (2) Task context ("A kid has described..."), (3) Vocabulary contract (EXHAUSTIVE list of available assets), (4) Evaluation rubric (what counts as each success level), (5) Few-shot examples (1 per level), (6) Output format (JSON scene script).
- **When Claude generates boring funny fails:** Add explicit instruction: "FUNNY_FAIL scenes should be MORE entertaining than FULL_SUCCESS scenes. The comedy comes from the absurd consequences of vagueness — make the audience laugh out loud."
- **When Claude's feedback sounds teacherly:** Add explicit anti-patterns in the system prompt: "NEVER say: 'try to be more specific', 'good job', 'you forgot to'. ALWAYS say: 'The [character] didn't know [what was missing]! What if [2-3 funny options]?'"
- **When Claude references assets that don't exist:** The vocabulary contract isn't strict enough. Add: "You MUST ONLY reference actors, props, and animations from the lists above. If an asset doesn't appear in the list, it DOES NOT EXIST. Never invent new asset names."

## Difficulty Calibration

- **For ages 7-8 (early concrete operational):** Tasks should have 3 specificity dimensions max. Feedback should name the missing dimension explicitly. Options in feedback should be simple, familiar concepts.
- **For ages 9-10 (established concrete operational):** Tasks can have 4-5 specificity dimensions. Feedback can be slightly more open-ended ("what else might the robot need?"). Kids at this age can handle "what if" hypotheticals.
- **For ages 10-11 (transitioning to formal operational):** Tasks can include implicit dimensions that aren't stated. Feedback can use gentle prompting questions. These kids can start to reflect on WHY specificity matters.

## Cache Design

- **When building golden response cache:** Cover the full specificity spectrum: 2-3 vague inputs (funny fails), 2-3 medium inputs (partial success), 2-3 specific inputs (full success) per task. Also include edge cases: off-topic input, single-word input, extremely creative input.
- **When a cached response doesn't match well:** The fuzzy matching threshold (0.6) may be too loose or tight. Test with 10+ real kid-like inputs. Kids misspell, use fragments, and combine ideas unpredictably.
