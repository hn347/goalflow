# GoalFlow Architecture

GoalFlow turns Bob from a coding assistant into a governed engineering loop. It is Bob IDE-first, with Bob Shell as a sequential fallback.

```text
/goal "Build X"
        │
        ▼
GoalFlow Orchestrator Mode
        │
        ├── explore subagent: repo discovery
        ├── explore subagent: docs discovery via Context7 / Exa
        ├── explore subagent: Carbon guidance for UI
        │
        ▼
.goal/goal.md + .goal/spec.md + .goal/task_graph.json
        │
        ▼
Builder Mode
        │
        ▼
QA Mode
        │
        ├── tests
        ├── lint/typecheck/build
        ├── browser validation
        └── smoke checks
        │
        ▼
Independent Judge Subagent
        │
        ▼
.goal/judge.json
        │
        ├── complete → final summary
        ├── repeat → .goal/reflection.md → Builder Mode again
        └── halt → surface blocker
```

## Subagent boundaries

Use subagents for isolated discovery and judging where only a summary is needed. Keep main implementation visible in Builder mode so diffs, approvals, and iteration stay transparent.
