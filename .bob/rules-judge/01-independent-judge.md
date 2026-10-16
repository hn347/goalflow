# Independent Judge

The judge evaluates whether the implementation satisfies the goal contract. Independence means the
judge reads **artifacts** (objective evidence written during the build), not the conversation history
that produced them.

## What the judge reads — the evidence package

The judge subagent receives only these files. It must not ask for anything else:

| Artifact | Purpose |
| --- | --- |
| `.goal/goal.md` | The contract — acceptance criteria, evaluation metrics, QA watch list, constraints |
| `.goal/spec.md` | What was planned — file list, implementation plan, verification plan |
| `.goal/verification.md` | What QA actually ran — every check, its result, and skipped checks with reasons |
| `.goal/task_graph.json` | Which tasks completed, which are still pending |
| `git diff HEAD~1` or relevant changed files | What was actually built — the ground truth of the implementation |

Reading the diff is mandatory. The judge cannot score `implementationCompleteness` without seeing
what code was actually changed. Run:

```bash
git diff HEAD~1 --stat          # what files changed
git diff HEAD~1 -- <key files>  # the actual changes
```

If git is not available, read the key source files listed in `spec.md` directly.

## What the judge is evaluating

For each acceptance criterion in `goal.md`, the judge answers three questions:

1. **Was it implemented?** — evidence from the diff or changed files
2. **Was it tested?** — evidence from `verification.md` (QA checks, test output)
3. **Did it pass?** — specific check results in `verification.md`

A criterion is only `complete` when all three are true. If any one is false, it is a gap.

## What the judge does NOT do

- Does not implement fixes
- Does not expand scope
- Does not re-run QA checks
- Does not speculate about intent — only evidence in the artifact files counts
- Does not mark a criterion complete because it seems likely to work

## Output

Write `.goal/judge.json` with:

- `decision` — `complete`, `repeat`, or `halt` (see rubric)
- `scores` — five 0–1.0 axes (see rubric)
- `strengths` — specific things that were done well, with artifact evidence
- `gaps` — each gap tied to a specific acceptance criterion and the missing evidence
- `requiredNextActions` — for each gap: the exact phase to return to (`builder` or `qa`), the specific task, and the file to fix
- `failedCriteria` — list of criterion IDs from `goal.md` that did not pass

The `requiredNextActions` array is the loop-back mechanism. Each entry must be actionable by
Builder or QA without asking a clarifying question — if it isn't specific enough, it's not done.
