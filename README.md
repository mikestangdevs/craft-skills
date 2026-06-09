# craft-skills

**Skills for code you'll have to live with.**

These are the skills I reach for to get *craft* out of an agent, not just more code. Most coding skills help you produce code faster. These help you produce code that the next person — including future-you, and the next agent — can actually understand, change, and trust.

AI can write working code all day. For me the bottleneck moved a while ago: the expensive problems now are code nobody understands, names that hide intent, files that only grow, failures that vanish silently, and "quick changes" that become rewrites. These six skills are my answer to exactly those — the craft layer, not the typing layer. Each fixes one specific failure mode, does one thing, works with any model, and is small enough to read in two minutes. Hack on them, make them your own.

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

## Why these skills exist

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

### The "quick change" that became a rewrite

Two opposite failures rot a codebase: never cleaning up, so entropy compounds until the file is a no-go zone — or a 10-line change ballooning into a 400-line refactor that's impossible to review and ships three new bugs. Agents swing wildly between both.

[`boyscout`](skills/craft/boyscout/SKILL.md) threads the needle: leave the file a little cleaner than you found it, but keep the cleanup small, safe, in-scope, and separate from the behavior change. **The discipline is the smallness.**

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
| **[boyscout](skills/craft/boyscout/SKILL.md)** | Leave the file a little cleaner than you found it — small, safe, in-scope cleanup that never balloons into a rewrite. |

### Setup

| Skill | What it does |
|---|---|
| **[setup-craft-skills](skills/setup/setup-craft-skills/SKILL.md)** | Run once per repo (optional but recommended). Scaffolds a `CONTEXT.md` (domain glossary + conventions + untouchables) that the other skills read *if present* to match your codebase. |

## Roadmap

Deliberately small at launch. Candidates being considered for a later release:

- **`why-comments`** — keep the comments that capture the non-obvious *why*, delete the ones that just narrate *what*, and add a "why" where load-bearing code has none.
- **`fit-in`** — match the surrounding code's existing patterns and idioms instead of importing a generic style.
