# Bob GoalFlow Integration

This `.bob` directory installs GoalFlow as a Bob-native workflow for the IBM hackathon.

## What it adds

- `/goal` slash command through `.bob/commands/goal.md`.
- IDE-first custom modes in `.bob/custom_modes.yaml`.
- Mode-specific rules loaded from `.bob/rules-{mode}/` folders.
- Templates for `.goal/` artifacts.
- Example MCP configuration without real credentials.

## MCP setup

Required MCPs for the full demo:

1. Carbon MCP via Bob Marketplace for IBM Carbon design guidance.
2. Context7 MCP for current framework and library docs.
3. Exa MCP for fallback web research.

Copy `.bob/mcp.example.json` only as a reference. Do not commit real `.bob/mcp.json` files with credentials. Prefer global Bob MCP settings or secure environment configuration for secrets.

## IDE-first, Shell fallback

GoalFlow is designed for Bob IDE because the wow path uses IDE subagents. Bob Shell supports custom modes, slash commands, browser automation, command execution, and MCP, but its documented mode tool groups differ from the IDE. Use `custom_modes.shell.yaml` as the Shell fallback: it replaces IDE `execute` with Shell `command`, keeps `browser` and `mcp`, and removes IDE-only `subagent`, `subtask`, `mode`, `todo`, `workflow`, and `skill` groups. When running in Bob Shell without subagents, run the same phases sequentially with custom modes and record the fallback in `.goal/history.md`.
