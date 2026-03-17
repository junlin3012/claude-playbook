# Migration Document — Old Architecture → New Architecture

**Date**: 2026-03-17
**Purpose**: Complete mapping of every file, path, reference, and dependency that changes during migration. Zero ambiguity.

---

## 1. Architecture Comparison

### Old Architecture (current)

```
~/.claude/
├── CLAUDE.md                              138 lines (bloated, mixed concerns)
├── Consciousness/
│   ├── INDEX.md                           Points to PURPOSE.md + agent_philosophy.md
│   ├── PURPOSE.md                         Why consciousness exists
│   └── agent_philosophy.md                Has WRONG frontmatter (type: feedback)
├── memory/
│   ├── MEMORY.md                          Index (will have dead link post-migration)
│   ├── convention_file_organization.md    DUPLICATE of CLAUDE.md content
│   ├── feedback_use_plugins.md            Behavioral feedback
│   ├── user_plan.md                       Subscription info
│   └── reference_claude_playbook.md       STALE description
├── settings.json                          Permissions + no hooks
├── plugins/
│   ├── cache/claude-plugins-official/     16 marketplace plugins (auto-managed)
│   └── local/
│       ├── junlin-custom-skills/          3 skills in plugin wrapper (unnecessary)
│       │   ├── .claude-plugin/plugin.json
│       │   └── skills/
│       │       ├── mirror-polish-protocol/SKILL.md
│       │       ├── knowledge-distillation/SKILL.md
│       │       └── custom-plugins/SKILL.md     HARDCODED path to plugins/local/
│       └── business-ops/                  1 skill + procedures (no git remote!)
│           ├── .claude-plugin/plugin.json
│           ├── skills/shopify-ops/SKILL.md     HARDCODED path to plugins/local/
│           └── procedures/shopify/split-draft-order.md
├── rules/                                 DOES NOT EXIST
├── skills/                                DOES NOT EXIST
├── agents/                                DOES NOT EXIST
└── (sessions, cache, debug, etc.)         Auto-managed

~/.zshrc alias: claude --plugin-dir ~/.claude/plugins/local/junlin-custom-skills --plugin-dir ~/.claude/plugins/local/business-ops
```

### New Architecture (target)

```
~/.claude/
├── CLAUDE.md                              40 lines (bootstrap only)
├── Consciousness/
│   ├── INDEX.md                           Enhanced with layer descriptions
│   ├── L0-identity.md                     Renamed from PURPOSE.md, enhanced
│   ├── L1-cognition.md                    Renamed from agent_philosophy.md, NO frontmatter
│   ├── L2-principles.md                   NEW — hard-won lessons, security
│   ├── L3-modes.md                        NEW — extracted from agent_philosophy.md
│   └── L4-evolution.md                    NEW — how consciousness grows
├── rules/
│   ├── behavioral/
│   │   ├── triangulate-facts.md           Extracted from CLAUDE.md
│   │   ├── memory-isolation.md            Extracted from CLAUDE.md
│   │   ├── mandatory-skills.md            NEW — Jesse Vincent pattern
│   │   └── security.md                    NEW — security hardening
│   ├── conventions/
│   │   ├── file-organization.md           Moved from CLAUDE.md + memory
│   │   ├── plugin-structure.md            Moved from CLAUDE.md (fixed user-invocable hyphen)
│   │   └── cost-estimate.md               Extracted from CLAUDE.md
│   └── meta/
│       ├── consolidation.md               NEW — memory → rules promotion
│       ├── forgetting.md                  NEW — stale knowledge pruning
│       └── self-improvement.md            NEW — correction → rule pipeline
├── skills/
│   ├── mirror-polish-protocol/SKILL.md    Moved from plugin (unchanged content)
│   ├── knowledge-distillation/SKILL.md    Moved from plugin (unchanged content)
│   ├── custom-plugins/SKILL.md            Moved + REWRITTEN (new search path)
│   └── shopify-ops/
│       ├── SKILL.md                       Moved + REWRITTEN (${CLAUDE_SKILL_DIR} paths)
│       └── procedures/
│           └── split-draft-order.md       Moved from business-ops plugin
├── agents/
│   └── consolidator.md                    NEW — memory review subagent
├── memory/
│   ├── MEMORY.md                          Updated (dead link removed)
│   ├── feedback_use_plugins.md            Unchanged
│   ├── user_plan.md                       Unchanged
│   └── reference_claude_playbook.md       Updated description
├── settings.json                          Hooks MERGED in (not replaced)
├── plugins/
│   ├── cache/claude-plugins-official/     Unchanged (auto-managed)
│   └── local/                             EMPTY (skills moved out, ready for future plugins)
└── (sessions, cache, debug, etc.)         Unchanged (auto-managed)

~/.zshrc alias: REMOVED (no --plugin-dir needed, skills auto-discovered)
```

