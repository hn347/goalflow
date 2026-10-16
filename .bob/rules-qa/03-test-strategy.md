# Test Strategy

Choose the smallest meaningful verification set first, then expand as risk requires:

1. targeted tests;
2. typecheck or lint;
3. build;
4. smoke checks;
5. **browser validation for UI — activate the `agent-browser` skill (`.bob/skills/agent-browser/SKILL.md`) and perform live browser QA; this step is mandatory for UI goals, not optional**.

Explain skipped checks in `.goal/verification.md`.
