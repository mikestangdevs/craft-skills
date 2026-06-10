# craft-skills

**Skills for code you'll have to live with.**

These are the skills I reach for to get *craft* out of an agent, not just more code. Most coding skills help you produce code faster. These help you produce code that the next person — including future-you, and the next agent — can actually understand, change, and trust.

AI can write working code all day. For me the bottleneck moved a while ago: the expensive problems now are code nobody understands, names that hide intent, files that only grow, failures that vanish silently, and "quick changes" that become rewrites. And right behind those, a second family: tests written to pass, invented constants, point fixes on systemic bugs, and "should work now" shipped as if it were evidence.

So there are two collections here. **Craft** is the legibility layer — it makes code the next person can understand, change, and trust. **Rigor** is the proof layer — it makes the *claims about* that code believable: every "done" backed by evidence, every number backed by a source, every fix checked against its siblings. The rigor skills weren't designed on a whiteboard; they were mined from hundreds of hours of my own agent sessions on a physics simulation product, where a fudged constant isn't a style problem — it's a wrong answer someone acts on. Each skill in both collections fixes one specific failure mode, does one thing, works with any model, and is small enough to read in two minutes. Hack on them, make them your own.

## Quickstart

1. **Install** — via the Claude Code plugin marketplace:

   ```bash
   /plugin marketplace add mikestangdevs/craft-skills
   /plugin install craft-skills
   ```

   …or with the skills CLI (works across agents, not just Claude Code):

   ```bash
   npx skills@latest add mikestangdevs/craft-skills
   ```

2. **(Optional but recommended)** Run it once per repo:

   ```
   /setup-craft-skills
   ```

   It scaffolds a `CONTEXT.md` — your domain glossary, conventions, and "untouchables" — that the other skills read to match *your* codebase. Skip it and the skills still work; they just fall back to detecting your conventions on the fly.

3. That's it. The skills trigger when they're relevant, or you can invoke any of them by name.

## Why the craft skills exist

I built these to fix the failure modes I kept hitting with Claude Code and other agents — the ones that don't show up as a red test, but as a codebase that slowly becomes harder to live in.

### Code that looks right but nobody understands

The agent writes 80 lines, you skim, say "looks good," and merge. Six weeks later it breaks and no one — human or agent — can say why it existed. The expensive bug in AI-assisted work isn't wrong code; it's code nobody built a model of.

[`explain-back`](skills/craft/explain-back/SKILL.md) inverts review: before code is trusted, the agent explains it back in plain language — what it does, why it exists, what breaks if it's wrong — and refuses to proceed until you confirm the model is right. If it can't explain *why* a line is there, it flags it. **Code you can't explain is code you don't own.** This is the one I use most.

### The codebase only ever grows

Every feature adds; almost nothing subtracts. Agents make it worse — trained to produce code, they route *around* dead code instead of removing it. The result is a graveyard of commented-out blocks, flags pinned to one value for months, and abstractions built for a second caller that never arrived. Every line of it is read, parsed, and feared by everyone who touches the file.

[`delete-this`](skills/craft/delete-this/SKILL.md) is the surprising one: an agent that *removes* code — and proves each deletion is dead first (grep, build, the works), staged so every removal is its own revertible commit. **The best refactor is often a deletion.**

### Names that hide intent

`data`, `handle`, `manager`, `process`, `util` — every one is a place where the author gave up on saying what the thing is. Agents are especially good at generating plausible-but-vague names at scale, and the result is code that's technically correct and cognitively exhausting.

[`name-things`](skills/craft/name-things/SKILL.md) kills generic names and makes each one reveal its intent and role, aligned to your domain's actual vocabulary — so you don't have to read the implementation to use it.

### "I can't test this"

That almost always means the code has no seams. Decision and action are fused — one function fetches, computes, branches on policy, *and* writes, all in one breath — so every test needs a live database and the logic you actually care about is impossible to isolate.