---

## 2. File-by-File Migration Table

### Files That MOVE (content preserved, location changes)

| Old Location | New Location | Content Changes |
|---|---|---|
| `Consciousness/PURPOSE.md` | `Consciousness/L0-identity.md` | Rename. Enhance with core values section. |
| `Consciousness/agent_philosophy.md` | `Consciousness/L1-cognition.md` | Rename. Remove wrong frontmatter. Extract mode awareness → L3. |
| `plugins/local/junlin-custom-skills/skills/mirror-polish-protocol/SKILL.md` | `skills/mirror-polish-protocol/SKILL.md` | No content change. |
| `plugins/local/junlin-custom-skills/skills/knowledge-distillation/SKILL.md` | `skills/knowledge-distillation/SKILL.md` | No content change. |
| `plugins/local/junlin-custom-skills/skills/custom-plugins/SKILL.md` | `skills/custom-plugins/SKILL.md` | **FULL REWRITE**: (1) Change search path to `~/.claude/skills/*/SKILL.md`, (2) Remove all references to "junlin-custom-skills plugin", (3) Update description — now enumerates standalone skills not plugin skills, (4) Update invoke syntax from `/junlin-custom-skills:custom-plugins` to `/custom-plugins`, (5) Fix `user_invocable` → `user-invocable` in frontmatter |
| `plugins/local/business-ops/skills/shopify-ops/SKILL.md` | `skills/shopify-ops/SKILL.md` | **REWRITE**: (1) Change procedure path to `${CLAUDE_SKILL_DIR}/procedures/split-draft-order.md`, (2) Fix `user_invocable` → `user-invocable` in frontmatter, (3) Note: `${CLAUDE_SKILL_DIR}` only works in skill body text, not in hooks or frontmatter |
| `plugins/local/business-ops/procedures/shopify/split-draft-order.md` | `skills/shopify-ops/procedures/split-draft-order.md` | No content change. |

### Files That Are EXTRACTED (content split from existing files)

| Source | New File | What's Extracted |
|---|---|---|
| `CLAUDE.md` lines 115-124 | `rules/behavioral/triangulate-facts.md` | "Never guess" rule + origin story |
| `CLAUDE.md` lines 107-113 | `rules/behavioral/memory-isolation.md` | Memory isolation from ~ rule |
| `CLAUDE.md` lines 126-138 | `rules/conventions/cost-estimate.md` | Cost footer format + plan info |
| `CLAUDE.md` lines 9-38 | (DELETED — not extracted) | Skill registry table — redundant, Claude auto-discovers skills |
| `CLAUDE.md` lines 60-73 | `rules/conventions/file-organization.md` | Directory conventions table |
| `CLAUDE.md` lines 75-96 | `rules/conventions/plugin-structure.md` | Plugin structure rules (with `user-invocable` FIXED to hyphen) |
| `agent_philosophy.md` lines 115-125 | `Consciousness/L3-modes.md` | Mode awareness table (POC/Integration/Recreation) |

### Files That Are CREATED (new content)

