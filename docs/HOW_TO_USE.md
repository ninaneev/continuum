# Using Continuum day to day

Install once ([INSTALL.md](INSTALL.md)), then the system mostly runs itself.
This page covers the daily rhythm, what a good memory looks like, and the
handoff ritual that makes tomorrow's session start warm.

## The daily rhythm

**Session start.** Say what you're working on. Claude reads
`memory/MEMORY.md` (one line per memory), opens only the relevant entries,
and — if they exist — `BRIDGE.md` and `STATUS.md`. You should not need to
re-explain anything you've said before. If you do, that's a bug in the
store: tell Claude to save it this time.

**During the session.** Work normally. When something durable comes up — a
preference, a decision, a constraint — you can say "remember this", but
the skill also instructs Claude to notice on its own. Corrections are the
highest-value saves: if you catch yourself telling Claude the same thing a
second time, make sure it lands in memory.

**Session end.** Two words: "wrap up" (or "handoff"). Claude saves new
facts, updates changed ones, deletes wrong ones, refreshes the index, and
rewrites `STATUS.md`. Thirty seconds that saves ten minutes tomorrow.

**Every few weeks.** Run the curator:

> Use the memory-curator agent to review my memory directory.

It merges duplicates, prunes stale entries, fixes broken `[[links]]`,
verifies checkable facts against the codebase, and rebuilds the index.

## Good vs bad memory entries

**Good** — a decision with its reason, dated, actionable:

```markdown
---
name: postgres-over-sqlite
description: Chose Postgres over SQLite on 2026-06-02 because of multi-writer needs
metadata:
  type: project
---

Chose Postgres over SQLite on 2026-06-02, despite SQLite being simpler to
ship, because the worker and the web app both write concurrently.

**Why:** SQLite's single-writer lock caused corruption in the 2026-05-30
load test.
**How to apply:** don't suggest "simplify to SQLite" — it was tried and
measured. Schema lives in [[db-conventions]].
```

Why it works: absolute date, the reason is recorded (so the decision won't
be relitigated), and "How to apply" changes future behavior.

**Bad** — derivable from the code, undated, no reason:

```markdown
---
name: api-notes
description: Notes about the API
metadata:
  type: project
---

The API has routes in src/routes. There are about 12 endpoints. We use
Express. Recently refactored the auth middleware. TODO: clean up later.
```

Everything here is one `ls` or `git log` away, "recently" means nothing in
a month, "api-notes" is a junk-drawer name, and nothing tells a future
session what to *do*. Entries like this are why memory stores die — the
curator would flag it for deletion.

**Rules of thumb**

- One fact per file. If the body needs a second topic, it's a second file.
- Save the *reason*, not just the conclusion.
- Absolute dates only. "Last week" is a lie by August.
- Never save what code or git already knows.
- Never save secrets. Ever.
- Update or delete; never append "UPDATE:" paragraphs to a fact.

## The handoff ritual

Continuum treats session endings as a first-class event, because the gap
between sessions is where context dies. Two files carry it across:

**BRIDGE.md** — the stable layer. What the project is, decisions made and
*why*, key files, hard constraints. It changes rarely; when a session
relitigates a settled decision, the fix is a better BRIDGE entry.

**STATUS.md** — the volatile layer. Rewritten wholesale at the end of
every session: what changed (dated), the exact next steps in order, open
questions with who or what they're waiting on. The test of a good
STATUS.md: could a session with zero conversation history read it and
start productive work in under a minute?

The ritual, end of every working session:

1. "Wrap up."
2. Claude updates memory + index, rewrites STATUS.md, touches BRIDGE.md
   only if a decision changed.
3. Skim the diff — ten seconds. You are the editor of your own memory;
   that skim is what keeps the store trustworthy.

Next morning: open a session, say "pick up where we left off." Claude
reads STATUS.md and starts on step 1 of your own next-steps list.

## When memory is wrong

Just say so: "that's outdated, we moved to Fly.io in June." The skill's
rule is delete-or-edit, never append — the wrong fact is removed, the
index updated, and any `[[links]]` pointing at it repaired. A memory
store you can correct in one sentence is the whole point of keeping it
in plain files.
