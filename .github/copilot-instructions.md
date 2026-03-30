# Agent Skill Creator — Project Guidelines

## What This Repo Is

A template repository for creating and iterating on GitHub Copilot agent skills and custom agents. It contains two built-in skills (`skill-creator` and `agent-customization`) and a custom agent (`agent-creator`).

## Repository Layout

- `.github/skills/` — All skills live here, one folder per skill with a `SKILL.md` entry point
- `.github/agents/` — Custom agent definitions (`.agent.md` files)
- `.github/copilot-instructions.md` — This file; workspace-wide guidelines

## Conventions

### Skills

- Each skill is a self-contained folder under `.github/skills/<name>/`
- `SKILL.md` is required and must include YAML frontmatter with `name` and `description`
- The `name` field must match the folder name (lowercase, hyphens, 1-64 chars)
- Keep `SKILL.md` under 500 lines; use `references/` and `scripts/` for overflow
- Descriptions should be keyword-rich and explain both **what** the skill does and **when** to use it
- Reference bundled resources with relative paths (`./scripts/`, `./references/`)

### Agents

- Agent files go in `.github/agents/` as `<name>.agent.md`
- Frontmatter must include a `description` field
- Give each agent a single, focused role with minimal tools

### Scripts

- Python scripts in skill folders should be runnable with `python -m scripts.<name>` from the skill directory
- Scripts require Python 3.10+

## Build & Test

There is no build step. Skills and agents are plain Markdown files. The skill-creator includes Python scripts for evaluation — run them with:

```bash
cd .github/skills/skill-creator
python -m scripts.quick_validate <path-to-skill>
```
