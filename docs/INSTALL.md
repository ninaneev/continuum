# Installing Continuum

Continuum is copy-in files: a skill, an agent, a memory directory, and one
block in your `CLAUDE.md`. No installer, no dependencies. Five minutes.

Commands below are POSIX shell. On Windows, use Git Bash, or translate to
PowerShell (`New-Item -ItemType Directory`, `Copy-Item`).

## 1. Install the skill

The skill teaches Claude the memory workflow: when to recall, what to save,
what never to save, how to keep the index honest.

**User-level** (recommended — applies to every project on your machine):

```bash
mkdir -p ~/.claude/skills/continuum
cp skills/continuum/SKILL.md ~/.claude/skills/continuum/SKILL.md
```

**Project-level** (applies to one project only, and travels with the repo):

```bash
mkdir -p .claude/skills/continuum
cp skills/continuum/SKILL.md .claude/skills/continuum/SKILL.md
```

If a skill named `continuum` exists at both levels, the project-level one
wins inside that project.

## 2. Install the curator agent

The agent is the maintenance crew: run it every few weeks to merge
duplicates, prune stale entries, and repair the index.

**User-level:**

```bash
mkdir -p ~/.claude/agents
cp agents/memory-curator.md ~/.claude/agents/memory-curator.md
```

**Project-level:**

```bash
mkdir -p .claude/agents
cp agents/memory-curator.md .claude/agents/memory-curator.md
```

## 3. Create the memory directory

In each project where you want persistent memory:

```bash
mkdir -p memory
cp templates/MEMORY.md memory/MEMORY.md
```

Open `memory/MEMORY.md` and delete the placeholder lines under each
heading — the index starts empty and Claude fills it as facts accumulate.

Optional: a global store for facts about *you* that apply everywhere
(`~/claude-memory/` with the same format), referenced from your user-level
`~/.claude/CLAUDE.md`.

## 4. Wire it into CLAUDE.md

Add this block to the project's `CLAUDE.md` (create the file if it does
not exist):

```markdown
# Memory
At session start, read memory/MEMORY.md and open any entries relevant
to the task. Save new durable facts before the session ends (see the
continuum skill). Keep BRIDGE.md and STATUS.md current at handoff.
```

Optionally seed the handoff layer now:

```bash
cp templates/BRIDGE.md BRIDGE.md
cp templates/STATUS.md STATUS.md
```

Fill in `BRIDGE.md` once by hand (or ask Claude to draft it from the
codebase) — it pays for itself the first time any session starts cold.

## 5. Verify it works

Start a **new** Claude Code session in the project and run this exchange:

1. Say:

   > Remember this: I'm the only developer on this project, I work
   > from GMT-3, and we never deploy on Fridays.

   Expected: Claude creates one or two files under `memory/` (a `user`
   entry and a `feedback` entry) and adds matching lines to
   `memory/MEMORY.md`. Check the directory — the files should exist, with
   valid frontmatter and, on the feedback entry, **Why:** and **How to
   apply:** lines.

2. End the session. Start another new session and ask:

   > What do you know about my deploy schedule?

   Expected: Claude answers "no deploys on Fridays" citing the memory —
   without you re-explaining anything.

3. Run the curator once to confirm the agent is wired up:

   > Use the memory-curator agent to review my memory directory.

   Expected: a short report (with a store this small, mostly "nothing to
   change").

If step 1 produced no files, check that the skill file is at the exact
path from step 1 and that its frontmatter still starts with
`name: continuum`. If step 2 failed, check the CLAUDE.md block from
step 4 exists in the project you tested.

## Uninstall

Delete the skill directory, the agent file, and the CLAUDE.md block. Your
`memory/` directory is plain markdown — keep it, it is yours.
