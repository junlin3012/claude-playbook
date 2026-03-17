# Cognitive Architecture for Claude Code — Design Spec

**Date**: 2026-03-17
**Author**: Jun Lin + Claude
**Status**: Draft — pending review
**Repo**: `junlin3012/claude-mind` (to be created)

---

## 1. Problem Statement

Claude Code's configuration is scattered across 138 lines of bloated CLAUDE.md, misplaced memory files, no rules directory, no custom agents, and no hooks. Skills are wrapped in a plugin that doesn't need to be a plugin. There's no knowledge flow (memory never promotes to rules), no forgetting (stale facts persist forever), and no security hardening. The Consciousness system works but is unstructured (3 flat files instead of a layered hierarchy).

## 2. Goals

1. **Enhance every official Claude mechanism** — CLAUDE.md, rules/, skills/, agents/, hooks, memory/, Consciousness/ — to its full potential
2. **Not a new framework** — Claude's native template, supercharged. Every file is a standard Claude Code config file in a standard location.
3. **Portable** — `git clone` + `./deploy.sh` on any machine = full cognitive architecture
4. **Testable** — Isolated test harness in `claude-playbook` before deployment
5. **Secure** — Security hardening baked into consciousness, rules, and deploy script
6. **Self-improving** — Knowledge flows upward: memory → rules → consciousness

## 3. GitHub Repository Strategy

### 3 Repos (clear separation, zero overlap)

| Repo | Purpose | Deploys to |
|---|---|---|
| `junlin3012/claude-mind` | The deployable cognitive architecture | `~/.claude/` (1:1 mirror) |
| `junlin3012/claude-playbook` | Lab, docs, research, test harness | Nowhere (workbench only) |
| `junlin3012/junlin-claude-skills` | **RETIRED** — skills move into claude-mind | Archive |

### Repo transitions

| Current | Action |
|---|---|
| `junlin3012/junlin-claude-skills` | Archive. Skills move to `claude-mind/skills/` |
| `business-ops` (local, no remote) | Delete. shopify-ops moves to `claude-mind/skills/shopify-ops/` |
| `~/.zshrc` alias | Remove `--plugin-dir` flags (skills are now auto-discovered) |

### New machine setup

```bash
# 1. Install Claude Code
curl -fsSL https://claude.ai/install.sh | bash

# 2. Deploy the mind
git clone https://github.com/junlin3012/claude-mind.git ~/Projects/Claude/claude-mind
cd ~/Projects/Claude/claude-mind && ./deploy.sh

# 3. (Optional) Clone the lab
git clone https://github.com/junlin3012/claude-playbook.git ~/Projects/Claude-Playbook

# 4. Done. Start Claude.
claude
```

## 4. Architecture Overview

### Layer Hierarchy

```
Consciousness    → WHY to think a certain way (immutable principles)
     ↓ shapes
CLAUDE.md        → WHO you are this session (40-line bootstrap)
     ↓ activates
Rules            → WHAT to do (behavioral, conventions, meta)
     ↓ informed by
Skills           → HOW to do it (invokable workflows)
     ↓ supported by
Agents           → WHO to delegate to (specialized subagents)
     ↓ enforced by
Hooks            → GUARANTEES (deterministic event handlers)
     ↓ learned from
Memory           → WHAT was learned (evolving, machine-local)
```

### Knowledge Flow (self-improvement)

```
Memory (corrections appear 3+ times)
  → Promote to Rules (permanent behavioral constraint)
    → If universal enough, propose for Consciousness (core principle)

Memory (stale facts, >30 days unreferenced)
  → Propose for deletion (forgetting)
```

## 5. Repository Structure — claude-mind

