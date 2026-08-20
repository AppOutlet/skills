# AGENTS.md

## Repository overview

This is a **skills repository** for the AppOutlet organization. Each subdirectory under `skills/` is a self-contained agent skill (usable with OpenCode and similar coding agents) — not application source code. There is no build, test suite, CI, or runtime environment.

## Creating and updating skills

All skill creation and modification must be done using the **skill-creator** skill (it is available in this environment). Load it with the skill tool and follow its workflow. If the skill-creator skill is not available in your environment, please notify the user before proceeding.

**Important:** When asked to update a skill, always modify the skill files inside this repository under `skills/`. Do not edit installed skill copies located outside this repository (e.g. under `~/.claude/skills/` or `~/.agents/skills/`); those are derived from this repository.
