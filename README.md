# Claude SMEs — Subject Matter Expert Framework

A framework for creating and sharing **Subject Matter Experts** (SMEs) for Claude Code. An SME is a self-contained package of deep domain knowledge that transforms Claude into a domain specialist — on demand, with zero overhead when not in use.

## What is an SME?

An SME sits alongside your existing skills, agents, and MCP servers:

```
.claude/
├── skills/          # What Claude can DO
├── agents/          # How Claude DELEGATES
├── smes/            # What Claude deeply KNOWS    ← SMEs live here
├── memory/          # What Claude REMEMBERS
└── commands/        # User slash commands
```

**Skills** do things. **Agents** orchestrate things. **SMEs** know things deeply.

An SME is completely dormant until called — it loads into a subagent, brings its domain expertise, does the work, returns the result, and releases its context. Zero cost when not in use.

## Quick Start

### 1. Install the SME runner skill

Copy the `sme-runner/` directory into your project:

```bash
cp -r sme-runner/SKILL.md /path/to/your/project/.claude/skills/sme/SKILL.md
```

### 2. Install an SME

Copy any SME directory into your project's `.claude/smes/`:

```bash
# Install one SME
cp -r smes/child-game-design /path/to/your/project/.claude/smes/

# Or install multiple
cp -r smes/3d-game-development /path/to/your/project/.claude/smes/
```

### 3. Use it

```
/sme child-game-design "Review this narration: 'Great job! You completed the task!'"
```

Claude spawns the SME as a subagent, loads its domain knowledge, does the review, and returns:

> **Verdict:** Fails Constraints
> **Findings:** "Great job!" is banned empty praise. "completed the task" is edu-jargon.
> **Recommendation:** Replace with scene description: "The monster catches the cake and does a happy dance!"

## Available SMEs

| SME | Domain | What It Knows |
|-----|--------|---------------|
| [child-game-design](smes/child-game-design/) | Kids 7-11 game UX | Piaget stages, age-appropriate UX, brand voice, accessibility, comedy-first pedagogy |
| [3d-game-development](smes/3d-game-development/) | Browser 3D games | Three.js, React Three Fiber, GLTF pipelines, animation, performance, post-processing |
| [prompt-engineering](smes/prompt-engineering/) | Teaching prompt eng | How to teach specificity through play, feedback design, task scaffolding |
| [character-director](smes/character-director/) | Character casting & personality | Character selection, personality design, development arcs, animation mapping for 28-character roster |
| [ece-professor](smes/ece-professor/) | Developmental psychology | Piaget, Vygotsky ZPD, NAEYC/AAP/COPPA compliance, age-band calibration, runtime guardrails |
| [prompt-writer](smes/prompt-writer/) | Story → system prompts | Claude system prompt authoring, vocabulary contracts, JSON schema, fallback scripts, golden cache |
| [story-writer](smes/story-writer/) | Narrative design | Story arcs, three-element success formulas, comedy-first failure paths, age-appropriate themes |
| [postgresql-expert](smes/postgresql-expert/) | PostgreSQL & pgvector | Schema design, migrations, pgvector optimization, RLS, partitioning, connection pooling, query analysis |

## How It Works

```
Normal operation:
  Claude + Skills + Agents + MCPs
  (SMEs don't exist in context — zero overhead)

When called:
  /sme child-game-design "review this text"
      │
      ▼
  SME runner reads sme.yaml (manifest)
  SME runner reads system-prompt.md (expertise)
  SME runner reads context.always files (constraints + heuristics)
      │
      ▼
  Subagent spawns with full domain knowledge
  Subagent does the work (can read project files)
  Subagent returns structured result
      │
      ▼
  Context released — SME is gone
  Main conversation continues (no residual overhead)
```

## Anatomy of an SME

```
my-sme/
├── sme.yaml              # Manifest: name, description, capabilities, tools
├── system-prompt.md       # THE prompt that makes Claude a domain expert
├── knowledge/             # Domain knowledge (loaded tiered)
│   ├── constraints.md     # Hard rules — NEVER violate (always loaded)
│   ├── heuristics.md      # "When X, do Y" patterns (loaded on activation)
│   ├── ontology.md        # Domain model, concepts, relationships (on demand)
│   ├── taste.md           # Good/bad examples (on demand)
│   └── glossary.md        # Domain vocabulary (on demand)
├── skills/                # SME-scoped skills (only available inside this SME)
└── examples/              # Few-shot examples for quality calibration
```

### Knowledge Tiers

| Tier | File | When Loaded | Purpose |
|------|------|-------------|---------|
| **Always** | constraints.md | Every invocation | Hard rules. Non-negotiable. |
| **Always** | heuristics.md | Every invocation | Pattern-matched judgment. |
| **On Demand** | ontology.md | When subagent needs it | The full domain model. |
| **On Demand** | taste.md | When subagent needs it | Good/bad examples. |
| **On Demand** | glossary.md | When subagent needs it | Vocabulary definitions. |

The "always" files are small (~50-100 lines). They load every time the SME is called. The "on demand" files are larger and only loaded when the subagent determines it needs them.

## Creating Your Own SME

Use the template:

```bash
cp -r template/ smes/my-new-sme/
```

Then edit:
1. `sme.yaml` — Name it, describe it, list its capabilities
2. `system-prompt.md` — Write the expertise prompt
3. `knowledge/constraints.md` — What must NEVER be violated
4. `knowledge/heuristics.md` — "When X, usually Y" judgment rules
5. `knowledge/ontology.md` — The domain model
6. Add examples in `examples/` for quality calibration

See the [template README](template/README.md) for detailed instructions.

## Design Principles

- **Dormant until called** — zero context cost when not in use
- **Subagent isolation** — each invocation is a fresh subagent, no cross-contamination
- **Non-breaking** — SMEs use existing Claude Code extension points, never modify them
- **Self-contained** — install = copy directory, uninstall = delete directory
- **Shareable** — each SME is an independent directory, share one at a time
- **Composable** — multiple SMEs can coexist, called independently or chained

## Sharing an SME

Each SME is a self-contained directory. Share it however you share directories:

```bash
# Zip one SME
zip -r child-game-design.zip smes/child-game-design/

# Git (whole repo)
git clone https://github.com/you/claude-smes .claude-smes-source
cp -r .claude-smes-source/smes/child-game-design .claude/smes/

# Direct copy
cp -r /path/to/smes/child-game-design /other/project/.claude/smes/
```

## License

MIT — use, modify, share freely.
