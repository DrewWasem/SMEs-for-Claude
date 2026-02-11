# SME Runner Skill

Invoke a Subject Matter Expert (SME) — a domain-specialized subagent with deep knowledge that loads on demand and releases when done.

## Trigger

- `/sme <name> "<task>"` — invoke a specific SME with a task
- `/sme list` — list all installed SMEs
- `/sme info <name>` — show an SME's capabilities

## How It Works

1. Read `.claude/smes/<name>/sme.yaml` to get the manifest
2. Read `.claude/smes/<name>/system-prompt.md` to get the domain expertise prompt
3. Read all files listed under `context.always` in the manifest
4. Spawn a subagent (via Task tool) with:
   - The system prompt as the agent's instructions
   - The always-load knowledge as initial context
   - The user's task as the prompt
   - Tools from the manifest's `tools` list
5. The subagent can Read additional knowledge files on demand
6. Return the structured result to the parent conversation
7. Subagent context is released — zero residual overhead

## Invocation

When the user runs `/sme <name> "<task>"`:

### Step 1: Validate
- Check that `.claude/smes/<name>/sme.yaml` exists
- If not, list available SMEs and ask the user to pick one

### Step 2: Load
- Read `sme.yaml` for manifest
- Read `system-prompt.md` for the domain prompt
- Read each file listed in `context.always`
- Concatenate: system-prompt + always-load knowledge

### Step 3: Spawn
Use the Task tool to launch a subagent:
```
Task(
  subagent_type: "general-purpose",
  description: "SME: <name> — <short task description>",
  prompt: """
    [SYSTEM PROMPT FROM system-prompt.md]

    [ALWAYS-LOAD KNOWLEDGE FILES]

    ---
    SME ROOT: .claude/smes/<name>/
    You can Read additional files from knowledge/, skills/, and examples/ as needed.
    ---

    TASK: <user's task>
  """
)
```

### Step 4: Return
Present the subagent's response to the user.

## List Command

When the user runs `/sme list`:
- Glob for `.claude/smes/*/sme.yaml`
- Read each manifest's `name` and `description`
- Present as a table

## Info Command

When the user runs `/sme info <name>`:
- Read `.claude/smes/<name>/sme.yaml`
- Display: name, description, capabilities, knowledge files, tools

## Installation

Copy this file to your project:
```
.claude/skills/sme/SKILL.md
```

Then install SMEs into:
```
.claude/smes/<sme-name>/
```
