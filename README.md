# GoalFlow

**A governed engineering workflow for IBM Bob: turn a feature request into a contract, an implementation, and reviewable verification evidence.**

GoalFlow adds a `/goal` command that coordinates requirements discovery, implementation, QA, and an independent judge. It addresses a common problem in agent-assisted development: code can look finished before its acceptance criteria have actually been checked.

Built for the IBM Bob hackathon. Original team attribution: **Rsubarsky / ibm-hack**.

## At a glance

- **What it is:** a portable IBM Bob configuration and workflow pack, built from custom modes, commands, rules, skills, and artifact templates.
- **Core design:** separate the builder, verifier, and judge; record the evidence each stage needs; route failed criteria back to the appropriate stage.
- **Stack:** IBM Bob, Markdown, YAML, JSON, MCP integrations, and browser QA through `agent-browser`.
- **Start exploring:** [architecture](goalflow/architecture.md), [workflow rules](.bob/rules-goal/04-loop-engineering.md), [judge rubric](.bob/rules-judge/02-scoring-rubric.md), and [sample artifacts](goalflow/examples/).

## How it works

```text
/goal "Build the feature"
          |
          v
Requirements interview -> Goal contract -> Repository and docs discovery
                                                    |
                                                    v
                                                 Builder
                                                    |
                                                    v
                                                   QA
                                                    |
                                                    v
                                             Independent judge
                                              /     |      \
                                        complete  repeat   halt
                                                    |
                                          Targeted Builder / QA repair
```

1. **Clarify the goal.** A structured interview establishes scope, acceptance criteria, risks, and test seams.
2. **Record the contract.** The workflow writes a goal, specification, and task graph under `.goal/`.
3. **Discover the context.** Focused exploration identifies relevant code and verification commands. Research uses available MCP services with documented fallback paths.
4. **Build against the contract.** Builder mode uses small implementation steps and test-driven development for testable behavior.
5. **Collect evidence.** QA records checks, failures, and skipped steps. UI checks include console errors, failed requests, image loading, and screenshots.
6. **Judge and route.** A separate-context judge reviews the artifacts and relevant changes. It returns `complete`, `repeat`, or `halt`; repeat actions target the specific builder or QA gap instead of restarting the whole workflow.

The rubric evaluates goal alignment, acceptance coverage, implementation completeness, verification strength, and regression risk. Completion requires all five scores to meet the threshold and no failed criteria. Repeated unresolved gaps stop for a user decision.

## Quick start

**Prerequisites:** IBM Bob with support for the custom modes and tools used by this pack. For UI goals, the browser workflow also needs Node.js/npm and Chrome/Chromium; its bootstrap instructions can install `agent-browser` and a browser when missing.

1. Clone this repository:

   ```bash
   git clone https://github.com/hn347/goalflow.git
   ```

2. Copy or merge its `.bob/` directory into the application repository you want Bob to work on. Review conflicts if that repository already has Bob configuration.
3. Open the target application in Bob and confirm that the GoalFlow modes and `/goal` command are available.
4. If using MCP research, configure the services available to your Bob installation. [`.bob/mcp.example.json`](.bob/mcp.example.json) contains placeholders for Carbon, Context7, and Exa; keep real credentials out of version control.
5. Run:

   ```text
   /goal "Build an IBM Carbon-styled status dashboard"
   ```

6. Answer the requirements questions and confirm the contract. Follow the resulting `.goal/` artifacts through implementation, QA, and review.

For Bob Shell, see the [integration notes](.bob/README.md) and [alternate modes file](.bob/custom_modes.shell.yaml). Sequential mode switching is the fallback when isolated subagents are unavailable; that fallback does not provide the same judge context separation.

## Repository guide

| Location | Purpose |
| --- | --- |
| [`.bob/commands/goal.md`](.bob/commands/goal.md) | Workflow entry point |
| [`.bob/custom_modes.yaml`](.bob/custom_modes.yaml) | Orchestrator, Builder, QA, and Judge modes |
| `.bob/rules-{goal,builder,qa,judge}/` | Phase responsibilities, checks, scoring, and stop conditions |
| [`.bob/skills/`](.bob/skills/) | Goal workflow, requirements interview, TDD, and browser QA |
| [`.bob/templates/`](.bob/templates/) | Structured contracts, task graphs, verification, and judge results |
| [`goalflow/architecture.md`](goalflow/architecture.md) | Architecture and subagent boundaries |
| [`goalflow/demo-script.md`](goalflow/demo-script.md) | Suggested demonstration sequence |
| [`goalflow/examples/`](goalflow/examples/) | Illustrative dashboard goal, specification, verification, and verdict |

## Project status and limits

This repository contains the workflow pack; the target application is supplied separately. The sample artifacts illustrate the output format and are not a reproducible benchmark or evidence that a bundled application passed tests.

The workflow depends on the host Bob version, enabled tools, MCP access, and target project's checks. Tool names and mode support should be verified in that environment. Separate-context judging reduces shared conversation bias but still requires reliable QA evidence and human review for consequential changes. No end-to-end Bob run is included as an automated test suite.