[`seams`](skills/craft/seams/SKILL.md) finds the joint between *deciding* and *doing* and splits along it: a pure decision you can test with plain values and zero mocks, and a thin action that just carries it out. The proof is a unit test that needs no mocks.

### Failures that vanish silently

The empty `catch {}`, the bare `except: pass`, the `throw new Error("something went wrong")`. Agents swallow errors to make the happy path run, which turns a clean crash into a silent corruption that surfaces hours later, somewhere else, with no trace back to the cause.

[`loud-errors`](skills/craft/loud-errors/SKILL.md) hunts down swallowed and vague failures and makes them loud and specific — what operation failed, with which inputs, and what to do next — while keeping the rare *intentional* swallow explicit instead of accidental.

### Comments that narrate instead of explain

Agents pad code with `// increment the counter` — comments that restate what the code already says, rot on the next edit, and train every reader to skip comments entirely. Meanwhile the one thing a comment can do that code can't — record *why* the retry is 3 and not 5, why the obvious approach was tried and reverted — goes unwritten. Six months later someone "simplifies" the workaround and reintroduces the bug it guarded.

[`why-comments`](skills/craft/why-comments/SKILL.md) inverts the ratio: deletes the *whats*, keeps and sharpens the *whys* (the constraint, the evidence, what breaks if you "fix" it), and adds a why where surprising code has none.

### Code that's correct but doesn't belong

The repo uses result types; the agent throws. Tests live next to the source; the agent creates `__tests__/`. The codebase already has a retry helper; the agent writes a second one. None of it is a bug, and all of it is cost — a codebase edited by agents long enough becomes a patchwork of five house styles with no house.

[`fit-in`](skills/craft/fit-in/SKILL.md) makes reading the neighborhood a mandatory first step: match the local idiom, reuse the local solutions, and treat "improving" the style as a separate, deliberate act. **Convention never outranks correctness** — harmful local patterns get flagged, not replicated.

### The "quick change" that became a rewrite

Two opposite failures rot a codebase: never cleaning up, so entropy compounds until the file is a no-go zone — or a 10-line change ballooning into a 400-line refactor that's impossible to review and ships three new bugs. Agents swing wildly between both.

[`boyscout`](skills/craft/boyscout/SKILL.md) threads the needle: leave the file a little cleaner than you found it, but keep the cleanup small, safe, in-scope, and separate from the behavior change. **The discipline is the smallness.**

## Why the rigor skills exist

Craft is about the code; rigor is about the claims. An agent doesn't just produce diffs — it produces assertions: "tests pass," "this value is right," "fixed," "ready." I kept getting burned not by bad code but by **confident claims that turned out to be vibes**. These eight are the counters, one per failure mode.

### Tests that were written to pass

The agent built the code, wants the code to work, and writes tests that confirm it does — happy paths, assertions copied from the implementation's own output. The suite goes green and verifies nothing. A test written to pass is a green light bolted over a blind corner.

[`test-like-you-mean-it`](skills/rigor/test-like-you-mean-it/SKILL.md) makes the agent switch sides after every build: attack the thing with invariants, edge regimes, and independent cross-checks, where *finding a defect is the expected outcome*. **Not smoke tests. Not safely. Test it for real.**

### "My tests pass" while the other 4,000 rot

The agent runs the three tests next to its change and moves on. Four tasks later the full suite runs and 23 tests are red — and nobody knows which change broke them. Or the suite *is* run, fails, and the report waves it off as "probably pre-existing."

[`bank-the-suite`](skills/rigor/bank-the-suite/SKILL.md) runs the *whole* suite after every unit of work — backgrounded and parallelized, never skipped — and banks a verified green: honest counts against a baseline, every red accounted for by name. **New red has exactly one suspect.**

### The number that came from nowhere

`efficiency = 0.92`, `timeout = 30`, `1.2x` — invented because they look plausible, then trusted by everyone downstream who can't see they were guesses. The malignant variant: a failing test "fixed" by adjusting the constant until it passes.

