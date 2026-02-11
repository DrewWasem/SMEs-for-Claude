# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A framework for creating **Subject Matter Experts (SMEs)** for Claude Code. An SME is a self-contained package of domain knowledge that transforms Claude into a domain specialist via a subagent — dormant until called, zero context cost when not in use.

This is a **content-only repository** — no build system, no tests, no dependencies. All files are Markdown and YAML.

## Repository Structure

```
sme-runner/SKILL.md    — The skill that invokes SMEs (installed to .claude/skills/sme/)
smes/                  — Concrete SME implementations (3 included)
template/              — Starter template for creating new SMEs
docs/                  — Framework documentation
```

## How SMEs Work

The SME runner (`sme-runner/SKILL.md`) is a Claude Code skill triggered by `/sme <name> "<task>"`. It:

1. Reads `.claude/smes/<name>/sme.yaml` (manifest) and `system-prompt.md` (expertise prompt)
2. Reads all `context.always` knowledge files (constraints + heuristics)
3. Spawns a `general-purpose` subagent via the Task tool with the combined prompt + knowledge + user task
4. The subagent can Read additional on-demand knowledge files as needed
5. Returns a structured result (Verdict / Findings / Recommendations)

## SME Anatomy — The File Contracts

Every SME follows this structure. The filenames and roles are conventions enforced by the runner:

| File | Role | Loading |
|------|------|---------|
| `sme.yaml` | Manifest: name, description, capabilities, tools, context tiers | Read by runner |
| `system-prompt.md` | The core expertise prompt that makes Claude a domain specialist | Always loaded |
| `knowledge/constraints.md` | Hard rules — NEVER violate (~50 lines, keep tight) | Always loaded |
| `knowledge/heuristics.md` | "When X, do Y" judgment patterns | Always loaded |
| `knowledge/ontology.md` | Full domain model, concepts, relationships | On demand |
| `knowledge/taste.md` | Good/bad examples for quality calibration | On demand |
| `knowledge/glossary.md` | Domain vocabulary definitions | On demand |

**"Always" files must stay small** (~50-100 lines) because they load on every invocation. On-demand files can be larger.

## Included SMEs

| SME | Domain |
|-----|--------|
| `child-game-design` | Kids 7-11 game UX, Piaget stages, comedy-first pedagogy, brand voice |
| `3d-game-development` | Three.js, React Three Fiber, GLTF pipelines, animation, performance |
| `prompt-engineering` | Teaching prompt engineering through play, feedback design, task scaffolding |

## Creating a New SME

```bash
cp -r template/ smes/my-new-sme/
```

Edit in this order: `sme.yaml` → `system-prompt.md` → `knowledge/constraints.md` → `knowledge/heuristics.md` → `knowledge/ontology.md` → add taste examples.

## Key Conventions

- **sme.yaml `context.always`** lists files the runner concatenates into the subagent prompt. Only constraints and heuristics belong here.
- **sme.yaml `tools`** controls which tools the subagent gets. Most SMEs need only `Read`, `Glob`, `Grep`. The 3d-game-development SME also includes `Bash`.
- **system-prompt.md** must define: areas of expertise, how the SME works (load constraints → apply heuristics → reference examples → read project files), response format, and scope boundaries ("You Are Not").
- **Taste examples are the most powerful calibration tool** — 5 good/bad pairs teach more than 50 rules.

## Installation (Into a Target Project)

SMEs are installed by copying directories into the target project:
```bash
cp -r sme-runner/SKILL.md /target/.claude/skills/sme/SKILL.md
cp -r smes/child-game-design /target/.claude/smes/
```
