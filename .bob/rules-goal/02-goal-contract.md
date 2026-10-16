# Goal Contract

The goal contract is populated by the **grill-me session**, not written from the original request
alone. Before any contract file is written:

1. Activate `use_skill: grill-me`.
2. Conduct the interview (one question at a time, recommended answer attached to each).
3. Get user confirmation of the full contract summary.

Only then write `.goal/goal.md` with:

- original user request;
- explicit in-scope outcomes;
- out-of-scope items;
- acceptance criteria — each one specific and independently testable;
- evaluation metrics — quantified where possible;
- QA watch list — specific things to check during verification (e.g. image load checks, console errors, third-party service reachability);
- test seams — which public interfaces to test at (fed into the TDD phase);
- constraints and safety notes;
- required verification evidence.

Do not silently expand the goal. If the requested outcome is ambiguous or risky, halt and ask for
clarification before Builder mode begins.