[`prove-every-number`](skills/rigor/prove-every-number/SKILL.md) enforces provenance: every number derived or cited — real data first, explicitly-labeled assumption as the fallback, invention never. **The test result is never evidence for a value.**

### Output that's obviously wrong, accepted because it rendered

A line at 1858% of its rated capacity. A formula panel of zeros. A metric that moved 40x between identical runs. The code ran, so the agent celebrates — or worse, clamps and smooths the display until the nonsense looks plausible.

[`smell-the-numbers`](skills/rigor/smell-the-numbers/SKILL.md) reads output like a domain expert: implausible values are guilty until root-caused, the trace goes all the way down, and the fix lands at the source — never in the display. **"It runs" is not "it's right."**

### The point fix on a population bug

The bug gets fixed in scenario 12. The report says "fixed." Scenarios 1–11 and 13–20 — same copy-paste lineage, same defect — keep lying, and you rediscover them one expensive debugging session at a time.

[`everywhere-else`](skills/rigor/everywhere-else/SKILL.md) asks the question agents never ask themselves: *did you do that anywhere else?* Enumerate the sibling population by search (not memory), audit every member, fix the origin — and report the clean ones too. **A defect found once is a sample, not the population.**

### The bug that was documented instead of fixed

An audit finds six real defects and produces a beautiful list. The list gets filed; the defects stay. Three weeks later one detonates — and the worst part is that the system *knew*.

[`fix-it-now`](skills/rigor/fix-it-now/SKILL.md) treats discovery as the cheapest fix you'll ever be offered: confirmed bugs die in the current pass, deferrals are explicit negotiations (not buried TODOs), and half-kept features get finished or removed. **A documented bug is a bug with better PR.**

### "Should work now"

The most expensive sentence in agent-assisted engineering. You ship on it, and production tells you what "should" was hiding. Pushed with "are you sure?", the agent escalates adjectives instead of evidence.

[`readiness-gate`](skills/rigor/readiness-gate/SKILL.md) replaces the mood with a structured claim: verified vs. not verified, the spec walked line by line (not from memory), certainty calibrated to proof, and an explicit go/no-go that's allowed to say no. **Answer "are you sure?" with evidence, not enthusiasm.**

### The session that forgot everything

Long projects outlive context windows — compaction, restarts, and tomorrow all guarantee it. The next session re-derives the plan, redoes finished work, and re-violates the rule you stated forcefully forty turns ago.

[`checkpoint-handoff`](skills/rigor/checkpoint-handoff/SKILL.md) writes the resume state to durable files before every boundary: what's done (with evidence), numbered remaining tasks, standing constraints verbatim, and the files to re-read first. The test: **a cold session could pick up the next task without asking a single question.**

---

These are the fundamentals I keep reaching for, condensed into skills an agent can actually follow. Run `/setup-craft-skills` once so they speak your codebase's language, then make them your own.

## Reference

### Craft

| Skill | What it does |
|---|---|
| **[explain-back](skills/craft/explain-back/SKILL.md)** | Before code is trusted, the agent explains it back in plain language and refuses to proceed until the mental model is confirmed. |
| **[delete-this](skills/craft/delete-this/SKILL.md)** | Finds code that no longer earns its place and removes it safely — proving it's dead first, staged so each removal is independently revertible. |
| **[name-things](skills/craft/name-things/SKILL.md)** | Kills generic names (`data`, `handle`, `manager`, `util`) and makes every name reveal intent and match the domain. |
| **[seams](skills/craft/seams/SKILL.md)** | Splits the decision (pure, testable logic) from the action (I/O and side effects) so "I can't test this without mocks" stops being true. |
| **[loud-errors](skills/craft/loud-errors/SKILL.md)** | Finds swallowed and vague errors and makes failures loud and specific — what failed, with which inputs, and what to do next. |
| **[why-comments](skills/craft/why-comments/SKILL.md)** | Deletes comments that narrate *what* the code already says, keeps and sharpens the ones that capture a non-obvious *why*, and adds a why where surprising code has none. |
| **[fit-in](skills/craft/fit-in/SKILL.md)** | Reads the surrounding code first and matches its idioms — error style, test layout, naming, existing helpers — instead of importing a generic house style. |
| **[boyscout](skills/craft/boyscout/SKILL.md)** | Leave the file a little cleaner than you found it — small, safe, in-scope cleanup that never balloons into a rewrite. |

