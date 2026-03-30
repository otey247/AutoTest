# Skills Directory Guidelines

This directory contains all agent skills for the repository. Each subdirectory is a self-contained skill with its own `SKILL.md`, scripts, references, and assets.

## Available Skills

| Skill | Purpose |
|-------|---------|
| [agent-customization](./agent-customization/SKILL.md) | Reference guide and decision framework for VS Code agent customization primitives (instructions, prompts, hooks, agents, skills) |
| [skill-creator](./skill-creator/SKILL.md) | End-to-end workflow for creating, evaluating, benchmarking, and optimizing agent skills |

## Adding a New Skill

1. Create a new folder: `.github/skills/<skill-name>/`
2. Add a `SKILL.md` with required YAML frontmatter (`name` and `description`):
   ```yaml
   ---
   name: my-skill
   description: 'What this skill does and when to use it.'
   ---
   ```
3. The folder name must match the `name` field exactly
4. Organize supporting files into `scripts/`, `references/`, and `assets/` subdirectories as needed
5. Keep `SKILL.md` under 500 lines — use reference files for detailed documentation

## Conventions

- **Descriptions are critical** — they are the primary mechanism for skill discovery and triggering. Include both what the skill does and when to use it, with specific trigger phrases.
- **Progressive loading** — only `name` and `description` are always in context. The `SKILL.md` body loads when the skill triggers, and referenced files load only when needed. Design your skill with this in mind.
- **Relative paths** — always use `./` when referencing bundled resources from `SKILL.md`.
- **Self-contained** — each skill folder should include everything needed to complete the workflow without relying on files outside its directory (except standard tools and MCP servers).
