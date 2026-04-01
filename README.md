# agents-super-skills

A curated repository of reusable AI agent skills and agent-workflow toolkits.

The collection combines:
- standalone skill packages with a top-level `SKILL.md` entrypoint;
- larger methodology/tooling folders that include rules, commands, templates, and supporting docs.

## Included Skills

| Skill | Description |
| --- | --- |
| `deep-research-prompter` | Generates a self-contained Russian master prompt for deep research workflows with explicit planning, evidence standards, and source hygiene. |
| `frontend-design-prompter` | Generates a self-contained Russian master prompt for production-ready frontend design workflows based on GPT-5.4-oriented implementation guidelines. |
| `presentation-builder-prompter` | Generates a self-contained Russian master prompt for interactive single-file HTML presentations with strong narrative structure, polished motion, and live-demo-friendly UX. |
| `agent-architect` | Designs production-grade AI-agent architecture for Claude (orchestration pattern, MCP/tooling setup, reliability, escalation, and production checklist). |
| `skill-creator` | A comprehensive skill-authoring toolkit imported from Anthropic's skills collection (evaluation scripts, references, assets, and packaging helpers). |

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
- `DDX HS` is a full workflow toolkit (not a single-skill package) and is intended to be used as a structured workspace.
- Most custom authored materials in this repository are designed for Russian-language workflows.
