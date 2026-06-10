# rigor

The rigor skills — proof over vibes, for work someone has to trust. Where the craft skills make code *livable*, these make claims *believable*: every "done" backed by evidence, every number backed by a source, every fix checked against its siblings. Each fixes one specific failure mode and is small enough to read in two minutes.

These were mined from real working sessions: hundreds of hours of agent-assisted engineering on a physics simulation product where a fudged constant or a "should work now" isn't a style problem — it's a wrong answer someone acts on. The patterns generalize to any codebase where output feeds a decision.

| Skill | What it does |
|---|---|
| [`test-like-you-mean-it`](test-like-you-mean-it/SKILL.md) | The flagship. After any build, switch sides and try to break it — invariants, edge regimes, cross-checks. Not smoke tests, not tests written to pass. Findings are the expected outcome. |
| [`bank-the-suite`](bank-the-suite/SKILL.md) | Run the *full* suite after every unit of work (background it, parallelize it, never skip it) and bank a verified green: honest counts, pre-existing failures carved out by name. |
| [`prove-every-number`](prove-every-number/SKILL.md) | Every constant is derived or cited — never invented, never tuned to make a test pass. Real data is the truth; an assumption is the explicitly-labeled backup. |
| [`smell-the-numbers`](smell-the-numbers/SKILL.md) | Read the output like a domain expert: implausible values are guilty until root-caused. Fix the source, never the display. |
| [`everywhere-else`](everywhere-else/SKILL.md) | "Did you do that anywhere else?" After any fix, enumerate the full population of sibling sites and sweep them — reporting the clean ones too. |
| [`fix-it-now`](fix-it-now/SKILL.md) | A found bug gets fixed in the current pass, not documented and deferred. Deferrals are explicit negotiations; half-kept features get finished or removed. |
| [`readiness-gate`](readiness-gate/SKILL.md) | Replaces "should work now" with an evidence-backed go/no-go: verified vs. not verified, contract walked line by line, certainty calibrated to proof. |
| [`checkpoint-handoff`](checkpoint-handoff/SKILL.md) | Before any context boundary, write the resume state to durable files — numbered remaining tasks, standing constraints verbatim, files to re-read — so a cold session continues without asking. |

## How they chain

A single unit of work, run with the full set: build → **test-like-you-mean-it** (adversarial wave) → **fix-it-now** (findings die in this pass) → **everywhere-else** (sweep the siblings) → **bank-the-suite** (full regression banked green) → **smell-the-numbers** (read the real output) → **readiness-gate** (evidence-backed go/no-go) → **checkpoint-handoff** (state written down) → next unit. **prove-every-number** rides along the whole way.
