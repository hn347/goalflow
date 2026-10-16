# Grill-Me Protocol for /goal

Every `/goal` invocation **must** begin with a grill-me session before any contract files are
written or Builder mode is entered. Activate with `use_skill: grill-me`.

## Purpose

Transform a potentially vague goal into an unambiguous contract. A builder agent receiving the
output should be able to implement without asking a single clarifying question.

## Session rules

- **One question at a time** — never a list. Pick the single most unresolved question first.
- **Attach a recommendation** — every question carries a best-guess answer in brackets. The user confirms, corrects, or redirects.
- **Auto-answer from codebase** — before asking, check if the answer is in the repo (README, package.json, existing tests, config files). State what you found; do not ask the user for things you can discover.
- **Challenge vague language** — propose precise canonical terms for overloaded words.
- **Stress-test assumptions** — after each answer, probe edge cases.

## Mandatory coverage areas

Before closing the session, every area below must have a definitive answer:

| Area | Key question |
| --- | --- |
| Acceptance criteria | "What does done look like? What would you check in a review?" |
| Evaluation metrics | "Are there specific numbers or thresholds this must hit?" |
| QA watch list | "Anything specific to look for during QA — known tricky areas, third-party dependencies, integration points?" |
| Scope boundary | "What is explicitly out of scope?" |
| Constraints | "What framework, language, runtime, API, or auth constraints apply?" |
| Risks | "What's the riskiest part? What could go wrong?" |
| Test seams | "Which behaviors are most critical to test? What are the public interfaces?" |

## Closing

Read back a summary of all resolved answers and ask:

> "Here's what I've captured — [summary]. Does this match your intent, or should we adjust anything before I start building?"

Do not proceed until the user confirms.

## Output

Captured answers flow directly into `.goal/goal.md`. Pay special attention to:

- Acceptance criteria — must be independently verifiable, not "it works"
- QA watch list → "Required verification" section of `goal.md`
- Evaluation metrics → "Acceptance criteria" section (with numbers)
- Test seams → "Constraints" section (for the TDD phase)
