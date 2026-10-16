# Bob Documentation Compatibility Research

This research summarizes what the official Bob documentation confirms for GoalFlow and what must fall back in Bob Shell.

## Sources checked

- Bob IDE custom modes documentation.
- Bob IDE subagents documentation.
- Bob IDE tools documentation.
- Bob IDE MCP documentation.
- Bob Shell welcome documentation.
- Bob Shell custom modes documentation.
- Bob Shell slash commands documentation.
- Bob Shell MCP documentation.
- Bob Shell trusted folders documentation.

## What works as designed in Bob IDE

GoalFlow's IDE-first configuration is compatible with the Bob IDE custom modes model:

- Project modes live in `.bob/custom_modes.yaml`.
- The top-level key is `customModes`.
- Supported IDE groups include `read`, `edit`, `execute`, `mcp`, `skill`, `workflow`, `todo`, `subtask`, `subagent`, and `mode`.
- `allowedSubagents` is a documented property for restricting subagent presets.
- Mode-specific rules belong in `.bob/rules-{mode-slug}/` and are loaded alphabetically.

Subagents are also an IDE-documented fit for GoalFlow's discovery and judging story:

- A subagent is independent from the main conversation, runs in its own isolated context, and returns only a summary.
- Bob documents two subagent types: `explore` for read-only codebase exploration and `general` for full-tool self-contained work.
- Bob uses subagents sparingly, only for focused self-contained tasks where a summary is enough and the work would otherwise pollute the main context.
- If a mode does not permit a subagent, Bob completes the work directly instead of spawning one.

## What works in both Bob IDE and Bob Shell

Custom slash commands are compatible across Bob IDE and Bob Shell:

- Custom commands live in `.bob/commands/` or `~/.bob/commands/`.
- Custom commands must be Markdown files.
- The same slash command syntax and behavior applies in both environments.
- Project commands override global custom commands with the same name.

This means `.bob/commands/goal.md` is the correct shared entry point for `/goal`.

## Bob Shell differences that affect GoalFlow

Bob Shell supports custom modes, custom commands, browser automation, command execution, and MCP, but the documented Shell tool groups are different from the IDE groups:

- Shell supports `read`, `edit`, `browser`, `command`, and `mcp`.
- Shell uses `command` for terminal execution, while the IDE uses `execute`.
- The Shell custom modes page does not document `subagent`, `subtask`, `mode`, `todo`, `workflow`, or `skill` as Shell tool groups.
- Shell custom modes can still live at `.bob/custom_modes.yaml` for a project and use the same `customModes` top-level structure.

Bob Shell MCP configuration is also documented differently from Bob IDE MCP configuration:

- Bob IDE global MCP config is documented as `~/.bob/mcp.json`; project MCP config is `.bob/mcp.json`.
- Bob Shell global MCP config is documented as `<USER_HOME>/.bob/mcp_settings.json`; project MCP config is `.bob/mcp.json`.
- In both cases, project-level MCP configuration takes precedence on server-name conflicts.

## Required Shell fallback

When running GoalFlow in Bob Shell, do not depend on IDE subagents. Use a sequential fallback loop instead:

1. `/goal` starts the same workflow command.
2. GoalFlow Orchestrator writes `.goal/goal.md`, `.goal/spec.md`, and `.goal/task_graph.json`.
3. Repo discovery runs directly in the current Shell mode and records findings in `.goal/discovery.md`.
4. Documentation discovery uses MCP if configured; otherwise it records the missing MCP capability as skipped or blocked.
5. UI guidance uses browser/MCP where available; otherwise it records a skipped Carbon guidance check.
6. Builder work runs visibly in Shell using the `builder` mode.
7. QA runs tests, builds, smoke checks, and browser validation using the `qa` mode.
8. Judging runs as a fresh sequential `judge` mode pass that reads `.goal` artifacts, diffs, and verification evidence, then writes `.goal/judge.json`.
9. The orchestrator decides `complete`, `repeat`, or `halt` from the judge result.

For Shell compatibility, use `.bob/custom_modes.shell.yaml` as the fallback mode definition, or adapt `.bob/custom_modes.yaml` by replacing IDE-only groups with Shell-supported groups.

## Compatibility verdict

GoalFlow will work best in Bob IDE exactly as positioned: custom slash command, custom modes, mode-specific rules, MCP, and IDE subagents for isolated discovery and judging. Bob Shell can run the same product concept, but as a sequential workflow without documented IDE subagent spawning. The practical Shell fallback is artifact-driven mode switching with `command`, `browser`, and `mcp` groups instead of IDE `execute`, `subagent`, `subtask`, `mode`, `todo`, `workflow`, and `skill` groups.
