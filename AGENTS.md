# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, Cursor, Copilot, etc.) when working with code in this repository.

## Repository Overview

A collection of skills for AI Agents. Skills are packaged instructions and scripts following the [Agent Skills](https://agentskills.io/) format that extend agent capabilities

## Directory Structure

```
skills/
  {skill-name}/           # kebab-case directory name
    SKILL.md              # Required: skill definition
    scripts/              # Optional: executable scripts
      {script-name}.sh    # Bash scripts (preferred)
    references/           # Optional: additional documentation that agents can read when needed
    assets/               # Optional: static resources, such as templates, images or data files
```

## Creating/Modifying Skills

Please refer to your `skill-creator` skill for detailed instructions on creating or modifying skills. Below are general guidelines to ensure consistency and efficiency when working with skills in this repository.

- After creating or updating a skill, run the `package_skill` script to validate the skill (as it performs a complete validation of the skill). For details and the exact command, consult the `skill-creator` skill (see its scripts, including `package_skill.py`, and its usage guidance).
- If you encounter Python package issues, propose using a repo-local `.venv`, explain why it is preferred, how it affects the user's system, and ask for approval before proceeding.

### Naming Conventions

- **Skill directory**: `kebab-case` (e.g., `log-monitor`)
- **SKILL.md**: Always uppercase, always this exact filename
- **Scripts**: `kebab-case.sh` (e.g., `deploy.sh`, `fetch-logs.sh`)
- **Other files**: `kebab-case` unless they have standard format
