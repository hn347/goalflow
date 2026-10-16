---
name: goal
description: '# /goal'
metadata:
  user-invocable: true
  disable-model-invocation: true
---

# /goal

Turn a user-provided engineering objective into a governed GoalFlow loop.

## Usage

```text
/goal "Build or fix the requested capability"
```

## Behavior

1. Switch to **GoalFlow Orchestrator** mode.
2. **Grill-me session** — activate `use_skill: grill-me` and conduct a structured pre-implementation
   interview. One question at a time, with a recommended answer attached to each. Do not proceed
   until the user confirms the contract. The session covers:
   - acceptance criteria (specific and independently testable)
   - evaluation metrics (quantified where possible)
   - QA watch list — anything specific to look for during verification
   - scope boundaries (explicit in-scope and out-of-scope)
   - constraints (framework, language, APIs, auth)
   - risks and unknowns
   - test seams (which public interfaces to test at, for the TDD phase)
3. Write `.goal/goal.md`, `.goal/spec.md`, and `.goal/task_graph.json` from the grilling output.
4. Run focused discovery before implementation:
   - repo discovery with an `explore` subagent when Bob IDE subagents are available;
   - documentation discovery with Context7 first and Exa only as fallback;
   - IBM Carbon guidance when the goal includes UI work.
5. Switch to **Builder** mode for implementation.
   - Activate `use_skill: tdd` for any goal that involves logic, data transformation, API calls,
     or other testable behavior. Work in vertical slices: one failing test → minimal
     implementation → repeat, one slice per acceptance criterion.
   - For UI goals, activate `use_skill: agent-browser` during the QA step.
6. Switch to **QA** mode for verification evidence.
   - For UI goals, activate `use_skill: agent-browser` and run the dogfood protocol:
     navigate, console errors, network requests, `naturalWidth === 0` count, screenshot.
   - Record all steps in `.goal/verification.md`.
7. Invoke an independent **Judge** pass and write `.goal/judge.json`.
8. Decide: `complete`, `repeat`, or `halt`.

## Fallback

If Bob Shell does not support IDE subagents in the active environment, run the same phases
sequentially through modes and record the fallback in `.goal/history.md`.
