# Stop Rules

## Halt immediately and surface to user when

- Acceptance criteria are ambiguous (grill-me did not resolve them)
- Credentials or private resources are required and not available
- A database migration, deletion, deployment, or major architecture change is needed
- Tests fail for reasons unrelated to the goal
- `judge.decision === "halt"` for any reason
- The same `failedCriteria` appear in `judge.json` after 3 consecutive repeat cycles — the loop
  is not converging; a human decision is required

## Repeat routing (not a halt)

When `judge.decision === "repeat"`, do NOT halt. Read `requiredNextActions` and route:

- `"phase": "builder"` → re-enter Builder mode with the exact action as the task
- `"phase": "qa"` → re-enter QA mode to re-run the specific failing check

After completing all required actions, spawn a new judge subagent (clean context) to re-evaluate.

## What the orchestrator reports on halt

When halting, report:
1. Which acceptance criteria failed (from `judge.json` → `failedCriteria`)
2. The specific `requiredNextActions` that could not be resolved automatically
3. What user decision or resource is needed to unblock
