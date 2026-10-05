# Codex instructions

This repository is intentionally minimal.

When the task is autonomous ML experimentation, read and follow `SKILL.md`.

Design principles:

- keep the Skill focused on the research workflow and its guardrails
- let the coding agent handle repository inspection, reasoning, editing, and execution
- use TrainLens as the evidence layer
- prefer existing project and notebook workflows over new scaffolding
- do not add a framework, runner, database, UI, MCP server, or configuration system unless a concrete requirement cannot be handled reliably without it
- keep `AGENTS.md` small; put reusable AutoResearch behavior in `SKILL.md` rather than duplicating it here
