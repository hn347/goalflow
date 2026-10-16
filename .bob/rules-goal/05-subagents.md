# Subagent Usage

This project runs in **Bob IDE** where `spawn_subagent` is available. Subagents run in isolated
contexts and return only a summary — use them for work that must not pollute the main conversation.

## Mandatory subagent uses (never skip)

### 1. Repo discovery — `explore` type
Spawn at the start of every discovery phase, before writing `spec.md`:

```
spawn_subagent(
  type: "explore",
  description: "Find relevant files, architecture patterns, test commands, and risk areas for [GOAL]. Return only paths, summaries, and uncertainties."
)
```

### 2. Judge pass — `general` type
The judge **must** run in a subagent to guarantee an independent context. A judge running in the
same conversation as the builder cannot be independent — it has seen all the implementation choices
and is primed to confirm them.

```
spawn_subagent(
  type: "general",
  fork_context: false,          ← do NOT fork — judge needs a clean context
  description: "Read .goal/goal.md, .goal/spec.md, and .goal/verification.md.
    Score these five axes 0–1.0: goalAlignment, acceptanceCriteriaCoverage,
    implementationCompleteness, verificationStrength, riskAndRegressionControl.
    List strengths, gaps, and requiredNextActions. Write .goal/judge.json.
    Decide: complete, repeat, or halt."
)
```

`fork_context: false` is essential. If the judge inherits the parent conversation, it is no longer
independent. Pass only the artifact file paths, never the implementation history.

## Optional subagent uses (use when beneficial)

- **Docs discovery** — `explore` subagent to query Context7/Exa in isolation when the main context is large
- **Carbon UI guidance** — `explore` subagent when the goal includes UI work and the Carbon query would add significant noise to the main context

## Never use subagents for

- Main implementation (Builder mode must be visible with diffs)
- Destructive or high-risk changes
- Work that requires user review of intermediate steps
- Anything where a failure must be recoverable by the orchestrator mid-stream

## Fallback — Bob Shell or subagents unavailable

If `spawn_subagent` is not available (Bob Shell environment, or mode lacks `subagent` group):

1. Record `subagents_unavailable: true` in `.goal/discovery.md`
2. Run repo discovery directly in the current mode
3. Run the judge pass as a sequential `judge` mode switch (document the loss of independence in `judge.json` gaps)
4. Record the fallback path in `.goal/history.md`

Do not claim subagents were used when they were not.
