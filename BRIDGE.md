# BRIDGE — Continuum

Stable context for any agent or person picking up this project. Update only
when a decision changes; day-to-day movement belongs in STATUS.md.

## What this project is

Continuum is a persistent memory system for Claude Code, shipped as a public
repository. It is a file convention (one markdown fact per file + a one-line
index), a session protocol (recall at start, save at end), a handoff layer
(BRIDGE.md + STATUS.md), a Claude Code skill that teaches the workflow, and a
curator agent that keeps the store healthy.

Audience: solo founders, indie developers, and operators who use Claude Code
daily and are tired of re-explaining context every session.

## Decisions made (and why)

1. **Plain markdown files, no database, no embeddings.** Inspectability is
   the core promise — a memory the user cannot read is a memory they cannot
   trust or fix. At personal scale (tens to low hundreds of facts) an indexed
   file store outperforms retrieval infrastructure on reliability and costs
   nothing.
2. **One fact per file.** Small files can be updated or deleted atomically;
   monolithic memory files rot because corrections get appended instead of
   applied. Filename = the `name` slug in frontmatter.
3. **Four memory types only** — `user`, `feedback`, `project`, `reference`.
   A small closed set keeps triage instant. `feedback` and `project` entries
   must carry **Why:** and **How to apply:** so the fact is actionable, not
   archival.
4. **Index loads, entries load on demand.** MEMORY.md (one line per memory)
   is the only always-loaded surface, keeping context cost flat as the store
   grows.
5. **Wrong facts get deleted, not amended.** Appending corrections is how
   memory stores accumulate contradictions.
6. **Handoff is part of memory.** BRIDGE.md (stable) + STATUS.md (volatile)
   are deliberately separate files so the stable layer stays short and the
   volatile layer can be rewritten wholesale each session.
7. **Curation is an explicit agent, not a hope.** memory-curator merges
   duplicates, prunes stale entries, fixes links, and rewrites the index;
   it never invents facts.
8. **Distribution: copy-in files, no installer.** Two `cp` commands and a
   `mkdir` — anything more is friction for the target user.
9. **This repo dogfoods its own conventions** (its own BRIDGE.md, STATUS.md,
   and CLAUDE.md follow the system they describe).

## Key files

- `skills/continuum/SKILL.md` — source of truth for format + workflow
- `agents/memory-curator.md` — maintenance agent
- `templates/` — the four copyable templates
- `examples/memory/` — fictional founder (Maya Chen / Driftwatch) memory set
- `docs/INSTALL.md`, `docs/HOW_TO_USE.md` — user-facing docs

## Constraints

- MIT licensed, copyright Nina Cressoni.
- No external dependencies, ever. If a feature needs a service, it is out of
  scope for this product.

_Last updated: 2026-07-15 — v1 published to https://github.com/ninaneev/continuum. Business model: repos free/open source; revenue = €149 guided install per system + €1,500 Full Stack Install flagship (sold via ninaneev.github.io, Stripe pending)._
