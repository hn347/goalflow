# Loop Engineering

GoalFlow proceeds through repeatable phases:

1. **grill-me** — `use_skill: grill-me`; structured pre-implementation interview; user must confirm before proceeding;
2. **contract** — write `.goal/goal.md`, `.goal/spec.md`, `.goal/task_graph.json` from grilling output;
3. **discovery** — spawn `explore` subagent (`fork_context: false`) for repo; probe MCP servers; query Context7 → Exa → `web_search` for docs; Carbon MCP for UI guidance;
4. **Builder mode** — `use_skill: tdd` for testable behavior; vertical slices: red → green → next slice;
5. **QA mode** — `use_skill: agent-browser`; auto-bootstrap CLI + Chrome; dogfood protocol: console → network → `naturalWidth` → screenshot; write `verification.md`;
6. **judge** — spawn `general` subagent with `fork_context: false`; subagent reads `.goal/` artifacts + git diff; writes `judge.json` with `requiredNextActions`;
7. **orchestrator reads `judge.json`** and routes based on `decision`:
   - `complete` → done; surface summary to user
   - `repeat` → read `requiredNextActions`; route each action back to the correct phase:
     - `"phase": "builder"` → re-enter **Builder mode** with the specific action as the task
     - `"phase": "qa"` → re-enter **QA mode** to re-run the specific failing check
   - `halt` → stop; surface blocker to user with the gaps list

## Subagent summary

| Phase | Subagent type | fork_context | Mandatory? |
| --- | --- | --- | --- |
| Discovery | `explore` | `false` | Yes |
| Judge | `general` | `false` | Yes — independence requires clean context |

## Loop-back routing

When the judge returns `repeat`, the orchestrator must NOT re-run the full loop from scratch.
Read `requiredNextActions` and route only the affected phases:

```
for each action in requiredNextActions:
  if action.phase === "builder":
    → switch to Builder mode
    → implement action.action to fix action.criterion
  if action.phase === "qa":
    → switch to QA mode
    → re-run the specific check in action.action
re-run judge subagent after all actions complete
```

This is targeted repair, not a full re-run. Only the phases named in `requiredNextActions` are
repeated. The grill-me, contract, and discovery phases do not repeat unless the judge sets
`decision: halt` with a reason that requires re-scoping.

## Repeat / halt rules

- **Repeat** only when the judge identifies addressable gaps with clear `requiredNextActions`.
- **Halt** on: destructive changes, unclear requirements, missing credentials, failures requiring user decision, `judge.decision === "halt"`, or same gaps appear after 3 repeat cycles.
- **Never** repeat more than 3 cycles without surfacing a user decision.
- **Track repeat count** in `.goal/task_graph.json` under `"repeatCount"`.
