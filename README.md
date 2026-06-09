# craft-skills

**Skills for code you'll have to live with.**

Most coding skills help you *produce* code faster. These help you produce code that the next person — including future-you, and the next agent — can actually understand, change, and trust. They target the part of "code quality" that agents are worst at: not syntax, but **legibility and restraint**.

Each skill fixes one specific failure mode, does one thing, works with any model, and is small enough to read in two minutes.

## The thesis

AI can write working code all day. The bottleneck has moved: the expensive problems now are code nobody understands, names that hide intent, files that only grow, failures that vanish silently, logic that can't be tested, and "quick changes" that become rewrites. `craft-skills` is a focused set of disciplines for exactly those problems — the craft layer, not the typing layer.

## The skills

### Craft

| Skill | What it does |
|---|---|
| [`explain-back`](skills/craft/explain-back/SKILL.md) | The everyday hero. Before code is trusted, the agent explains it back in plain language and refuses to proceed until the mental model is confirmed. Code you can't explain is code you don't own. |
| [`delete-this`](skills/craft/delete-this/SKILL.md) | The surprising one — an agent that *removes* code, safely. Finds what no longer earns its place and proves it's dead before deleting, staged so each removal is independently revertible. |
| [`name-things`](skills/craft/name-things/SKILL.md) | Kills generic names (`data`, `handle`, `manager`, `util`) and makes every name reveal intent and match the domain. |
| [`seams`](skills/craft/seams/SKILL.md) | Splits the decision (pure, testable logic) from the action (I/O and side effects) so "I can't test this without mocks" stops being true. |
| [`loud-errors`](skills/craft/loud-errors/SKILL.md) | Finds swallowed and vague errors and makes failures loud and specific — what failed, with which inputs, and what to do next — instead of vanishing into an empty `catch`. |
| [`boyscout`](skills/craft/boyscout/SKILL.md) | Leave the file a little cleaner than you found it — small, safe, in-scope cleanup that never balloons into a rewrite. |

### Setup

| Skill | What it does |
|---|---|
| [`setup-craft-skills`](skills/setup/setup-craft-skills/SKILL.md) | Run once per repo (optional but recommended). Scaffolds a `CONTEXT.md` (domain glossary + conventions + untouchables) that the other skills read *if present* to match your codebase. The skills work without it. |

## Two front doors

`delete-this` is the one to try first for the surprise — most people don't expect an agent to *remove* code, let alone prove each deletion is safe first. `explain-back` is the one you'll reach for every day — it runs on any non-trivial diff and tells you what your code actually does versus what you think it does. One is the hook; the other is the habit.

## Install

Via the Claude Code plugin marketplace:

```bash
/plugin marketplace add mikestangdevs/craft-skills
/plugin install craft-skills
```

Or with the skills CLI:

```bash
npx skills@latest add mikestangdevs/craft-skills
```

Then, once per repository (optional but recommended):

```
/setup-craft-skills
```

## Design principles

These follow the conventions that make a skill collection composable and adoptable:

- **One failure mode per skill.** If a skill does two things, it's two skills.
- **Flip the dynamic.** The best ones (`explain-back`) make the agent prove understanding instead of asserting it.
- **Restraint is a feature.** `delete-this`, `boyscout`, `loud-errors`, and `seams` are all about doing the *right* less, carefully — including the things agents reflexively get wrong (deleting nothing, swallowing failures).
- **Model-agnostic.** Plain instructions, no dependency on a specific model or runtime.
- **Sharp descriptions.** Each `description` field names concrete trigger situations so the agent knows exactly when to reach for it.

## Roadmap

Deliberately small at launch. Candidates being considered for a later release:

- **`why-comments`** — keep the comments that capture the non-obvious *why*, delete the ones that just narrate *what*, and add a "why" where load-bearing code has none.
- **`fit-in`** — match the surrounding code's existing patterns and idioms instead of importing a generic style.

## License

MIT