| New File | Purpose | Inspired By |
|---|---|---|
| `Consciousness/L2-principles.md` | Hard-won lessons, security principles, decision frameworks | Starlight L2 |
| `Consciousness/L4-evolution.md` | How consciousness grows + acceptance criteria | ScallopBot + must reference `rules/meta/consolidation.md` |
| `rules/behavioral/mandatory-skills.md` | Must check skills before any task | Jesse Vincent |
| `rules/behavioral/security.md` | Security hardening rules | Session research |
| `rules/meta/consolidation.md` | Memory → rules promotion criteria | Mizoreww + must reference `Consciousness/L4-evolution.md` |
| `rules/meta/forgetting.md` | Stale knowledge pruning criteria | FadeMem |
| `rules/meta/self-improvement.md` | Correction → permanent rule pipeline | Mizoreww |
| `agents/consolidator.md` | Memory review subagent | CoALA + ScallopBot |

### Files That Are DELETED

| File | Why |
|---|---|
| `memory/convention_file_organization.md` | Duplicate of CLAUDE.md content. Moved to `rules/conventions/file-organization.md`. |
| `plugins/local/junlin-custom-skills/` (entire directory) | Skills moved to `skills/`. Plugin wrapper no longer needed. |
| `plugins/local/business-ops/` (entire directory) | Skills + procedures moved to `skills/shopify-ops/`. |
| `Consciousness/PURPOSE.md` | Renamed to `L0-identity.md`. |
| `Consciousness/agent_philosophy.md` | Renamed to `L1-cognition.md`. Mode section extracted to `L3-modes.md`. |

### Files That Are UPDATED (in place)

| File | What Changes |
|---|---|
| `CLAUDE.md` | **REWRITTEN** from 138 → 40 lines. Bootstrap only. |
| `Consciousness/INDEX.md` | **REWRITTEN** with 5-layer descriptions and when-to-read guidance. |
| `memory/MEMORY.md` | Remove dead link to deleted `convention_file_organization.md`. |
| `memory/reference_claude_playbook.md` | Update description: remove "skills/ (staging area)", add "lab + docs only". |
| `settings.json` | **MERGE** hooks in (SessionStart, ~~PostCompact~~ (REMOVED — unnecessary, CLAUDE.md survives compaction natively)). Do not replace existing permissions. |
| `~/.zshrc` | **REMOVE** `--plugin-dir` flags from claude alias. |

### Files That Are UNCHANGED

| File | Why |
|---|---|
| `memory/feedback_use_plugins.md` | Still valid behavioral feedback. |
| `memory/feedback_no_hardcoded_values.md` | Still valid. Zero tolerance for hardcoded business values. |
| `memory/user_plan.md` | Still valid user fact. |
| `plugins/cache/` (entire directory) | Auto-managed marketplace plugins. Not touched. |
| `~/.claude.json` | MCP servers, OAuth. Not touched. |
| `sessions/`, `debug/`, `backups/`, `plans/` | Auto-managed. Not touched. |

---

## 3. Dependency Map — Every Cross-Reference That Changes

### Pointer: CLAUDE.md → Consciousness

| Old | New | Status |
|---|---|---|
| `CLAUDE.md:5` → `~/.claude/Consciousness/` | `CLAUDE.md` → `Consciousness/` | ✅ Path unchanged (relative) |
| `CLAUDE.md:7` → `Consciousness/INDEX.md` | `CLAUDE.md` → `Consciousness/INDEX.md` | ✅ Path unchanged |

### Pointer: INDEX.md → Layer Files

