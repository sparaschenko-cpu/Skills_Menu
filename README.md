# Skills Menu

A curated menu of reusable AI-agent skills and workflow toolkits.

The collection combines:
- standalone skill packages with a top-level `SKILL.md` entrypoint;
- larger methodology/tooling folders that include rules, commands, templates, and supporting docs.

## Included Skills

| Skill | Description |
| --- | --- |
| `CEO Council Skill` | Launches multiple independent C-level sub-agents to analyze the same project context and synthesize consensus, disagreements, and strategic decisions. |
| `deep-research-prompter` | Generates a self-contained Russian master prompt for deep research workflows with explicit planning, evidence standards, and source hygiene. |
| `frontend-design-prompter` | Generates a self-contained Russian master prompt for production-ready frontend design workflows based on GPT-5.4-oriented implementation guidelines. |
| `presentation-builder-prompter` | Generates a self-contained Russian master prompt for interactive single-file HTML presentations with strong narrative structure, polished motion, and live-demo-friendly UX. |
| `agent-architect` | Designs production-grade AI-agent architecture for Claude (orchestration pattern, MCP/tooling setup, reliability, escalation, and production checklist). |
| `skill-creator` | A comprehensive skill-authoring toolkit imported from Anthropic's skills collection (evaluation scripts, references, assets, and packaging helpers). |

## Quick Start

- Open a standalone skill folder and use its `SKILL.md` as the entrypoint.
- For `CEO Council Skill/`, start with `SKILL.md` for execution logic and use `README.md` or `README.ru.md` for human-facing overview.
- For full differential-diagnostic workflows, use `DDX HS/` as a complete workspace toolkit.
- Keep new additions at the repository root so they are discoverable immediately.

## Spotlight: CEO Council Skill

`CEO Council Skill/` is a standalone multi-agent strategy package for project analysis through independent expert lenses.

- `SKILL.md` defines the operating workflow for assembling a council, launching parallel expert sub-agents, and synthesizing results.
- `README.md` provides the English overview and usage examples.
- `README.ru.md` provides the Russian overview and usage examples.

## Included Workflow Toolkits

| Toolkit | What it contains | Primary use |
| --- | --- | --- |
| `DDX HS` | Multi-file DDx-6D/8D analysis workspace: `.cursor/skills`, `.cursor/rules`, `.cursor/commands`, `Artifacts/DDx` templates, and manual docs. | Differential-diagnostic text analysis workflow with structured case artifacts and phased reasoning. |

## Repository Structure

```text
DDX HS/
  .cursor/
  Artifacts/
  Manual/
CEO Council Skill/
  SKILL.md
  README.md
  README.ru.md
agent-architect/
  SKILL.md
  references/
deep-research-prompter/
  SKILL.md
frontend-design-prompter/
  SKILL.md
presentation-builder-prompter/
  SKILL.md
skill-creator/
  SKILL.md
  scripts/
  references/
```

## Notes

- Standalone skill folders use a `SKILL.md` entrypoint.
- Some standalone skills also include localized README files for easier human navigation.
- `DDX HS` is a full workflow toolkit (not a single-skill package) and is intended to be used as a structured workspace.
- Most custom authored materials in this repository are designed for Russian-language workflows.
- The repository is organized as a flat top-level menu (no extra wrapper folder for skills).
