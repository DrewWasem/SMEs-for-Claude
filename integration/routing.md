# SME Routing & Automation

By default, SMEs are passive — they only activate when you manually type `/sme <name> "task"`. This document describes how to wire SMEs into your existing workflow so they're suggested automatically.

## The Problem

You have 7 expert consultants on call, but no receptionist routing calls to them. YOU have to remember which SME to invoke for which task. If you forget, the SME never gets consulted.

## The Solution: Routing Table

Add an SME routing table to your orchestration skill (e.g., `/conductor`). The routing table maps task types to relevant SMEs:

```markdown
## SME Routing Table

| Task involves... | Consult SME | When |
|---|---|---|
| Writing kid-facing text | `child-game-design` | Planning, Implementation |
| Story/narrative design | `story-writer` | Planning |
| System prompts or API interaction | `prompt-writer` | Planning, Validation |
| 3D scenes, models, lighting | `3d-game-development` | Research, Planning |
| Educational content | `ece-professor` | Validation |
```

Customize this for your project — the specific SMEs and task types will differ.

## Where to Wire SME Awareness

### 1. Initial Assessment (every non-trivial request)

Add a lightweight check to your orchestration flow. Before starting work, scan the task against the routing table. If a match, mention it in one sentence:

> "This touches kid-facing text. Want me to run it by `child-game-design`?"

If the user says no, proceed without. This is awareness, not friction.

### 2. Research Phase

After synthesizing findings, note which SMEs would add value:

```markdown
## Recommended SME Consultation
- `/sme prompt-writer "Review the system prompt structure found in src/prompts/"`
```

### 3. Planning Phase

Before finalizing a plan, consult domain SMEs:

```markdown
### Step N: SME Review Gate
Before saving the plan, consult relevant SMEs from the routing table.
Add their feedback under: ## SME Review
```

### 4. Implementation Phase

At the start of each phase, lightweight domain check:
- Phase involves kid-facing text? → `/sme child-game-design "review this text"`
- Phase involves 3D changes? → `/sme 3d-game-development "review this setup"`

### 5. Validation Phase

Run SME reviews in parallel as part of validation:

```markdown
### SME Validation Pass
| SME | Verdict | Key Findings |
|-----|---------|-------------|
| child-game-design | APPROVED | No issues |
| ece-professor | ISSUES | Narration too long (22 words) |
```

### 6. Session Start / Daily Standup

Suggest relevant SMEs based on today's planned work:

> "SMEs for today: `/sme story-writer`, `/sme ece-professor` — relevant for story content work."

## Compaction Survival

SME state can be lost when the context window compacts. To preserve it:

### Session Log

The SME runner logs every invocation to `.claude/memory/.sme-session-log`:
```
[2026-02-12T14:30:00] /sme child-game-design — "review narrations" — Result: 2 forbidden words found
```

### Pre-Compact Hook

Before compaction, save the SME session log to the compaction summary:

```bash
# In pre_compact_remember.sh
SME_LOG="$MEMORY_DIR/.sme-session-log"
if [ -f "$SME_LOG" ]; then
    echo "## SME Consultations This Session"
    cat "$SME_LOG"
fi
```

### Post-Compact Hook

After compaction, re-inject:
1. The full SME roster (so Claude knows what's available)
2. The session log (so Claude doesn't re-run the same consultations)

```bash
# In post_compact_recall.sh
echo "SMEs: /sme <name> \"task\" — Available: story-writer, ece-professor, prompt-writer, ..."

SME_LOG="$MEMORY_DIR/.sme-session-log"
if [ -f "$SME_LOG" ]; then
    echo "--- SME STATE (pre-compaction) ---"
    cat "$SME_LOG"
    echo "--- END SME STATE ---"
fi
```

## SME Gap Detection

Optionally, watch for signals that a new SME should be created:

- You're looking up the same domain docs repeatedly
- The user keeps correcting domain-specific assumptions
- External documentation is consulted for a domain that could be encoded

When detected, mention it once: "I keep needing {domain} expertise and we don't have an SME for that. Want me to create one?" If declined, drop it.

## Design Principles

- **Awareness, not friction** — one sentence suggestion, not a mandatory gate
- **Skippable** — user can always decline, proceed without SME
- **Lightweight** — don't turn every task into an SME consultation ceremony
- **Compaction-safe** — session log preserves state across context resets
- **Progressive** — start with routing table, add automation as patterns emerge
