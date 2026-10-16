---
name: grill-me
description: Relentless pre-implementation interviewing skill that stress-tests a goal or plan through systematic one-question-at-a-time questioning before any code is written. Use at the start of /goal to clarify ambiguous requirements, nail down acceptance criteria, identify risks, and agree evaluation metrics. Automatically explores the codebase to answer questions where context is available, reducing redundant back-and-forth. Designed for design reviews, architecture validation, and pre-implementation planning where thorough vetting prevents downstream issues.
---

# Grill Me

Conduct a focused pre-implementation interview to transform a vague goal into a concrete, unambiguous contract. The output is a fully populated `.goal/goal.md` that a builder agent can act on without asking a single clarifying question.

## When to run

Run at the **start of every `/goal` invocation**, before writing `spec.md` or any implementation.  
Stop grilling when all of the following are true:

- Every acceptance criterion is specific and testable (a human can read it and know unambiguously whether it passed or failed)
- Evaluation metrics are agreed and quantified
- Anything specific to look for during QA is captured
- Scope boundaries are explicit — both in-scope and out-of-scope are named
- Constraints are listed (framework, language, APIs, auth, environment)
- No open "it depends" answers remain

## The grilling discipline

**One question at a time.** Never ask a list. Pick the single most important unresolved question and ask it with a recommended answer attached. Wait for the user's response before proceeding.

**Always attach a recommendation.** Every question includes your best-guess answer in brackets so the user can confirm, correct, or redirect rather than invent from scratch. Example:

> "Should the dashboard refresh automatically, or only on manual reload?  
> [Recommended: manual reload — simpler to implement and sufficient for the use case]"

**Auto-answer from the codebase.** Before asking a question, check whether the answer is already in the codebase (existing config, README, package.json, existing tests). If it is, state what you found and move on. Do not ask the user for information you can discover yourself.

**Branch exhaustively.** When a decision branches (e.g. "should we use a database?"), follow each branch to its leaf before moving on. A decision tree is only resolved when every branch has a definitive answer.

**Challenge vague language.** When the user uses an overloaded or vague term, propose a precise canonical version: "You said 'fast' — do you mean < 200 ms response time, or something else?"

**Stress-test assumptions.** After the user answers, probe whether the answer holds under edge cases and load. "You said images will always load from the CDN — what should happen if the CDN is unreachable?"

## Mandatory questions for every /goal session

Work through all of these before closing the session. Skip any that are clearly already answered in the user's original request.

### 1. Acceptance criteria
"What does done look like in plain language? Walk me through what you'd check if you were reviewing the finished work."

### 2. Evaluation metrics
"How will we measure success — are there specific numbers, thresholds, or KPIs this needs to hit?"  
*Examples: page loads in < 2s, test coverage > 80%, error rate = 0, all images render (naturalWidth > 0)*

### 3. QA specifics — anything to watch for
"Is there anything specific you want me to watch for during QA that might not be obvious from the requirements? Known tricky areas, third-party dependencies, integration points?"

### 4. Scope boundary
"What is explicitly out of scope — things a reasonable person might assume are included but aren't?"

### 5. Constraints
"What constraints apply — framework, language, runtime, APIs, auth, existing architecture?"

### 6. Risks and unknowns
"What's the riskiest part of this? What could go wrong that we should plan for now?"

### 7. Test seams
"Which behaviors are most critical to verify with tests? What are the public interfaces we should test at?"  
*(This feeds directly into the TDD phase — see the `tdd` skill.)*

## Output: populated goal contract

When grilling is complete, write `.goal/goal.md` using the answers gathered. Every field must be filled — no placeholders. The contract is the source of truth for the entire GoalFlow loop.

Pay special attention to:

- **Acceptance criteria** — must be independently verifiable, not "it works"
- **QA watch list** — captured under "Required verification" with the specific checks to run
- **Evaluation metrics** — numerical where possible; qualitative only when no number applies
- **Test seams** — captured under constraints so the TDD phase knows where to start

## Closing the session

End with a summary of what was resolved, read it back to the user, and ask for a single confirmation:

> "Here's what I've captured — [summary]. Does this match your intent, or should we adjust anything before I start building?"

Do not proceed to Builder mode until the user confirms.
