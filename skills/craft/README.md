# craft

The craft skills — legibility and restraint for code you'll have to live with. Each fixes one specific failure mode and is small enough to read in two minutes.

| Skill | What it does |
|---|---|
| [`explain-back`](explain-back/SKILL.md) | The everyday hero. Before code is trusted, the agent explains it back in plain language and refuses to proceed until the mental model is confirmed. |
| [`delete-this`](delete-this/SKILL.md) | The surprising one — an agent that *removes* code, safely. Proves code is dead before deleting, staged so each removal is independently revertible. |
| [`name-things`](name-things/SKILL.md) | Kills generic names (`data`, `handle`, `manager`, `util`) and makes every name reveal intent and match the domain. |
| [`seams`](seams/SKILL.md) | Splits the decision (pure, testable logic) from the action (I/O and side effects) so "I can't test this without mocks" stops being true. |
| [`loud-errors`](loud-errors/SKILL.md) | Finds swallowed and vague errors and makes failures loud and specific — what failed, with which inputs, and what to do next. |
| [`boyscout`](boyscout/SKILL.md) | Leave the file a little cleaner than you found it — small, safe, in-scope cleanup that never balloons into a rewrite. |
