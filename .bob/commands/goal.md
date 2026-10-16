# /goal

Turn a user-provided engineering objective into a governed GoalFlow loop.

## Usage

```text
/goal "Build or fix the requested capability"
```

## Behavior

1. Switch to **GoalFlow Orchestrator** mode.
2. **Grill-me session** — activate `use_skill: grill-me` and conduct a structured pre-implementation
   interview. One question at a time, with a recommended answer attached. Do not proceed until the
   user confirms the contract. Covers: acceptance criteria, evaluation metrics, QA watch list, scope
   boundaries, constraints, risks, and test seams.
3. Write `.goal/goal.md`, `.goal/spec.md`, and `.goal/task_graph.json` from the grilling output.
4. Run focused discovery:
   - repo discovery with an `explore` subagent when available;
   - documentation discovery with Context7 first, Exa as fallback;
   - IBM Carbon guidance when the goal includes UI work.
5. Switch to **Builder** mode for implementation.
   - Activate `use_skill: tdd` for goals with testable behavior. Vertical slices: failing test →
     minimal implementation → repeat.
   - For UI goals, use `use_skill: agent-browser` in the QA step.
6. Switch to **QA** mode for verification evidence.
   - For UI goals, activate `use_skill: agent-browser` (dogfood protocol).
   - Record all steps in `.goal/verification.md`.
7. Invoke an independent **Judge** pass → `.goal/judge.json`.
8. Decide: `complete`, `repeat`, or `halt`.

## Fallback

If Bob Shell does not support IDE subagents, run phases sequentially through modes and record
the fallback in `.goal/history.md`.