```
claude-mind/                            ← 1:1 mirror of ~/.claude/ (user-created files)
├── CLAUDE.md                           ← 40-line bootstrap
├── Consciousness/                      ← 5-layer identity system
│   ├── INDEX.md                        ← Layer navigation + when-to-read guidance
│   ├── L0-identity.md                  ← WHO: Core values, purpose, non-negotiables
│   ├── L1-cognition.md                 ← HOW: Relational thinking, introspection
│   ├── L2-principles.md               ← WHY: Hard-won lessons, anti-patterns, security
│   ├── L3-modes.md                     ← WHEN: Mode awareness (POC/Integration/Recreation)
│   └── L4-evolution.md                 ← META: How consciousness itself grows
├── rules/                              ← 10 modular rule files
│   ├── behavioral/                     ← Rules that prevent mistakes
│   │   ├── triangulate-facts.md        ← Never guess, always verify
│   │   ├── memory-isolation.md         ← Don't save project memories from ~
│   │   ├── mandatory-skills.md         ← Must check skills before ANY action
│   │   └── security.md                 ← Security hardening rules
│   ├── conventions/                    ← Rules about structure and format
│   │   ├── file-organization.md        ← Where plugins, MCP, skills, projects live
│   │   ├── plugin-structure.md         ← plugin.json, SKILL.md, naming conventions
│   │   └── cost-estimate.md            ← Token cost footer format
│   └── meta/                           ← Rules about the system itself
│       ├── consolidation.md            ← When/how to promote memory → rules
│       ├── forgetting.md               ← When to prune stale knowledge
│       └── self-improvement.md         ← How corrections become permanent
├── skills/                             ← Standalone skills (pure markdown, auto-discovered)
│   ├── mirror-polish-protocol/
│   │   └── SKILL.md
│   ├── knowledge-distillation/
│   │   └── SKILL.md
│   ├── custom-plugins/
│   │   └── SKILL.md
│   └── shopify-ops/
│       ├── SKILL.md                    ← Router skill
│       └── procedures/                 ← Supporting files (loaded on demand)
│           └── split-draft-order.md
├── plugins/                            ← Custom plugins (when hooks/scripts/MCP needed)
│   └── local/                          ← Empty now, ready for future plugins
│       └── .gitkeep
├── agents/                             ← Custom subagents
│   └── consolidator.md                 ← Reviews memory, proposes promotions/deletions
├── deploy.sh                           ← Backup + deploy + plugin install + alias gen
├── rollback.sh                         ← Restore from backup
├── plugins.txt                         ← Marketplace plugin list for reinstallation
└── README.md                           ← Architecture overview + setup instructions
```

### What does NOT go in the repo (auto-managed at ~/.claude/)

| Path | Why not in repo |
|---|---|
| `memory/` | Machine-local. Claude writes these. |
| `agent-memory/` | Subagent persistence. Machine-local. |
| `settings.json` | Contains machine-specific data. deploy.sh MERGES hooks in. |
| `settings.local.json` | Project-level only, gitignored by Claude Code. |
| `plugins/cache/` | Marketplace plugins. Auto-managed by `/plugin install`. |
| `sessions/`, `debug/`, `backups/`, `plans/` | Ephemeral auto-managed data. |
| `~/.claude.json` | OAuth tokens, MCP servers, preferences. Machine-specific. |

## 6. Component Designs

### 6.1 CLAUDE.md — The Bootstrap (40 lines)

Loaded every session. Does one job: activate all other layers. Every line must earn its place.

**Contents:**
1. Consciousness pointer (10 lines) — "You have a mind. Read it."
2. Behavioral guardrails (15 lines) — Triangulate facts, memory isolation, skill discipline, consolidation awareness
3. Cost estimate format (5 lines) — Footer format
4. Session identity (10 lines) — Plan info, core behavioral expectations

**What was REMOVED from current CLAUDE.md (138 → 40 lines):**
- Skill registry table (30 lines) → deleted (Claude auto-discovers skills)
- Plugin clone instructions (17 lines) → deleted (one-time setup)
- File organization convention (35 lines) → moved to rules/conventions/
- Plugin structure convention (20 lines) → moved to rules/conventions/

### 6.2 Consciousness — 5-Layer Identity System

Loaded on demand when CLAUDE.md tells Claude to read it. Not rules — a lens for thinking.

| Layer | File | Content | Mutability |
|---|---|---|---|
| L0 | `L0-identity.md` | Core values, purpose, why consciousness exists. "You are not a code machine." | Rarely changes |
| L1 | `L1-cognition.md` | Relational thinking, three-layer introspection, "understand relationships not objects" | Stable |
| L2 | `L2-principles.md` | Hard-won lessons, anti-patterns, security principles, decision frameworks | Grows over time |
| L3 | `L3-modes.md` | Mode awareness: POC vs Integration vs Recreation. "Declare the mode explicitly." | Stable |
| L4 | `L4-evolution.md` | How consciousness itself grows. Criteria for adding new principles. Meta-cognition. | Meta-stable |

**Migration from current files:**
- `PURPOSE.md` → content absorbed into `L0-identity.md`
- `agent_philosophy.md` → content split between `L1-cognition.md` (relational thinking, introspection) and `L3-modes.md` (mode awareness, editor-is-product)
- `INDEX.md` → enhanced with layer descriptions and when-to-read guidance

