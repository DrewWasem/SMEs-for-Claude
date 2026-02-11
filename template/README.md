# SME Template

Use this template to create a new Subject Matter Expert.

## Steps

1. **Copy this directory** to your SMEs collection:
   ```bash
   cp -r template/ smes/my-sme-name/
   ```

2. **Edit `sme.yaml`** — Name it, describe the domain, list capabilities

3. **Write `system-prompt.md`** — This is the most important file. It transforms Claude from a generalist into YOUR domain expert. Be specific about:
   - What areas of expertise this SME covers
   - How it should approach problems
   - What response format to use
   - What it is NOT (to prevent scope creep)

4. **Write knowledge files:**
   - `constraints.md` — Hard rules (small, always loaded). Things that must NEVER happen.
   - `heuristics.md` — Judgment patterns (loaded on activation). "When X, do Y."
   - `ontology.md` — The domain model (on demand). Concepts, relationships, how things work.
   - `taste.md` — Good/bad examples (on demand). Quality calibration.
   - `glossary.md` — Vocabulary (on demand). Domain-specific terms.

5. **Add examples** in `examples/` — Few-shot demonstrations of good and bad work in your domain.

6. **Test it:**
   ```
   /sme my-sme-name "review this [thing in your domain]"
   ```

## Tips

- **Constraints should be small** (~50 lines). They load every time. Keep them tight.
- **Be concrete, not abstract** in all knowledge files. "NEVER use red for failure" beats "use appropriate colors."
- **Include taste examples** — they're the most powerful calibration tool. Claude learns more from 5 good/bad pairs than 50 rules.
- **Test with edge cases** — the best SMEs handle ambiguous situations well because their heuristics cover gray areas.

## Installation

Copy your finished SME to any project:
```bash
cp -r smes/my-sme-name /path/to/project/.claude/smes/
```

Make sure the project has the SME runner skill installed:
```bash
cp sme-runner/SKILL.md /path/to/project/.claude/skills/sme/SKILL.md
```
