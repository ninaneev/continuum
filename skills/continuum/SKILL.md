---
name: continuum
description: Use when starting a work session (recall memory), ending one (save memory), or whenever a durable fact is learned — a user preference, a correction, a decision with a reason, or a project state change. Also use when asked to "remember", "save this", "update memory", or "what do you know about me".
---

# Continuum — persistent memory workflow

Memory lives in the project's `memory/` directory: one fact per markdown
file, plus an index at `memory/MEMORY.md`. Cross-project facts about the
user may live in a global store (typically `~/claude-memory/`) with the
same format. This skill defines the exact workflow. Follow it precisely —
a memory store is only useful if every session maintains it the same way.

## 1. Recall — at session start

1. Read `memory/MEMORY.md`. It is one line per memory; cheap to load.
2. Open ONLY the entries whose hook is relevant to the current task. Do not
   bulk-read the whole directory.
3. If `BRIDGE.md` and `STATUS.md` exist at the project root, read both
   before touching code: BRIDGE for stable context, STATUS for what happened
   last session and what comes next.
4. Treat recalled facts as true until evidence contradicts them. If reality
   contradicts a memory, fix the memory (see §5).

## 2. Memory entry format

One fact per file, filename `<name>.md` matching the frontmatter slug:

```markdown
---
name: deploy-fridays-only
description: Production deploys happen Fridays only, after the weekly review
metadata:
  type: feedback
---

Production deploys go out on Fridays only, after the 10:00 weekly review.
Decided 2026-03-14 after a Tuesday deploy took checkout down during peak.

**Why:** mid-week incidents landed during the highest-traffic window.
**How to apply:** never suggest or run a production deploy Mon–Thu; queue
the change and note it in [[release-queue]] instead.
```

Rules:

- `name`: kebab-case slug, unique across the store, identical to filename.
- `description`: one line, concrete, no trailing period needed.
- `metadata.type`: exactly one of `user | feedback | project | reference`.
  - `user` — who the user is: role, timezone, working style, goals.
  - `feedback` — corrections and preferences ("never do X", "always Y").
  - `project` — current state of something being built.
  - `reference` — stable facts about tools, systems, external services.
- `feedback` and `project` entries MUST include a `**Why:**` line and a
  `**How to apply:**` line. A rule without a reason gets ignored; a fact
  without an application is trivia.
- Cross-link related entries with `[[name]]` wiki-links (the target's
  `name` slug). Every link must resolve to an existing file.
- Absolute dates always: write `2026-07-15`, never "today", "yesterday",
  or "last week". Relative dates are lies within a month.
- Keep the body under ~15 lines. If it needs more, it is probably two facts.

## 3. What to save

Save a fact when losing it would cost a future session real time or cause
a repeated mistake:

- Explicit user preferences and corrections — especially anything the user
  said twice, or said with irritation.
- Decisions WITH their reasons ("chose Postgres over SQLite because multi-
  writer"). The reason is the valuable half.
- Non-obvious constraints: "staging shares the prod database", "the client
  demo is 2026-08-01", "never touch the billing service".
- People and relationships: who reviews, who decides, who the client is.
- Project state that is not visible from the code: what was tried and
  rejected, what is blocked and on what.

## 4. What NOT to save

- Anything derivable from the code or git: file lists, function signatures,
  directory layout, commit history, current branch. It goes stale the
  moment it is written and the source of truth is one command away.
- Transient state: build output, today's error message, in-progress
  debugging theories.
- Secrets. Never store credentials, API keys, tokens, or connection strings
  — memory files are plain text and may be committed.
- Speculation or inference about the user that they did not state. Memory
  stores facts, not guesses.
- Generic knowledge you already have (how React works, what REST is).

## 5. Save — at session end, or the moment a durable fact appears

1. **Dedupe before create.** Grep `memory/` for 2–3 keywords from the fact.
   If a related entry exists, UPDATE that file — never create a near-
   duplicate. Duplicates are how stores start contradicting themselves.
2. **New fact** → new file per §2, in the right type.
3. **Changed fact** → edit the existing file in place. Replace the outdated
   sentence; do not append "UPDATE:" paragraphs.
4. **Wrong fact** → delete the file and remove its index line. Then grep the
   store for `[[its-name]]` and fix or remove those links.
5. **Update the index after EVERY write, edit, or delete** — same session,
   no exceptions. Index format, one line per memory:

   ```markdown
   - [Deploy Fridays only](deploy-fridays-only.md) — no prod deploys Mon–Thu, queue instead
   ```

   Title in brackets, filename in parens, then an em-dash and a hook: the
   one clause that tells a future session whether to open the file. Group
   lines by type if the store is large. An entry missing from the index
   does not exist.

## 6. Handoff — before ending a session that touched a project

1. Rewrite `STATUS.md`: what changed this session (with the date), exact
   next steps as an ordered list, open questions. Overwrite stale content;
   STATUS is volatile by design.
2. Touch `BRIDGE.md` only if a durable decision or constraint changed.
3. Templates for both live in the Continuum repo under `templates/`.

## 7. Hygiene

- If the index and the files disagree, the files win — rebuild the index.
- If the store exceeds ~40 entries or you notice duplicates/stale entries,
  recommend the user run the `memory-curator` agent.
- Never mention this skill's mechanics in ordinary conversation; just use
  the memory. The user should feel continuity, not machinery.
