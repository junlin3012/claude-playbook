# Understanding Skills in Claude Code

## What Is a Skill?

A skill is a markdown file that changes how Claude behaves. Think of it as a playbook — when a specific situation comes up, Claude reads the playbook and follows its instructions instead of winging it.

There's no code, no compilation, no API. Just a markdown file called `SKILL.md` inside a folder:

```
my-skill/
└── SKILL.md    ← Claude reads this and follows it
```

When Claude loads a skill, its content gets injected into the conversation — as if you typed it yourself. Claude then follows those instructions for the rest of the task.

Skills can be simple ("always write tests before code") or sophisticated ("run 10-round iterative review batches, compose three other skills, track a clean counter, and only exit after 5 consecutive clean batches"). The format is the same either way: a markdown file with instructions.

## Why Skills Matter — A Real Story

We were building [Fluid Intelligence](https://github.com/junlin3012/fluid-intelligence), a multi-process container running 5 services on Google Cloud Run. The codebase was bash scripts, GraphQL queries, Dockerfiles, and YAML configs.

We did code reviews. Found bugs. Fixed them. Did more reviews. Found more bugs. After a few rounds we thought it was clean.

It wasn't.

So we created a skill called **Mirror Polish Protocol** — an iterative review process that refuses to stop. Each batch brainstorms 10 fresh review angles (mixing simple checks like "are comments accurate?" with complex ones like "can a SIGTERM during bootstrap cause a PID leak?"). Each finding gets verified before fixing. Each fix gets a failing test first. And the critical rule: **any fix resets the clean counter to zero.**

The protocol only exits after 5 consecutive clean batches — 50 review rounds with zero bugs found.

Here's what happened:

```
Batch:   7    8    9   10   11   12   13   14   15   16   17   18   19   20   21
Fixes:   6    6    4    6    1    0    0    2    1    1    0    0    0    0    0
Clean:   -    -    -    -    -   1/5  2/5   0    0    0   1/5  2/5  3/5  4/5  5/5 ✓
```

21 batches. 201+ review rounds. **82 bugs found.** 176 unit tests created. The fix count declined naturally — 6, 6, 4, 6, 1, 0, 0 — then spiked when review angles shifted from code logic to build infrastructure (unpinned Docker dependencies, runtime `@latest` tags). Then declined again to zero.

That skill — one markdown file — turned a "looks clean to me" codebase into a genuinely hardened one. And because it composed three existing skills (brainstorming for angle invention, systematic debugging for verification, TDD for fixing), it didn't need to reinvent any of those processes.

That's the power of skills.

---

## Where Skills Live

Claude Code has a layered file system. Understanding where things sit is the key to using it effectively.

### The Three Layers

Claude reads instructions from three places, stacked on top of each other:

```
╔═══════════════════════════════════════════════════════════╗
║                                                           ║
║   GLOBAL         ~/.claude/                               ║
║                                                           ║
║   Everything here applies to ALL your projects.           ║
║   Your personal skills, plugin skills, master             ║
║   instructions, global memories, settings.                ║
║                                                           ║
║   This is where YOUR skills live.                         ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║   PROJECT        your-repo/CLAUDE.md                      ║
║                                                           ║
║   Applies to THIS repository only.                        ║
║   Project architecture, team conventions,                 ║
║   required reading. Checked into git.                     ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║   SESSION        (this conversation)                      ║
║                                                           ║
║   Your messages, tool results, loaded skill content,      ║
║   todo list. Dies when the conversation ends.             ║
║                                                           ║
╚═══════════════════════════════════════════════════════════╝

Priority:   SESSION  >  PROJECT  >  GLOBAL
            (more specific always wins)
```

### The Three Sources of Skills

Skills come from three places. They are all just markdown files, but they're managed differently:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   YOUR PERSONAL SKILLS                                              │
│   ~/.claude/skills/                                                 │
│                                                                     │
│   • You create, edit, and delete these                              │
│   • Available to ALL your projects                                  │
│   • Plain markdown files you fully control                          │
│   • Survive forever (until you delete them)                         │
│                                                                     │
│   Example: mirror-polish-protocol                                   │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   PLUGIN SKILLS                                                     │
│   ~/.claude/plugins/cache/                                          │
│                                                                     │
│   • Installed via plugins (e.g. "superpowers" has 15 skills)        │
│   • Community or plugin authors maintain them                       │
│   • Updated when the plugin updates                                 │
│   • You don't edit these directly                                   │
│                                                                     │
│   Examples: brainstorming, systematic-debugging, TDD                │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   PROJECT INSTRUCTIONS                                              │
│   your-repo/CLAUDE.md                                               │
│                                                                     │
│   • Per-repo rules and context (not technically "skills")           │
│   • Checked into git — your team shares them                        │
│   • Only apply when working in THAT project                         │
│                                                                     │
│   Example: "This is a Shopify app. Use API version 2026-01."        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### The File Tree

Here's where everything actually sits on your computer. This is a real file tree — these paths exist on disk and you can browse them in Finder or terminal:

```
/Users/you/
│
├── .claude/                                          GLOBAL
│   │
│   ├── CLAUDE.md                                     Your master instructions.
│   │                                                 Loaded into EVERY conversation.
│   │                                                 This is where you register skills
│   │                                                 and tell Claude when to use them.
│   │
│   ├── skills/                                       YOUR PERSONAL SKILLS
│   │   │
│   │   └── mirror-polish-protocol/
│   │       └── SKILL.md  ◀─────────────────────────  The skill we created.
│   │                                                  You own this file.
│   │                                                  Edit it anytime.
│   │
│   ├── plugins/
│   │   └── cache/
│   │       └── claude-plugins-official/
│   │           │
│   │           ├── superpowers/5.0.2/
│   │           │   └── skills/                       PLUGIN SKILLS (15 total)
│   │           │       ├── brainstorming/SKILL.md
│   │           │       ├── systematic-debugging/SKILL.md
│   │           │       ├── test-driven-development/SKILL.md
│   │           │       ├── writing-plans/SKILL.md
│   │           │       ├── executing-plans/SKILL.md
│   │           │       ├── verification-before-completion/SKILL.md
│   │           │       ├── requesting-code-review/SKILL.md
│   │           │       ├── dispatching-parallel-agents/SKILL.md
│   │           │       └── ... (7 more)
│   │           │
│   │           └── frontend-design/1.0.0/
│   │               └── skills/
│   │                   └── frontend-design/SKILL.md  PLUGIN SKILL (1)
│   │
│   ├── memory/                                       Global memories
│   ├── settings.json                                 Permissions, model prefs
│   └── mcp.json                                      MCP server connections
│
│
└── Projects/
    └── my-project/                                   YOUR PROJECT
        │
        ├── CLAUDE.md  ◀────────────────────────────  Project-specific instructions.
        │                                              Only applies in this repo.
        │                                              Checked into git.
        ├── src/
        └── docs/
```

### How Claude Finds and Loads a Skill

This is the exact sequence that happens when you trigger a skill:

```
Step 1    You say: "Do an exhaustive code review"
           │
           │
Step 2    Claude reads ~/.claude/CLAUDE.md
           (this is loaded at conversation start, always)
           │
           │  Sees the skills table and trigger rules:
           │  "Exhaustive code review? → mirror-polish-protocol"
           │
           │
Step 3    Claude searches for the skill file:
           │
           │  First:   ~/.claude/skills/mirror-polish-protocol/SKILL.md
           │           ↳ FOUND ✓
           │
           │  Second:  ~/.claude/plugins/cache/.../skills/
           │           (only checked if not found above)
           │
           │
Step 4    Claude reads the SKILL.md content
           │
           │  The full markdown is injected into the conversation
           │  as if you had typed it yourself.
           │
           │
Step 5    Claude follows the instructions in the skill
           │
           │  For Mirror Polish: brainstorm angles → review →
           │  verify with systematic debugging → TDD fix →
           │  write report → check counter → repeat or exit
```

### How Skills Compose Other Skills

One skill can reference and use other skills. This is what makes the system powerful — small focused skills combine into sophisticated workflows:

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   Mirror Polish Protocol                                │
│   ~/.claude/skills/ (YOUR personal skill)               │
│                                                         │
│   Orchestrates the overall process:                     │
│   batch loop, clean counter, report writing,            │
│   exclusion list, exit condition                        │
│                                                         │
└────────────┬──────────────┬──────────────┬──────────────┘
             │              │              │
         uses ▼          uses ▼          uses ▼
┌────────────────┐ ┌────────────────┐ ┌────────────────┐
│  Brainstorming │ │   Systematic   │ │  Test-Driven   │
│                │ │   Debugging    │ │  Development   │
│  Invents 10    │ │                │ │                │
│  review angles │ │  Verifies      │ │  Failing test  │
│  per batch     │ │  findings      │ │  → fix →       │
│                │ │  before fixing │ │  verify        │
└────────────────┘ └────────────────┘ └────────────────┘
    PLUGIN SKILL       PLUGIN SKILL       PLUGIN SKILL
    (superpowers)      (superpowers)      (superpowers)
```

Your skill is the conductor. The plugin skills are the musicians. Each does one thing well. Together they produce something none could do alone.

---

## What Skills Can Be Helpful For

Skills aren't just for code review. Anything you find yourself repeating or wishing Claude would "just know how to do" is a candidate:

### Development Workflows
- **Mirror Polish Protocol** — exhaustive iterative review (what we built)
- **Feature development** — brainstorm → design → plan → implement → review
- **Bug investigation** — systematic root cause analysis before fixing
- **Deployment checklists** — pre-deploy verification steps

### Code Quality
- **Test-Driven Development** — enforce RED → GREEN → REFACTOR
- **Code review standards** — what to check, how to verify, what severity levels mean
- **Documentation standards** — when to document, what format, where to put it

### Team Patterns
- **Onboarding** — "Here's how this project works, here's what to read first"
- **Architecture decisions** — "We use X because Y, never do Z"
- **Incident response** — "When something breaks, check these things in this order"

### Personal Productivity
- **Writing style** — "Be terse, no emojis, lead with the answer"
- **Commit conventions** — "Use conventional commits, always include Co-Authored-By"
- **Research patterns** — "When exploring a topic, check these sources first"

The common thread: if you've corrected Claude more than once about the same thing, that correction should be a skill (or at minimum, a line in CLAUDE.md). Skills turn one-time corrections into permanent behavior.

---

## Quick Reference

| What | Where | Who Controls It |
|------|-------|-----------------|
| Global instructions | `~/.claude/CLAUDE.md` | You |
| Your personal skills | `~/.claude/skills/<name>/SKILL.md` | You |
| Plugin skills | `~/.claude/plugins/cache/.../skills/` | Plugin authors |
| Project instructions | `your-repo/CLAUDE.md` | You + your team |
| Project memories | `~/.claude/projects/<hash>/memory/` | Claude (you can edit) |
| Settings | `~/.claude/settings.json` | You |

| Action | How |
|--------|-----|
| See your skills | `ls ~/.claude/skills/` |
| Edit a skill | Open `~/.claude/skills/<name>/SKILL.md` in any editor |
| Create a new skill | `mkdir ~/.claude/skills/my-skill` then create `SKILL.md` inside |
| Delete a skill | `rm -rf ~/.claude/skills/<name>/` |
| Register a skill | Add it to the skills table in `~/.claude/CLAUDE.md` |
| Share a skill | Copy the folder — it's just files |

---

*Everything is plain markdown files on your computer. You own them. Claude reads them. Edit a file, and Claude's behavior changes next conversation. There is no cloud, no lock-in, no magic.*
