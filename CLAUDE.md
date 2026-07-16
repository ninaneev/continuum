# Continuum — instructions for Claude

This repository IS the product. It ships a memory convention, a skill, an
agent, templates, and docs. There is no application code and no build step.

## Before any work

1. Read `BRIDGE.md` (what this project is, decisions already made).
2. Read `STATUS.md` (current state, next steps).
3. If the task touches formats, read `skills/continuum/SKILL.md` — it is the
   single source of truth for the memory-entry format and workflow.

## Hard rules

- **Consistency over cleverness.** The memory-entry format (frontmatter keys,
  the four `metadata.type` values, `[[name]]` links, **Why:** / **How to
  apply:** lines) is defined once in `skills/continuum/SKILL.md`. Every other
  file — templates, examples, docs — must match it exactly. If you change the
  format, change it everywhere in the same session.
- **Templates must stay copy-paste usable.** Angle-bracket placeholders like
  `<one line>` are the only acceptable placeholder style; no lorem ipsum.
- **Examples must stay realistic.** The `examples/memory/` directory models a
  fictional founder (Maya Chen / Driftwatch). Keep it internally consistent:
  every `[[link]]` resolves to a file in that directory, the index lists every
  file, dates are absolute.
- **Docs describe what exists.** Never document behavior the skill or agent
  does not actually instruct.
- **Dogfood.** When you finish meaningful work in this repo, update
  `STATUS.md` (always) and `BRIDGE.md` (only if a decision changed).
- **Frontmatter validity matters.** `skills/continuum/SKILL.md` must keep
  `name: continuum` and a `description:` starting with "Use when".
  `agents/memory-curator.md` must keep valid agent frontmatter with its
  `tools:` list. Breaking either breaks the install.

## Writing style

Plain, direct, senior-engineer voice. No hype words, no exclamation points,
no emojis outside README section markers. Absolute dates (2026-07-15), never
"recently" or "last week".