| Old | New | Status |
|---|---|---|
| `INDEX.md:3` → `PURPOSE.md` | `INDEX.md` → `L0-identity.md` | ⚠️ MUST UPDATE INDEX.md |
| `INDEX.md:9` → `PURPOSE.md` | `INDEX.md` → `L0-identity.md` | ⚠️ MUST UPDATE INDEX.md |
| `INDEX.md:10` → `agent_philosophy.md` | `INDEX.md` → `L1-cognition.md` | ⚠️ MUST UPDATE INDEX.md |
| (doesn't exist) | `INDEX.md` → `L2-principles.md` | ⚠️ MUST ADD to INDEX.md |
| (doesn't exist) | `INDEX.md` → `L3-modes.md` | ⚠️ MUST ADD to INDEX.md |
| (doesn't exist) | `INDEX.md` → `L4-evolution.md` | ⚠️ MUST ADD to INDEX.md |

### Pointer: PURPOSE.md ↔ INDEX.md (bidirectional)

| Old | New | Status |
|---|---|---|
| `PURPOSE.md:20` → `INDEX.md` | `L0-identity.md` → `INDEX.md` | ✅ Valid if internal ref updated |
| `PURPOSE.md:23` → "update INDEX.md" | `L0-identity.md` → "update INDEX.md" | ✅ Valid |

### Pointer: MEMORY.md → Memory Files

| Old | New | Status |
|---|---|---|
| `MEMORY.md:3` → `feedback_use_plugins.md` | Same | ✅ Unchanged |
| `MEMORY.md:4` → `convention_file_organization.md` | DELETED | ❌ DEAD LINK — remove entry |
| `MEMORY.md:5` → `user_plan.md` | Same | ✅ Unchanged |
| `MEMORY.md:6` → `reference_claude_playbook.md` | Same | ✅ Unchanged (but update description) |

### Pointer: Skills → Supporting Files

| Old | New | Status |
|---|---|---|
| `custom-plugins/SKILL.md:13` → `~/.claude/plugins/local/junlin-custom-skills/skills/*/SKILL.md` | → `~/.claude/skills/*/SKILL.md` | ❌ MUST REWRITE |
| `custom-plugins/SKILL.md:19` → `~/.claude/plugins/local/junlin-custom-skills/` | → `~/.claude/skills/` | ❌ MUST REWRITE |
| `shopify-ops/SKILL.md:13` → `~/.claude/plugins/local/business-ops/procedures/shopify/split-draft-order.md` | → `${CLAUDE_SKILL_DIR}/procedures/split-draft-order.md` | ❌ MUST REWRITE |

### Pointer: Consciousness ↔ Rules (bidirectional — NEW)

| From | To | Status |
|---|---|---|
| `L4-evolution.md` | `rules/meta/consolidation.md` | ⚠️ MUST CREATE with reference |
| `rules/meta/consolidation.md` | `Consciousness/L4-evolution.md` | ⚠️ MUST CREATE with reference |

### Pointer: CLAUDE.md → Skills/Plugins (all REMOVED)

| Old Reference | Action |
|---|---|
| `CLAUDE.md:22` → `junlin-custom-skills (~/.claude/plugins/local/)` | REMOVED — not in new CLAUDE.md |
| `CLAUDE.md:23` → `business-ops (~/.claude/plugins/local/)` | REMOVED — not in new CLAUDE.md |
| `CLAUDE.md:42` → `junlin3012/junlin-claude-skills (private)` | REMOVED — repo archived |
| `CLAUDE.md:49` → `git clone ... ~/.claude/plugins/local/junlin-custom-skills` | REMOVED — no longer a plugin |
| `CLAUDE.md:66-71` → File organization table with plugin paths | MOVED to `rules/conventions/file-organization.md` with UPDATED paths |
| `CLAUDE.md:103` → Shell alias with `--plugin-dir` | REMOVED — alias no longer needed |

### Pointer: ~/.zshrc → Plugin Dirs

| Old | New | Status |
|---|---|---|
| `alias claude='command claude --plugin-dir ... --plugin-dir ...'` | `alias claude='command claude'` (or remove alias) | ⚠️ MUST UPDATE ~/.zshrc |

### Pointer: settings.json → Hooks (NEW)

| Old | New | Status |
|---|---|---|
| (no hooks) | `hooks.SessionStart` → consciousness-check script | ⚠️ MUST MERGE into settings.json |
| (no hooks) | `hooks.~~PostCompact~~ (REMOVED — unnecessary, CLAUDE.md survives compaction natively)` → reinject-identity script | ⚠️ MUST MERGE into settings.json |

### Invocation Syntax Change (affects all migrated skills)

| Old (plugin) | New (standalone) | Impact |
|---|---|---|
| `/junlin-custom-skills:mirror-polish-protocol` | `/mirror-polish-protocol` | Shorter. Any docs/memories referencing old syntax need updating. |
| `/junlin-custom-skills:knowledge-distillation` | `/knowledge-distillation` | Same. |
| `/junlin-custom-skills:custom-plugins` | `/custom-plugins` | Same. |
| `/business-ops:shopify-ops` | `/shopify-ops` | Same. |

**Note**: If a marketplace plugin has a skill with the same name, the standalone skill takes precedence (enterprise > personal > project per official docs).

---

## 4. deploy.sh Critical Requirements

From Mirror Polish review — these must be in deploy.sh:

| # | Requirement | Why |
|---|---|---|
| 1 | `mkdir -p ~/.claude` at start | `~/.claude/` only exists after first `claude` run. Fresh machine won't have it. |
| 2 | Remove old plugin dirs after copying skills | `plugins/local/junlin-custom-skills/` and `plugins/local/business-ops/` will conflict with standalone skills if left in place. |
| 3 | Hook merge strategy: use `jq` to merge hooks into existing settings.json | Cannot replace settings.json — it has permissions, tool usage stats, etc. Must surgically add hooks object. |
| 4 | Validate after deploy | Run the 18-point validation checklist automatically. |

---

## 5. Hardcoded Path Audit

**Rule**: No absolute paths inside skills, agents, or rules. Use variables or relative paths.

| File | Hardcoded Path | Fix |
|---|---|---|
| `custom-plugins/SKILL.md:13` | `~/.claude/plugins/local/junlin-custom-skills/skills/*/SKILL.md` | `~/.claude/skills/*/SKILL.md` (still absolute but necessary for glob — skill searches outside its own dir) |
| `custom-plugins/SKILL.md:19` | `~/.claude/plugins/local/junlin-custom-skills/` | `~/.claude/skills/` |
| `shopify-ops/SKILL.md:13` | `~/.claude/plugins/local/business-ops/procedures/shopify/split-draft-order.md` | `${CLAUDE_SKILL_DIR}/procedures/split-draft-order.md` |
| `split-draft-order.md:14` | `https://admin.shopify.com/store/junlinleather-5148/draft_orders` | Keep — this is an external URL, not a local path. Correct to hardcode. |

**Note on custom-plugins/SKILL.md**: This skill enumerates ALL skills by globbing `~/.claude/skills/`. It has to use `~/.claude/` because it searches outside its own directory. This is acceptable — it's the user's known config root. But it should NOT hardcode a plugin subpath.

**Note on `${CLAUDE_SKILL_DIR}`**: This variable resolves ONLY in the skill body text (markdown content). It does NOT resolve in frontmatter fields or hooks defined in frontmatter. If a skill needs to reference its own files in a hook, use a relative path from the project root instead.

### Bidirectional References (must be created during implementation)

| File A | File B | A must reference B | B must reference A |
|---|---|---|---|
| `Consciousness/L4-evolution.md` | `rules/meta/consolidation.md` | "See consolidation.md for the promotion criteria" | "See L4-evolution.md for acceptance criteria" |
| `rules/meta/consolidation.md` | `agents/consolidator.md` | "Use the consolidator agent to automate review" | "This agent implements the consolidation rule" |
| `rules/meta/self-improvement.md` | `rules/meta/consolidation.md` | "Corrections that qualify get promoted via consolidation" | "Self-improvement identifies candidates for promotion" |

---

## 5. Data That Must NOT Be Lost

| Data | Current Location | Destination | Risk |
|---|---|---|---|
| Relational thinking philosophy | `agent_philosophy.md` (166 lines) | `L1-cognition.md` + `L3-modes.md` | HIGH — must preserve every line, just split |
| Why consciousness exists | `PURPOSE.md` (38 lines) | `L0-identity.md` | HIGH — must preserve every line, enhance |
| Split draft order procedure | `procedures/shopify/split-draft-order.md` (54 lines) | `skills/shopify-ops/procedures/split-draft-order.md` | HIGH — business procedure, must be exact |
| User subscription info | `memory/user_plan.md` | Same (unchanged) | LOW — stays in place |
| Mirror polish protocol | `mirror-polish-protocol/SKILL.md` (629 lines) | `skills/mirror-polish-protocol/SKILL.md` | HIGH — largest file, must be complete |
| Knowledge distillation | `knowledge-distillation/SKILL.md` (543 lines) | `skills/knowledge-distillation/SKILL.md` | HIGH — large file, must be complete |

---

## 6. Validation Checklist (post-migration)

| # | Check | Command | Expected |
|---|---|---|---|
| 1 | CLAUDE.md under 50 lines | `wc -l ~/.claude/CLAUDE.md` | ≤ 50 |
| 2 | All 5 consciousness layers exist | `ls ~/.claude/Consciousness/L*.md` | 5 files |
| 3 | INDEX.md references all 5 layers | `grep "L[0-4]" ~/.claude/Consciousness/INDEX.md` | 5 matches |
| 4 | All 10 rules exist | `find ~/.claude/rules -name "*.md" \| wc -l` | 10 |
| 5 | All 4 skills exist | `ls ~/.claude/skills/*/SKILL.md` | 4 files |
| 6 | shopify-ops has procedures/ subdir | `ls ~/.claude/skills/shopify-ops/procedures/` | split-draft-order.md |
| 7 | No hardcoded plugin paths in skills | `grep -r "plugins/local" ~/.claude/skills/` | 0 matches |
| 8 | consolidator agent exists | `cat ~/.claude/agents/consolidator.md` | Valid markdown |
| 9 | Hooks in settings.json | `grep "SessionStart\|~~PostCompact~~ (REMOVED — unnecessary, CLAUDE.md survives compaction natively)" ~/.claude/settings.json` | 2 matches |
| 10 | Memory dead link removed | `grep "convention_file_organization" ~/.claude/memory/MEMORY.md` | 0 matches |
| 11 | Old plugins removed | `ls ~/.claude/plugins/local/` | Empty (or .gitkeep) |
| 12 | zshrc alias updated | `grep "plugin-dir" ~/.zshrc` | 0 matches |
| 13 | No wrong frontmatter in Consciousness | `grep "type: feedback" ~/.claude/Consciousness/*.md` | 0 matches |
| 14 | L4-evolution references consolidation | `grep "consolidation" ~/.claude/Consciousness/L4-evolution.md` | ≥ 1 match |
| 15 | Consolidation references L4 | `grep "L4\|evolution" ~/.claude/rules/meta/consolidation.md` | ≥ 1 match |
| 16 | user-invocable uses hyphen | `grep -r "user_invocable" ~/.claude/rules/` | 0 matches |
| 17 | reference_claude_playbook updated | `grep "staging area" ~/.claude/memory/reference_claude_playbook.md` | 0 matches |
| 18 | Skills use ${CLAUDE_SKILL_DIR} | `grep "CLAUDE_SKILL_DIR" ~/.claude/skills/shopify-ops/SKILL.md` | ≥ 1 match |
| 19 | feedback_no_hardcoded_values.md preserved | `cat ~/.claude/memory/feedback_no_hardcoded_values.md` | File exists with content |
| 20 | No underscore user_invocable in skills | `grep -r "user_invocable" ~/.claude/skills/` | 0 matches |
| 21 | custom-plugins SKILL.md rewritten | `grep "junlin-custom-skills" ~/.claude/skills/custom-plugins/SKILL.md` | 0 matches |
| 22 | Consolidation references consolidator agent | `grep "consolidator" ~/.claude/rules/meta/consolidation.md` | ≥ 1 match |
| 23 | Consolidator agent is read-only | `grep -i "write\|edit" ~/.claude/agents/consolidator.md` | 0 matches in tools field |
| 24 | Old plugin dirs removed | `test -d ~/.claude/plugins/local/junlin-custom-skills && echo FAIL \|\| echo PASS` | PASS |