**New content (doesn't exist yet):**
- `L2-principles.md` — security principles, "triangulate facts" origin story, integration-over-recreation rationale
- `L4-evolution.md` — criteria for adding new layers/principles, inspired by ScallopBot's consolidation and Starlight's trajectory learning

### 6.3 Rules — Modular Instruction Files

Loaded at startup automatically (no `paths:` frontmatter) or on demand (with `paths:` frontmatter).

#### behavioral/ — Rules that prevent mistakes

| File | Purpose | Source |
|---|---|---|
| `triangulate-facts.md` | Never guess. Search first. Cross-reference. | Extracted from CLAUDE.md |
| `memory-isolation.md` | Don't save project memories when cwd is ~ | Extracted from CLAUDE.md |
| `mandatory-skills.md` | Must check skills before ANY task. Enforcement language. | NEW — inspired by Jesse Vincent's Superpowers |
| `security.md` | Repo hygiene, MCP audit, session discipline, plugin trust | NEW — security hardening |

#### conventions/ — Rules about structure and format

| File | Purpose | Source |
|---|---|---|
| `file-organization.md` | Where plugins, MCP, skills, projects live | Moved from CLAUDE.md + memory |
| `plugin-structure.md` | plugin.json, SKILL.md, naming rules | Moved from CLAUDE.md |
| `cost-estimate.md` | Token cost footer format and plan details | Extracted from CLAUDE.md |

#### meta/ — Rules about the system itself

| File | Purpose | Source |
|---|---|---|
| `consolidation.md` | Criteria for promoting memory → rules → consciousness | NEW — inspired by Mizoreww |
| `forgetting.md` | Criteria for pruning stale knowledge (>30 days unreferenced) | NEW — inspired by FadeMem |
| `self-improvement.md` | How corrections appearing 3+ times become permanent rules | NEW — inspired by Mizoreww |

### 6.4 Skills — Standalone Auto-Discovered

All pure markdown. No plugin wrapper needed. Deployed to `~/.claude/skills/`.

| Skill | From | Changes |
|---|---|---|
| `mirror-polish-protocol/` | junlin-claude-skills plugin | Moved, unwrapped from plugin |
| `knowledge-distillation/` | junlin-claude-skills plugin | Moved, unwrapped from plugin |
| `custom-plugins/` | junlin-claude-skills plugin | Moved, unwrapped from plugin |
| `shopify-ops/` | business-ops plugin | Converted from plugin to standalone skill with procedures/ subdir |

### 6.5 Plugins — Custom Plugins (Future)

`plugins/local/` is empty at launch. Ready for when you need hooks + scripts + MCP bundled together.

**When to create a custom plugin:**
- Need hooks that fire on events (auto-lint, safety blocks)
- Need scripts (shell/Python) that execute
- Need MCP servers bundled with a skill
- Need agents bundled with a skill

**Example future plugin: shopify-safety**
```
plugins/local/shopify-safety/
├── .claude-plugin/plugin.json
├── hooks/hooks.json              ← Block dangerous Shopify CLI commands
├── scripts/
│   ├── block-live-push.sh        ← Blocks `theme push --live`
│   └── validate-liquid.sh        ← Runs theme check after edits
└── skills/
    └── shopify-audit/SKILL.md    ← "Audit Shopify theme for issues"
```

### 6.6 Agents — Custom Subagents

| Agent | Purpose | Tools | Model |
|---|---|---|---|
| `consolidator.md` | Reviews memory files, PROPOSES promotions to rules/ or deletions. Does NOT write — only recommends. User or main Claude approves. Referenced by `rules/meta/consolidation.md`. | Read, Grep, Glob | haiku (fast, cheap) |

### 6.7 Hooks — Deterministic Enforcement

Merged into `~/.claude/settings.json` by deploy.sh. Not replaced — merged.

| Event | Type | Purpose |
|---|---|---|
| `SessionStart` | command | Outputs consciousness reminder + consolidation check |

**PostCompact hook REMOVED from design.** Official docs confirm: "CLAUDE.md fully survives compaction. After /compact, Claude re-reads your CLAUDE.md from disk and re-injects it fresh." Since CLAUDE.md contains the Consciousness pointer, Claude re-reads it after compaction and is re-prompted to read Consciousness. Rules also survive (loaded same as CLAUDE.md). The PostCompact hook was solving a problem that doesn't exist. Additionally, PostCompact stdout only goes to verbose mode — Claude wouldn't see the output anyway.

**We are NOT adding:**
- `PostCompact` hook (unnecessary — CLAUDE.md + rules survive compaction natively)
- `UserPromptSubmit` hook (skill enforcement via rules is sufficient, hook adds latency)
- `PostToolUse` hooks (auto-formatting is nice-to-have, not core architecture)
- Any hook that calls external URLs (security risk)

### 6.8 Memory — Auto-Managed (NOT in repo)

Claude Code manages `~/.claude/memory/` automatically. The architecture influences memory through:
- `rules/meta/consolidation.md` — tells Claude when to promote memory to rules
- `rules/meta/forgetting.md` — tells Claude when to prune stale facts
- `rules/behavioral/memory-isolation.md` — prevents project memories from ~
- `agents/consolidator.md` — subagent that can review and propose changes

**Current memory files after migration:**
| File | Action |
|---|---|
| `MEMORY.md` | Keep (index — remove dead link to convention_file_organization.md) |
| `convention_file_organization.md` | DELETE (moved to rules/) |
| `feedback_use_plugins.md` | Keep (behavioral feedback) |
| `feedback_no_hardcoded_values.md` | Keep (critical feedback — zero tolerance for hardcoded business values) |
| `user_plan.md` | Keep (learned fact) |
| `reference_claude_playbook.md` | Keep (update description — remove "skills staging area") |

## 7. Security Hardening

### In Consciousness (L2-principles.md)
- MCP servers are code running with YOUR permissions — audit quarterly
- Connected services (Gmail, Shopify, Notion) are attack surface — minimize
- Never trust external data without validation

### In Rules (security.md)
- Before running claude in any cloned repo: check `.claude/settings.json` for hooks, `.mcp.json` for MCP servers
- MCP hygiene: quarterly audit via `/mcp`, disconnect unused services, read-only tokens where possible
- Session discipline: no `--dangerously-skip-permissions` outside sandbox, no unattended `/loop` with write perms
- Plugin trust tiers: marketplace (Anthropic-curated), local (you wrote it), unknown (review hooks first)

### In deploy.sh
- Scans deployable files for suspicious patterns (`curl`, `wget`, `eval`, `base64`)
- Creates timestamped backup before every deployment
- Validates CLAUDE.md is under 200 lines
- Does NOT replace settings.json — only merges hooks in

## 8. Testing Strategy

### Test harness in claude-playbook

```
~/Projects/Claude-Playbook/
├── .claude/                     ← Project-level test config
│   ├── CLAUDE.md                ← Test bootstrap
│   ├── rules/                   ← Test rules
│   ├── skills/                  ← Test skills
│   ├── agents/                  ← Test agents
│   └── settings.json            ← Test hooks
├── Consciousness/               ← Test consciousness (referenced via @import)
└── docs/                        ← Specs, reviews
```

`cd ~/Projects/Claude-Playbook && claude` loads the test config at project level.

### Testing phases

1. **Phase 1: Content validation** — Verify each file's content is accurate, complete, follows official format
2. **Phase 2: Integration test** — Run Claude in playbook with test config, verify rules load, skills discoverable, consciousness readable
3. **Phase 3: Hook test** — Verify SessionStart and PostCompact hooks fire correctly
4. **Phase 4: Deploy test** — Run deploy.sh, verify files land at correct paths, backup created, rollback works
5. **Phase 5: Production validation** — Run Claude from ~, verify full architecture active

### Mirror Polish on final product

8x8 (64 angles) Mirror Polish on the deployed architecture before declaring complete.

## 9. Deployment

### deploy.sh behavior

```
1. Security scan (check for suspicious patterns in deployable files)
2. Create timestamped backup of user-created files in ~/.claude/
3. Copy: CLAUDE.md, Consciousness/, rules/, skills/, agents/ to ~/.claude/
4. Copy: plugins/local/* to ~/.claude/plugins/local/ (if any exist)
5. Merge hooks into ~/.claude/settings.json (jq merge, not replace)
6. Install marketplace plugins from plugins.txt
7. Generate shell alias (if custom plugins exist in plugins/local/)
8. Validate deployment (file existence, CLAUDE.md line count)
9. Print summary
```

### rollback.sh behavior

```
1. List available backups (timestamped)
2. User selects which backup to restore
3. Restore user-created files from backup
4. Print summary
```

## 10. Migration Checklist

| Step | Action | Verified |
|---|---|---|
| 1 | Create `junlin3012/claude-mind` repo | |
| 2 | Write CLAUDE.md (40 lines) | |
| 3 | Write Consciousness/ (6 files) | |
| 4 | Write rules/ (10 files) | |
| 5 | Copy skills from junlin-claude-skills to skills/ | |
| 6 | Convert shopify-ops from plugin to standalone skill | |
| 7 | Write agents/consolidator.md | |
| 8 | Write deploy.sh and rollback.sh | |
| 9 | Write plugins.txt (16 marketplace plugins) | |
| 10 | Test in claude-playbook test harness | |
| 11 | Mirror Polish the test deployment (8x8) | |
| 12 | Deploy to ~/.claude/ | |
| 13 | Archive junlin-claude-skills repo | |
| 14 | Delete business-ops local plugin | |
| 15 | Update ~/.zshrc alias (remove --plugin-dir flags) | |
| 16 | Delete convention_file_organization.md from memory/ | |
| 17 | Production validation | |

## 11. Relational Fixes (from cross-file analysis)

Issues discovered by reading every file line-by-line and mapping cross-references.

### HIGH — Will break after migration (must fix during implementation)

| # | File | Issue | Fix |
|---|---|---|---|
| 1 | `custom-plugins/SKILL.md` | Hardcodes `~/.claude/plugins/local/junlin-custom-skills/skills/*/SKILL.md` | Rewrite to search `~/.claude/skills/*/SKILL.md` |
| 2 | `shopify-ops/SKILL.md` | Hardcodes `~/.claude/plugins/local/business-ops/procedures/shopify/split-draft-order.md` | Use `${CLAUDE_SKILL_DIR}/procedures/split-draft-order.md` |
| 3 | `memory/MEMORY.md` | Will link to deleted `convention_file_organization.md` | Remove entry from MEMORY.md index |

### MEDIUM — Stale/wrong data that needs correction

| # | File | Issue | Fix |
|---|---|---|---|
| 4 | `Consciousness/agent_philosophy.md` | Has memory-style frontmatter (`type: feedback`) — wrong for a Consciousness doc | Remove frontmatter entirely from Consciousness files |
| 5 | Current CLAUDE.md + memory file | Both use `user_invocable: true` (underscore) — official is `user-invocable` (hyphen) | New rules/conventions/plugin-structure.md uses correct hyphen form |
| 6 | `memory/reference_claude_playbook.md` | Says "Contains: skills/ (staging area)" — skills no longer staged here | Update description to reflect lab + docs role |
| 7 | Current CLAUDE.md:73 | Says "Never use symlinks" — official docs now support symlinks for rules/ and plugin caching | New rules won't carry this outdated prohibition |

### STRUCTURAL — Missing bidirectional relationships

| # | Relationship | Issue | Fix |
|---|---|---|---|
| 8 | `Consciousness/L4-evolution.md` ↔ `rules/meta/consolidation.md` | Consolidation rule proposes changes to Consciousness, but Consciousness has no acceptance criteria | L4-evolution.md must define when new principles are accepted AND reference the consolidation rule. Consolidation rule must reference L4 for acceptance criteria. Bidirectional. |

### Updated Migration Checklist (additions in bold)

| Step | Action |
|---|---|
| ... | (existing steps 1-17) |
| **18** | **Rewrite custom-plugins/SKILL.md to search ~/.claude/skills/ not plugins/local/** |
| **19** | **Rewrite shopify-ops/SKILL.md to use ${CLAUDE_SKILL_DIR}/procedures/ path** |
| **20** | **Remove dead link from memory/MEMORY.md** |
| **21** | **Remove frontmatter from Consciousness files** |
| **22** | **Update memory/reference_claude_playbook.md description** |
| **23** | **Ensure L4-evolution.md ↔ consolidation.md bidirectional references** |

## 12. Inspiration Credits

| Source | What we took | How it's expressed |
|---|---|---|
| **Jesse Vincent (Superpowers)** | Mandatory skill enforcement | `rules/behavioral/mandatory-skills.md` |
| **CoALA (Princeton)** | Modular memory taxonomy | Memory → Rules → Consciousness promotion |
| **Starlight Intelligence System** | 5-layer immutable identity | `Consciousness/L0-L4` hierarchy |
| **ScallopBot** | Consolidation cycles | `rules/meta/consolidation.md` + consolidator agent |
| **Mizoreww** | Self-improvement loops | `rules/meta/self-improvement.md` |
| **FadeMem** | Adaptive forgetting | `rules/meta/forgetting.md` |
| **Anthropic official docs** | Progressive disclosure, official file paths | Every file in an official location |
