# Scoring Rubric

Score each axis 0.0–1.0 based strictly on artifact evidence. No speculation.

## Axes

### goalAlignment (0.0–1.0)
Does the implementation address the original request in `.goal/goal.md`?
- 1.0 — every in-scope item is present; nothing out-of-scope was added
- 0.7 — most items present; minor out-of-scope additions or small omissions
- 0.4 — significant omissions or the implementation targets the wrong problem
- 0.0 — does not address the goal at all

### acceptanceCriteriaCoverage (0.0–1.0)
What fraction of the acceptance criteria in `goal.md` are provably satisfied by evidence in `verification.md`?
- 1.0 — every criterion has a passing check in `verification.md`
- Deduct proportionally for each unmet or unverified criterion
- A criterion with no corresponding check in `verification.md` scores 0 for that criterion

### implementationCompleteness (0.0–1.0)
Does the diff / changed file set match the implementation plan in `spec.md`?
- 1.0 — all planned files created/modified; no planned tasks still `pending` in `task_graph.json`
- Deduct for each planned file absent from the diff
- Deduct for each task in `task_graph.json` with status `pending`

### verificationStrength (0.0–1.0)
How thorough and trustworthy is the QA evidence in `verification.md`?
- 1.0 — all required checks ran with direct evidence (browser screenshot, command output, naturalWidth eval); no check marked "inferred"
- Deduct 0.15 for each check that was skipped with only `browser_tools_unavailable` as justification
- Deduct 0.20 for any check marked "inferred" rather than directly observed
- Deduct 0.10 for missing console/network checks on a UI goal
- 0.0 — no verification evidence at all

### riskAndRegressionControl (0.0–1.0)
Were known risks (from `goal.md` and `spec.md`) addressed?
- 1.0 — all risks in the spec have mitigations that are visible in the diff or verification
- Deduct for each unmitigated risk
- Deduct if third-party service reachability was not verified (curl/network check)

## Decision thresholds

| Condition | Decision |
| --- | --- |
| All five axes ≥ 0.9 AND `failedCriteria` is empty | `complete` |
| Any axis < 0.9 OR any acceptance criterion unmet, AND gaps are addressable | `repeat` |
| Any of the stop rules are triggered (see `07-stop-rules.md`) | `halt` |
| Repeated 3+ times with the same gaps | `halt` — surface to user |

**Do not return `complete` if `failedCriteria` is non-empty**, even if scores look high.
**Do not return `complete` if `verificationStrength` < 0.85** — unverified work is unfinished work.

## requiredNextActions format

Each entry in `requiredNextActions` must follow this structure:

```json
{
  "phase": "builder",
  "criterion": "<exact criterion text from goal.md>",
  "action": "<specific file or command to fix>",
  "reason": "<what evidence was missing or wrong>"
}
```

`phase` must be either `"builder"` (implementation gap) or `"qa"` (verification gap).
Vague actions like "fix the images" are not acceptable — name the file, the check, and the failure.
