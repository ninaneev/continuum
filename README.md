# Continuum

**Your AI forgets everything every night. Mine doesn't.**

Continuum is a persistent memory system for [Claude Code](https://claude.com/claude-code). It turns a stateless AI session into one that remembers who you are, what you're building, what you decided last Tuesday, and why.

No database. No vector store. No external service. Just markdown files you can read, edit, and version like everything else in your project.

## The problem

Every Claude Code session starts from zero. You re-explain your stack, your conventions, the decision you already litigated twice, the thing it must never touch. Ten minutes of context-setting before any real work — every single day. Worse: the corrections you gave yesterday ("stop writing passive voice", "never commit to main") evaporate overnight.

## What Continuum is

A file convention plus two small pieces of Claude Code machinery:

1. **A memory directory** — one fact per markdown file, with YAML frontmatter and wiki-style cross-links, plus a one-line-per-memory index (`MEMORY.md`) that loads at session start.
2. **A session protocol** — recall at the start of a session, save at the end. New facts become new files. Changed facts get updated. Wrong facts get deleted, not corrected-by-appending.
3. **A handoff layer** — `BRIDGE.md` (stable project context any agent can pick up) and `STATUS.md` (what changed this session, exact next steps). Sessions end clean; the next one starts warm.
4. **A skill** (`skills/continuum/SKILL.md`) that teaches Claude the exact memory workflow: what to save, what not to save, how to dedupe, how to keep the index honest.
5. **A curator agent** (`agents/memory-curator.md`) that periodically reviews the memory store: merges duplicates, prunes stale entries, fixes broken links, rewrites the index.

Everything is plain text. You can `cat` your AI's brain.

## 60-second quickstart

```bash
# 1. Install the skill (user-level, works in every project)
mkdir -p ~/.claude/skills/continuum
cp skills/continuum/SKILL.md ~/.claude/skills/continuum/

# 2. Install the curator agent
mkdir -p ~/.claude/agents
cp agents/memory-curator.md ~/.claude/agents/

# 3. Create a memory directory in your project
mkdir -p memory
cp templates/MEMORY.md memory/MEMORY.md
```

Then add this to your project's `CLAUDE.md`:

```markdown
# Memory
At session start, read memory/MEMORY.md and open any entries relevant
to the task. Save new durable facts before the session ends (see the
continuum skill). Keep BRIDGE.md and STATUS.md current at handoff.
```

Test it: tell Claude a durable fact ("I prefer tabs, and we deploy Fridays only"), end the session, start a new one, and ask "what do you know about my deploy schedule?" It answers from `memory/` — not from luck.

Full walkthrough in [docs/INSTALL.md](docs/INSTALL.md).

## Architecture

```
 ┌─────────────────────────────────────────────────────────┐
 │                     SESSION START                       │
 │  Claude reads memory/MEMORY.md (the index, ~30 lines)   │
 │  → pulls only the entries relevant to today's task      │
 └────────────────────────────┬────────────────────────────┘
                              │
 ┌────────────────────────────▼────────────────────────────┐
 │                        WORK                             │
 │  BRIDGE.md ── stable project context (rarely changes)   │
 │  STATUS.md ── what changed last session + next steps    │
 └────────────────────────────┬────────────────────────────┘
                              │
 ┌────────────────────────────▼────────────────────────────┐
 │                     SESSION END                         │
 │  new fact     → new file in memory/                     │
 │  changed fact → edit the existing file                  │
 │  wrong fact   → delete the file                         │
 │  always       → update MEMORY.md index + STATUS.md      │
 └────────────────────────────┬────────────────────────────┘
                              │        (periodically)
 ┌────────────────────────────▼────────────────────────────┐
 │                   memory-curator agent                  │
 │  merge duplicates · prune stale · fix [[links]] ·       │
 │  verify facts · rewrite index                           │
 └─────────────────────────────────────────────────────────┘
```

### The memory format

One fact per file. Small files stay true; big files rot.

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

Four types: `user` (who they are), `feedback` (corrections and preferences), `project` (state of a thing being built), `reference` (stable facts about tools and systems). Entries cross-link with `[[name]]` wiki-links.

### Repository layout

```
continuum/
├── skills/continuum/SKILL.md   # the memory workflow Claude follows
├── agents/memory-curator.md    # periodic memory review agent
├── templates/                  # memory-entry, MEMORY, BRIDGE, STATUS
├── examples/memory/            # a realistic example memory directory
├── docs/
│   ├── INSTALL.md              # step-by-step install + verification
│   └── HOW_TO_USE.md           # daily workflow, good vs bad entries
├── BRIDGE.md                   # handoff context for this repo itself
└── STATUS.md                   # current state of this repo
```

Continuum dogfoods itself: this repo carries its own `BRIDGE.md` and `STATUS.md`.

## FAQ

**Why files instead of a vector database?**
Because you can read files. A memory you can't inspect is a memory you can't trust — and can't fix when it's wrong. At the scale of one person's working context (tens to low hundreds of facts), an index that fits in one screen beats similarity search: no infrastructure, no embedding drift, no retrieval misses you can't debug.

**Doesn't this bloat the context window?**
No. Only the index (`MEMORY.md`, one line per memory) loads at session start. Individual entries load on demand, when relevant. A 50-memory store costs roughly 50 lines up front.

**What stops the memory from filling with junk?**
Three things: the skill's save rules (never store what's derivable from code or git), dedupe-before-create, and the memory-curator agent, which you run periodically to merge, prune, and verify.

**Does it work across projects?**
Yes. Keep a `memory/` directory per project for project facts, and optionally a global one (e.g. `~/claude-memory/`) referenced from `~/.claude/CLAUDE.md` for facts about *you* that apply everywhere.

**What about secrets?**
Never in memory. The skill forbids storing credentials, tokens, or keys. Memory files are plain text and may be committed — treat them like source code.

**Is this tied to Claude Code?**
The conventions are plain markdown, so any agent that can read files can use the store. The skill and curator agent are Claude Code-specific.

## License

MIT © 2026 Nina Cressoni. See [LICENSE](LICENSE).
