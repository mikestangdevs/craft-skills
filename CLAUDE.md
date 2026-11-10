# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`craft-skills` is a **Claude Code plugin**: a curated collection of agent Skills, not a runnable application. There is no source to compile and no runtime — each skill is a Markdown file (`SKILL.md`) of instructions an agent loads on demand. "Building" here means keeping the two manifests and the skill files mutually consistent and passing the official plugin validator.

The thesis (see `README.md`): skills for "code you'll have to live with" — **craft** (legibility and restraint) and **rigor** (claims backed by evidence), the parts of code quality agents are worst at, deliberately *not* the typing/refactor lane.

## Commands

```bash
# Validate the plugin + marketplace manifests (the closest thing to a build/test)
claude plugin validate .
```

There is no test runner, linter, or build step. The validator checks `.claude-plugin/marketplace.json`; for the plugin itself, correctness is verified by the consistency invariants below.

## Architecture

Two manifests in `.claude-plugin/` and one directory of skills:

- **`.claude-plugin/plugin.json`** — the plugin manifest. The `skills` array lists each skill by its **individual directory path** (e.g. `./skills/craft/explain-back`), not a parent dir. This explicit form is valid and is how skills get registered; a skill that isn't in this array won't load.
- **`.claude-plugin/marketplace.json`** — the marketplace manifest so `/plugin marketplace add mikestangdevs/craft-skills` works. It references this same repo as a single plugin via `"source": "./"`.
- **`skills/<bucket>/<name>/SKILL.md`** — one skill per directory. Buckets (`craft/`, `rigor/`, `setup/`) are organizational only; Claude Code invokes a skill by its leaf directory name regardless of bucket.

### Bucket organization & governance

Skills live in **buckets** under `skills/`. Buckets are one of two kinds:

- **Shipped buckets** (currently `craft/`, `rigor/`, `setup/`) — skills that are part of the published plugin. **Every skill in a shipped bucket must be referenced in both the top-level `README.md` and `.claude-plugin/plugin.json`.**
- **Staging buckets** (`in-progress/`, `deprecated/`, `personal/`) — not created yet; add one only when you actually have a skill to park there. **Skills in a staging bucket must appear in neither `README.md` nor `plugin.json`** — staging lets you draft or retire skills in-repo without shipping them.

Conventions:

- **Each bucket folder has its own `README.md`** listing every skill in that bucket with a one-line description, with each skill name linked to its `SKILL.md`. Staging buckets get a README too (so the folder explains itself), but their skills still stay out of the top-level README and `plugin.json`.
- **The top-level `README.md` links each skill name to its `SKILL.md`.**
- This repo is deliberately small and curated: roadmap/candidate skills live as prose under `## Roadmap` in the top-level `README.md`, not as empty staging folders. Create a staging bucket only when a real draft needs parking.

### How the skills relate (the big picture)

The `craft/` skills each own exactly one failure mode and are designed to compose:

- **`explain-back`** — build & confirm a mental model before trusting code (the everyday hero). Routes `UNJUSTIFIED` dead chunks to `delete-this` and naming stumbles to `name-things`.
- **`delete-this`** — prove code is dead, then remove it in revertible stages (the surprising front door).
- **`name-things`** — kill generic names; align to the domain vocabulary.
- **`seams`** — split pure decision from effectful action so logic is testable with no mocks (functional-core / imperative-shell at the function level — deliberately distinct from architectural layering).
- **`loud-errors`** — make swallowed/vague failures loud and specific.
- **`why-comments`** — delete comments that narrate *what*, keep/sharpen/add the non-obvious *why*. Hands commented-out code to `delete-this`; unclear-code-propped-by-comment to `name-things`.
- **`fit-in`** — read the neighborhood first; match local idiom and reuse existing solutions instead of importing a generic style. Convention never outranks correctness: harmful local patterns are flagged (`loud-errors`/`name-things` win), not replicated. Style crusades route to `boyscout` (small) or a separate proposal (big).

- **`subtract-first`** — when work is rejected as too much, remove one thing and re-show before adding anything; one change per look.
- **`shared-tree`** — in a working tree holding someone else's uncommitted changes, stage by explicit path, avoid tree-wide destructive commands, copy aside before rewriting.

`boyscout` is a meta-skill: small, opportunistic, in-scope cleanup riding along with another change, and it may invoke the others *at small scale*. The boundary with `delete-this` matters and is cross-referenced in both files: `boyscout` = adjacent cleanup while editing; `delete-this` = a deliberate repo-wide sweep. A confirmed bug found along the way is not cleanup; it routes to `fix-it-now`.

The `rigor/` skills cover claims rather than code, one failure mode each, and chain across a unit of work (see `skills/rigor/README.md`): `test-like-you-mean-it` → `fix-it-now` → `everywhere-else` → `bank-the-suite` → `smell-the-numbers` → `readiness-gate` → `checkpoint-handoff`. `prove-every-number` applies throughout, and `held-out` applies whenever something fitted or tuned is being scored. Adjacent boundaries: `test-like-you-mean-it` is for deterministic code with a spec, `held-out` for fitted systems; `prove-every-number` is about a number's source, `smell-the-numbers` about an output's plausibility.

`setup/setup-craft-skills` is a run-once scaffolder that generates an **optional** `CONTEXT.md` at a consuming repo's root (domain glossary + `Avoid` synonym ban-list + relationships + flagged ambiguities + conventions + untouchables). The craft skills read `CONTEXT.md` *if present* to match the project; they degrade gracefully when it's absent. Note: the `CONTEXT.md` it describes lives in the **user's** repo after they run the skill — it is not committed to this plugin repo.

## SKILL.md conventions (match these when adding or editing a skill)

Every `SKILL.md` follows the same shape — keep it consistent:

1. **YAML frontmatter** with exactly two keys: `name` (must equal the directory name) and a sharp `description`. The `description` must name **concrete trigger situations** (when to reach for it), not vibes — this is what makes the agent fire it reliably. (No `license`/`metadata` block — licensing lives in the root `LICENSE` file.)
2. Body sections, in order: `## The failure mode this fixes` → `## When to Use This Skill` (including a bold **Don't use when:** honest negative) → `## Instructions` (numbered phases) → `## Output format` (a fenced block) → `## Anti-Patterns` (a table of anti-pattern → why it defeats the skill) → `## Mental Model` (a one-line blockquote).
3. One thing per skill. If it does two things, it's two skills.
4. Model-agnostic, plain instructions, ~2-minute read.

## Consistency invariants (verify after any change)

When adding, renaming, or removing a skill, all of these must hold (a stale one silently breaks `/plugin install`):

- Frontmatter `name` == leaf directory name.
- A skill in a **shipped** bucket (`craft/`, `rigor/`, `setup/`) is referenced in **both** `plugin.json`'s `skills` array **and** the top-level `README.md` (table row + working relative link). A skill in a **staging** bucket appears in **neither**.
- The skill is listed in its **bucket's `README.md`** with a one-line description, name linked to its `SKILL.md`.
- `setup-craft-skills` mentions the skill in its "before using" list if it consumes `CONTEXT.md`.
- The release version lives in `plugin.json` (`version`); skills carry no per-file version.
- `claude plugin validate .` passes.

## Conventions

- Cross-reference sibling skills explicitly when their scopes are adjacent (e.g. `boyscout` ↔ `delete-this`) so the agent routes to the right one.
- Keep launch scope small and curated; new skill ideas are parked under `## Roadmap` in `README.md` rather than shipped half-formed.