### Rigor

Proof over vibes — craft makes code *livable*, rigor makes claims *believable*.

| Skill | What it does |
|---|---|
| **[test-like-you-mean-it](skills/rigor/test-like-you-mean-it/SKILL.md)** | After any build, switch sides and try to break it — invariants, edge regimes, cross-checks. Not smoke tests, not tests written to pass; findings are the expected outcome. |
| **[bank-the-suite](skills/rigor/bank-the-suite/SKILL.md)** | Run the *full* suite after every unit of work (background it, parallelize it, never skip it) and bank a verified green — honest counts, pre-existing failures carved out by name. |
| **[prove-every-number](skills/rigor/prove-every-number/SKILL.md)** | Every constant is derived or cited — never invented, never tuned to make a test pass. Real data is the truth; an assumption is the explicitly-labeled backup. |
| **[smell-the-numbers](skills/rigor/smell-the-numbers/SKILL.md)** | Read the output like a domain expert: implausible values are guilty until root-caused. Fix the source, never the display. |
| **[everywhere-else](skills/rigor/everywhere-else/SKILL.md)** | "Did you do that anywhere else?" After any fix, enumerate the full population of sibling sites and sweep them — reporting the clean ones too. |
| **[fix-it-now](skills/rigor/fix-it-now/SKILL.md)** | A found bug gets fixed in the current pass, not documented and deferred. Deferrals are explicit negotiations; half-kept features get finished or removed. |
| **[readiness-gate](skills/rigor/readiness-gate/SKILL.md)** | Replaces "should work now" with an evidence-backed go/no-go: verified vs. not verified, contract walked line by line, certainty calibrated to proof. |
| **[checkpoint-handoff](skills/rigor/checkpoint-handoff/SKILL.md)** | Before any context boundary, write the resume state to durable files — numbered remaining tasks, standing constraints verbatim, files to re-read — so a cold session continues without asking. |

### Setup

| Skill | What it does |
|---|---|
| **[setup-craft-skills](skills/setup/setup-craft-skills/SKILL.md)** | Run once per repo (optional but recommended). Scaffolds a `CONTEXT.md` (domain glossary + conventions + untouchables) that the other skills read *if present* to match your codebase. |

## Roadmap

Deliberately small. Candidates being considered for a later release:

- **`honest-tests`** — kill tautological tests: tests that mock every collaborator and assert the mocks were called mirror the implementation and pass forever, proving nothing. Assert observable behavior; a test that can't fail when the logic breaks gets deleted. Sits between `seams` and `test-like-you-mean-it`: seams makes logic testable, honest-tests makes existing tests mean something at write/review time, test-like-you-mean-it is the post-build adversarial wave.
- **`second-caller`** — YAGNI at write-time: no abstraction until the second concrete caller exists. `delete-this` removes speculative generality after the fact; this prevents it — the config option nobody passes, the interface with one implementation, the "pluggable" factory for a hypothetical future.
- **`research-handoff`** — when a fact can't be derived from the repo or cited from memory, don't guess: draft a self-contained research prompt (context, exact questions, required citation quality) for an external research agent, then *judge the returns against the codebase* before integrating them. Pairs with `prove-every-number` — it's where the citations come from.
- **`engineer-pass`** — review the deliverable as a named, demanding persona (the SCADA engineer, the technical COO, the on-call dev) and reject what they'd reject: filler narration, charts with no formula, reports that don't walk the evidence. Output isn't done when it's generated; it's done when the persona would sign it.
- **Eval scenarios** — small fixtures per skill (a snippet with a swallowed error, a function with no seam) to regression-test that wording changes don't break skill triggering or output shape.
