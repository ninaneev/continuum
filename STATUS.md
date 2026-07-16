# STATUS — Continuum

Last updated: 2026-07-15

## Current state

v1 scaffold complete (2026-07-15). All core assets written and internally
consistent:

- Memory convention defined in `skills/continuum/SKILL.md` (format, save
  rules, dedupe, absolute dates, index discipline)
- Curator agent at `agents/memory-curator.md`
- Templates: memory-entry, MEMORY, BRIDGE, STATUS
- Example memory directory (Maya Chen / Driftwatch, 5 entries + index)
- Docs: INSTALL.md (with verification test), HOW_TO_USE.md (daily workflow,
  good vs bad entries, handoff ritual)
- README, LICENSE (MIT), .gitignore

Not yet done: no git history (files only), nothing published.

## Next steps

1. Record demo video: cold session → recall → "what do you know about my
   deploy schedule?" moment.
2. Record install video following docs/INSTALL.md verbatim (also validates
   the doc).
3. List on the website (product page + link to repo).
4. After first external users: collect friction points, revisit whether a
   session-start hook should automate index loading.

## Open questions

- Global memory location: docs currently suggest `~/claude-memory/` for
  cross-project facts; confirm this is the recommendation we want before the
  install video.

---

## Session handoff 2026-07-15

- Done: repo scaffolded, committed (9de108e), published public to https://github.com/ninaneev/continuum (MIT where applicable)

- Known broken: nothing known; no verification run in a clean session yet
- Next session: Run docs/INSTALL.md verification in a clean Claude Code session, then script the demo video (before/after memory).
