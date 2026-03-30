# Agent Skill Creator

A template repository for creating and iterating on [GitHub Copilot agent skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills) and [custom agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents). It ships with two built-in skills and a custom agent that together form a complete toolkit for authoring, testing, and optimizing agent customizations.

## What's Inside

### Skills

| Skill | Description |
|-------|-------------|
| **[skill-creator](.github/skills/skill-creator/SKILL.md)** | End-to-end workflow for creating new skills, running evaluations, benchmarking with variance analysis, and optimizing skill descriptions for better triggering accuracy. |
| **[agent-customization](.github/skills/agent-customization/SKILL.md)** | Reference guide and decision framework for all VS Code agent customization primitives — instructions, prompts, hooks, agents, and skills. |

### Agents

| Agent | Description |
|-------|-------------|
| **agent-creator** | A custom agent for creating new `.agent.md` files. |

## Repository Structure

```
.github/
├── copilot-instructions.md              # Workspace-wide coding guidelines
├── agents/
│   └── agent-creator.agent.md            # Custom agent for creating new agents
└── skills/
    ├── AGENTS.md                        # Guidelines for working in the skills directory
    ├── agent-customization/             # Skill: reference for all customization primitives
    │   ├── SKILL.md
    │   └── references/
    │       ├── workspace-instructions.md
    │       ├── instructions.md
    │       ├── prompts.md
    │       ├── hooks.md
    │       ├── agents.md
    │       └── skills.md
    └── skill-creator/                   # Skill: create, test, and improve skills
        ├── SKILL.md
        ├── LICENSE.txt
        ├── agents/                      # Subagent definitions for eval workflow
        │   ├── analyzer.md
        │   ├── comparator.md
        │   └── grader.md
        ├── assets/
        │   └── eval_review.html
        ├── eval-viewer/
        │   ├── generate_review.py
        │   └── viewer.html
        ├── references/
        │   └── schemas.md
        └── scripts/                     # Python scripts for eval orchestration
            ├── aggregate_benchmark.py
            ├── generate_report.py
            ├── improve_description.py
            ├── package_skill.py
            ├── quick_validate.py
            ├── run_eval.py
            ├── run_loop.py
            └── utils.py
```

## Getting Started

### Prerequisites

- [VS Code](https://code.visualstudio.com/) with [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) installed
- Python 3.10+ (for the skill-creator evaluation scripts)

### Use This Template

1. Click **Use this template** → **Create a new repository** on GitHub.
2. Clone your new repository locally and open it in VS Code.
3. GitHub Copilot will automatically discover the skills and agents defined in `.github/`.

### Creating a Skill

The fastest way to create a new skill is to invoke the **skill-creator** skill:

1. Open Copilot Chat in VS Code.
2. Type `/skill-creator` or describe what you want — e.g., *"I want to create a skill that generates API documentation from OpenAPI specs."*
3. The skill-creator walks you through intent capture, drafting, testing, and iterating.

To create a skill manually, add a new folder under `.github/skills/<skill-name>/` with a `SKILL.md` file. See the [skills reference](.github/skills/agent-customization/references/skills.md) for the full format.

### Creating an Agent

1. Open Copilot Chat in VS Code.
2. Select the **agent-creator** agent from the agent picker, or invoke the **agent-customization** skill.
3. Describe the agent you want — e.g., *"Create an agent that reviews PRs for security issues."*
4. The agent will be created in `.github/agents/` as an `*.agent.md` file.

See the [agents reference](.github/skills/agent-customization/references/agents.md) for the full format.

### Choosing the Right Primitive

Not sure whether you need a skill, an agent, a prompt, or an instruction file? Invoke the **agent-customization** skill — it includes a decision framework that helps you pick the right primitive for your use case.

| Primitive | Best For |
|-----------|----------|
| Workspace Instructions | Always-on project-wide standards |
| File Instructions | Rules scoped to specific file types or directories |
| Prompts | Single focused tasks with parameterized inputs |
| Skills | Repeatable multi-step workflows with bundled assets |
| Custom Agents | Role-based personas with tool restrictions |
| Hooks | Deterministic enforcement at agent lifecycle points |

## Evaluating Skills

The skill-creator includes a full evaluation pipeline:

1. **Draft** test prompts that exercise your skill's capabilities.
2. **Run** the skill (and a baseline without it) on each prompt via subagents.
3. **Grade** outputs against assertions — programmatically where possible.
4. **Review** results in an interactive HTML viewer that shows side-by-side outputs and benchmark stats.
5. **Iterate** based on feedback until the skill meets your quality bar.
6. **Optimize** the skill's description for better triggering accuracy.

See the [skill-creator SKILL.md](.github/skills/skill-creator/SKILL.md) for the complete workflow.

## License

This project is licensed under the Apache License 2.0 — see [LICENSE.txt](.github/skills/skill-creator/LICENSE.txt) for details.
