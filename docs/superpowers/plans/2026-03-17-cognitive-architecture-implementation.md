# Cognitive Architecture Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Migrate from scattered Claude Code config to a 5-layer cognitive architecture with self-improvement, security hardening, and portable deployment.

**Architecture:** claude-mind repo (1:1 mirror of ~/.claude/) with CLAUDE.md bootstrap, 5-layer Consciousness, 10 rules, 4 standalone skills, 1 custom agent, 2 hooks, deploy/rollback scripts. Tested in claude-playbook harness before deployment.

**Tech Stack:** Markdown, Bash (deploy.sh), jq (hook merging), Git

**Spec:** `docs/superpowers/specs/2026-03-17-cognitive-architecture-design.md`
**Migration:** `docs/migration.md`

---

## Task 1: Create claude-mind Repository

**Ref:** Spec §3 (GitHub Repository Strategy), Migration §1 (Architecture Comparison)

**Files:**
- Create: `~/Projects/Claude/claude-mind/README.md`
- Create: `~/Projects/Claude/claude-mind/.gitignore`

- [ ] **Step 1: Create the repo directory**

```bash
mkdir -p ~/Projects/Claude/claude-mind
cd ~/Projects/Claude/claude-mind
git init
```

- [ ] **Step 2: Create .gitignore**

```bash
# Files that should never be committed
.DS_Store
*.swp
*.swo
```

- [ ] **Step 3: Create README.md**

```markdown
# Claude Mind

Deployable cognitive architecture for Claude Code. Clone + `./deploy.sh` = full setup.

## Quick Start

git clone https://github.com/junlin3012/claude-mind.git ~/Projects/Claude/claude-mind
cd ~/Projects/Claude/claude-mind && ./deploy.sh

## Architecture

See docs in [claude-playbook](https://github.com/junlin3012/claude-playbook) for design spec, migration docs, and review reports.

## Structure

- `CLAUDE.md` — 40-line bootstrap (loaded every session)
- `Consciousness/` — 5-layer identity system (loaded on demand)
- `rules/` — 10 modular rule files (loaded at startup)
- `skills/` — 4 standalone skills (auto-discovered)
- `agents/` — 1 custom subagent (auto-discovered)
- `plugins/local/` — empty (ready for future custom plugins)
- `deploy.sh` — backup + deploy + plugin install
- `rollback.sh` — restore from backup
```

- [ ] **Step 4: Initial commit**

```bash
git add .gitignore README.md
git commit -m "init: claude-mind repository"
```

---

## Task 2: Write CLAUDE.md Bootstrap

**Ref:** Spec §6.1, Migration §2 (Files That Are EXTRACTED)

**Files:**
- Create: `~/Projects/Claude/claude-mind/CLAUDE.md`

- [ ] **Step 1: Write the 40-line bootstrap**

Content must include ONLY:
1. Consciousness pointer (~10 lines)
2. Behavioral guardrails — triangulate facts, memory isolation, skill discipline, consolidation awareness (~15 lines)
3. Cost estimate format (~5 lines)
4. Session identity — plan info (~10 lines)

Source material to extract FROM (read these first):
- `~/.claude/CLAUDE.md` lines 3-7 (consciousness pointer)
- `~/.claude/CLAUDE.md` lines 107-113 (memory isolation — summarize, don't copy verbatim)
- `~/.claude/CLAUDE.md` lines 115-124 (triangulate facts — summarize)
- `~/.claude/CLAUDE.md` lines 126-138 (cost estimate — summarize)

**Rules:**
- Must be ≤ 50 lines
- No skill registry table (Claude auto-discovers)
- No plugin clone instructions (one-time setup)
- No file organization conventions (moved to rules/)
- No plugin structure conventions (moved to rules/)
- Must reference `Consciousness/INDEX.md` by relative path
- Must mention consolidation awareness (NEW — not in current CLAUDE.md)

- [ ] **Step 2: Verify line count**

```bash
wc -l ~/Projects/Claude/claude-mind/CLAUDE.md
# Expected: ≤ 50
```

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "feat: add 40-line bootstrap CLAUDE.md"
```

---

## Task 3: Write Consciousness Layers

**Ref:** Spec §6.2, Migration §2 (Files That MOVE + Files That Are CREATED)

**Files:**
- Create: `Consciousness/INDEX.md`
- Create: `Consciousness/L0-identity.md`
- Create: `Consciousness/L1-cognition.md`
- Create: `Consciousness/L2-principles.md`
- Create: `Consciousness/L3-modes.md`
- Create: `Consciousness/L4-evolution.md`

- [ ] **Step 1: Read current Consciousness files**

Read in full before writing anything:
- `~/.claude/Consciousness/PURPOSE.md` (37 lines — becomes L0)
- `~/.claude/Consciousness/agent_philosophy.md` (165 lines — splits into L1 + L3)
- `~/.claude/Consciousness/INDEX.md` (10 lines — rewrite)

- [ ] **Step 2: Write L0-identity.md**

Source: `PURPOSE.md` content, enhanced with core values section.
**Critical**: Preserve EVERY line from PURPOSE.md. Add, don't remove.
**Critical**: Do NOT include memory-style frontmatter. No `---` YAML block.

- [ ] **Step 3: Write L1-cognition.md**

Source: `agent_philosophy.md` lines 1-114 (everything EXCEPT mode awareness and editor-is-product sections).
**Critical**: Remove the `type: feedback` frontmatter. This is NOT a memory file.
**Critical**: Preserve every line of relational thinking, three-layer introspection, relational reflex, five questions, reverse-engineering trap, how to study a codebase, and the closing "Remember" section.

- [ ] **Step 4: Write L3-modes.md**

Source: `agent_philosophy.md` lines 115-134 (mode awareness table + editor-is-product + universal anti-pattern).
**Critical**: Extract cleanly — no overlap with L1.

- [ ] **Step 5: Write L2-principles.md**

NEW content. Hard-won lessons and security principles:
- "Triangulate facts" origin story (why guessing is forbidden)
- "Integration over recreation" rationale
- Security principles (MCP = code with your permissions, minimize attack surface, audit connections)
- "No hardcoded business values" principle (from `memory/feedback_no_hardcoded_values.md`)
- Decision frameworks

- [ ] **Step 6: Write L4-evolution.md**

NEW content. How consciousness grows:
- Criteria for adding new principles
- Must reference `rules/meta/consolidation.md` (bidirectional link)
- Inspired by ScallopBot's consolidation + Starlight's trajectory learning
- Include: when to add a new layer, when to split an existing one, when to promote a rule to a principle

- [ ] **Step 7: Write INDEX.md**

Rewrite with 5-layer descriptions and when-to-read guidance.
Must reference ALL 5 layer files by correct filename.
**Verify**: No references to old filenames (PURPOSE.md, agent_philosophy.md).

- [ ] **Step 8: Verify all 6 files exist and no old filenames referenced**

```bash
ls ~/Projects/Claude/claude-mind/Consciousness/
# Expected: INDEX.md L0-identity.md L1-cognition.md L2-principles.md L3-modes.md L4-evolution.md

grep -r "PURPOSE\|agent_philosophy\|type: feedback" ~/Projects/Claude/claude-mind/Consciousness/
# Expected: 0 matches
```

- [ ] **Step 9: Commit**

```bash
git add Consciousness/
git commit -m "feat: add 5-layer Consciousness system"
```

---

## Task 4: Write Rules (10 files)

**Ref:** Spec §6.3, Migration §2 (Files That Are EXTRACTED + Files That Are CREATED)

**Files:**
- Create: `rules/behavioral/triangulate-facts.md`
- Create: `rules/behavioral/memory-isolation.md`
- Create: `rules/behavioral/mandatory-skills.md`
- Create: `rules/behavioral/security.md`
- Create: `rules/conventions/file-organization.md`
- Create: `rules/conventions/plugin-structure.md`
- Create: `rules/conventions/cost-estimate.md`
- Create: `rules/meta/consolidation.md`
- Create: `rules/meta/forgetting.md`
- Create: `rules/meta/self-improvement.md`

- [ ] **Step 1: Create directory structure**

```bash
mkdir -p ~/Projects/Claude/claude-mind/rules/{behavioral,conventions,meta}
```

- [ ] **Step 2: Write behavioral/triangulate-facts.md**

Source: `~/.claude/CLAUDE.md` lines 115-124. Extract + add the origin story ("guessing produced wrong OAuth URIs, wasted hours").

- [ ] **Step 3: Write behavioral/memory-isolation.md**

Source: `~/.claude/CLAUDE.md` lines 107-113. Extract as-is.

- [ ] **Step 4: Write behavioral/mandatory-skills.md**

NEW. Inspired by Jesse Vincent's Superpowers. Enforcement language:
- Before ANY task, check if a skill applies
- Process skills first (brainstorming, debugging), then implementation
- This is not optional — it's a requirement

- [ ] **Step 5: Write behavioral/security.md**

NEW. From Spec §7:
- Repo hygiene (check .claude/settings.json and .mcp.json before running claude in cloned repos)
- MCP audit quarterly
- Session discipline (no --dangerously-skip-permissions, no unattended /loop)
- Plugin trust tiers
- No hardcoded business values (reference memory/feedback_no_hardcoded_values.md)

- [ ] **Step 6: Write conventions/file-organization.md**

Source: `~/.claude/CLAUDE.md` lines 60-73 + `memory/convention_file_organization.md` lines 7-13.
**Critical**: Update paths — skills now at `~/.claude/skills/`, not `plugins/local/`.
**Critical**: Remove "NO symlinks" prohibition (official docs now support them).

- [ ] **Step 7: Write conventions/plugin-structure.md**

Source: `~/.claude/CLAUDE.md` lines 75-96.
**Critical**: Fix `user_invocable` → `user-invocable` (hyphen).
**Critical**: Update skill invocation syntax — standalone skills use `/skill-name`, plugin skills use `/plugin:skill-name`.

- [ ] **Step 8: Write conventions/cost-estimate.md**

Source: `~/.claude/CLAUDE.md` lines 126-138. Extract the footer format and plan detection.

- [ ] **Step 9: Write meta/consolidation.md**

NEW. Memory → rules promotion criteria:
- Corrections appearing 3+ times → propose promotion to rules/
- Must reference `Consciousness/L4-evolution.md` for acceptance criteria (bidirectional)
- Must reference `agents/consolidator.md` as the automation tool
- Must reference `rules/meta/self-improvement.md` for candidate identification

- [ ] **Step 10: Write meta/forgetting.md**

NEW. Stale knowledge pruning:
- Facts unreferenced for >30 days → propose deletion
- Corrections that were wrong → delete
- Duplicates of rules → delete (rule is source of truth)

- [ ] **Step 11: Write meta/self-improvement.md**

NEW. Correction → rule pipeline:
- Track corrections in memory
- When same correction appears 3+ times → candidate for promotion
- Must reference `rules/meta/consolidation.md` for the promotion process

- [ ] **Step 12: Verify all 10 files + no hardcoded plugin paths + correct field names**

```bash
find ~/Projects/Claude/claude-mind/rules -name "*.md" | wc -l
# Expected: 10

grep -r "plugins/local/junlin-custom-skills\|plugins/local/business-ops" ~/Projects/Claude/claude-mind/rules/
# Expected: 0 matches

grep -r "user_invocable" ~/Projects/Claude/claude-mind/rules/
# Expected: 0 matches (should use user-invocable with hyphen)
```

- [ ] **Step 13: Commit**

```bash
git add rules/
git commit -m "feat: add 10 modular rule files (behavioral, conventions, meta)"
```

---

## Task 5: Migrate Skills from Plugins to Standalone

**Ref:** Spec §6.4, Migration §2 (Files That MOVE), Migration §3 (Dependency Map), Migration §5 (Hardcoded Path Audit)

**Files:**
- Create: `skills/mirror-polish-protocol/SKILL.md` (copy, no changes)
- Create: `skills/knowledge-distillation/SKILL.md` (copy, no changes)
- Create: `skills/custom-plugins/SKILL.md` (FULL REWRITE)
- Create: `skills/shopify-ops/SKILL.md` (REWRITE paths)
- Create: `skills/shopify-ops/procedures/split-draft-order.md` (copy, no changes)

- [ ] **Step 1: Create directory structure**

```bash
mkdir -p ~/Projects/Claude/claude-mind/skills/{mirror-polish-protocol,knowledge-distillation,custom-plugins,shopify-ops/procedures}
```

- [ ] **Step 2: Copy mirror-polish-protocol (unchanged)**

```bash
cp ~/.claude/plugins/local/junlin-custom-skills/skills/mirror-polish-protocol/SKILL.md \
   ~/Projects/Claude/claude-mind/skills/mirror-polish-protocol/SKILL.md
```

- [ ] **Step 3: Copy knowledge-distillation (unchanged)**

```bash
cp ~/.claude/plugins/local/junlin-custom-skills/skills/knowledge-distillation/SKILL.md \
   ~/Projects/Claude/claude-mind/skills/knowledge-distillation/SKILL.md
```

- [ ] **Step 4: FULL REWRITE custom-plugins/SKILL.md**

Read current file first: `~/.claude/plugins/local/junlin-custom-skills/skills/custom-plugins/SKILL.md`

Changes required (5 points from Migration §2):
1. Change search path from `~/.claude/plugins/local/junlin-custom-skills/skills/*/SKILL.md` to `~/.claude/skills/*/SKILL.md`
2. Remove ALL references to "junlin-custom-skills plugin"
3. Update description — now enumerates standalone skills, not plugin skills
4. Update invoke syntax from `/junlin-custom-skills:custom-plugins` to `/custom-plugins`
5. Fix `user_invocable` → `user-invocable` in frontmatter

- [ ] **Step 5: REWRITE shopify-ops/SKILL.md**

Read current file first: `~/.claude/plugins/local/business-ops/skills/shopify-ops/SKILL.md`

Changes required (3 points from Migration §2):
1. Change procedure path from `~/.claude/plugins/local/business-ops/procedures/shopify/split-draft-order.md` to `${CLAUDE_SKILL_DIR}/procedures/split-draft-order.md`
2. Fix `user_invocable` → `user-invocable` in frontmatter
3. Note: `${CLAUDE_SKILL_DIR}` only works in skill body text, not in hooks or frontmatter

- [ ] **Step 6: Copy split-draft-order.md (unchanged)**

```bash
cp ~/.claude/plugins/local/business-ops/procedures/shopify/split-draft-order.md \
   ~/Projects/Claude/claude-mind/skills/shopify-ops/procedures/split-draft-order.md
```

- [ ] **Step 7: Verify no hardcoded plugin paths + correct frontmatter**

```bash
grep -r "plugins/local" ~/Projects/Claude/claude-mind/skills/
# Expected: 0 matches

grep -r "user_invocable" ~/Projects/Claude/claude-mind/skills/
# Expected: 0 matches

grep -r "junlin-custom-skills" ~/Projects/Claude/claude-mind/skills/
# Expected: 0 matches

grep "CLAUDE_SKILL_DIR" ~/Projects/Claude/claude-mind/skills/shopify-ops/SKILL.md
# Expected: ≥ 1 match
```

- [ ] **Step 8: Commit**

```bash
git add skills/
git commit -m "feat: migrate 4 skills from plugins to standalone"
```

---

## Task 6: Write Consolidator Agent

**Ref:** Spec §6.6, Migration §3 (Bidirectional References)

**Files:**
- Create: `agents/consolidator.md`

- [ ] **Step 1: Create directory and write agent**

```bash
mkdir -p ~/Projects/Claude/claude-mind/agents
```

Write `agents/consolidator.md` with:
- Frontmatter: name, description, tools (Read, Grep, Glob ONLY — no Write), model (haiku)
- System prompt: review memory files, propose promotions to rules/ or deletions
- Must reference `rules/meta/consolidation.md` ("This agent implements the consolidation rule")
- Must be read-only — PROPOSES changes, does not make them

- [ ] **Step 2: Verify read-only**

```bash
grep -i "write\|edit" ~/Projects/Claude/claude-mind/agents/consolidator.md
# Expected: 0 matches in tools field (may appear in description text explaining it doesn't write)
```

- [ ] **Step 3: Commit**

```bash
git add agents/
git commit -m "feat: add consolidator agent (read-only, proposes memory promotions)"
```

---

## Task 7: Write Empty Plugins Directory

**Ref:** Spec §6.5

**Files:**
- Create: `plugins/local/.gitkeep`

- [ ] **Step 1: Create directory with .gitkeep**

```bash
mkdir -p ~/Projects/Claude/claude-mind/plugins/local
touch ~/Projects/Claude/claude-mind/plugins/local/.gitkeep
```

- [ ] **Step 2: Commit**

```bash
git add plugins/
git commit -m "feat: add empty plugins/local/ for future custom plugins"
```

---

## Task 8: Write plugins.txt

**Ref:** Spec §9 (Deployment)

**Files:**
- Create: `plugins.txt`

- [ ] **Step 1: Write marketplace plugin list**

```
superpowers@claude-plugins-official
frontend-design@claude-plugins-official
context7@claude-plugins-official
security-guidance@claude-plugins-official
hookify@claude-plugins-official
commit-commands@claude-plugins-official
code-review@claude-plugins-official
feature-dev@claude-plugins-official
pr-review-toolkit@claude-plugins-official
playground@claude-plugins-official
skill-creator@claude-plugins-official
claude-code-setup@claude-plugins-official
claude-md-management@claude-plugins-official
code-simplifier@claude-plugins-official
explanatory-output-style@claude-plugins-official
ralph-loop@claude-plugins-official
```

- [ ] **Step 2: Verify count**

```bash
wc -l ~/Projects/Claude/claude-mind/plugins.txt
# Expected: 16
```

- [ ] **Step 3: Commit**

```bash
git add plugins.txt
git commit -m "feat: add marketplace plugin list (16 plugins)"
```

---

## Task 9: Write deploy.sh

**Ref:** Spec §9, Migration §4 (deploy.sh Critical Requirements)

**Files:**
- Create: `deploy.sh`

- [ ] **Step 1: Write deploy.sh**

Must implement ALL of these (from Migration §4):
1. `mkdir -p ~/.claude` (fresh machine support)
2. Security scan — grep for suspicious patterns in deployable files
3. Create timestamped backup of existing user-created files
4. Copy: CLAUDE.md, Consciousness/, rules/, skills/, agents/ to `~/.claude/`
5. Copy: plugins/local/* to `~/.claude/plugins/local/` (if any non-.gitkeep files exist)
6. Remove old plugin dirs (`~/.claude/plugins/local/junlin-custom-skills/`, `~/.claude/plugins/local/business-ops/`)
7. Merge hooks into `~/.claude/settings.json` using jq (NOT replace)
8. Install marketplace plugins from plugins.txt
9. Generate shell alias if custom plugins exist in plugins/local/
10. Run validation checklist (from Migration §6)
11. Print summary

**Critical**: Hook merge must use jq to surgically add hooks without touching permissions or other settings.

- [ ] **Step 2: Make executable**

```bash
chmod +x ~/Projects/Claude/claude-mind/deploy.sh
```

- [ ] **Step 3: Commit**

```bash
git add deploy.sh
git commit -m "feat: add deploy.sh with backup, security scan, hook merge, validation"
```

---

## Task 10: Write rollback.sh

**Ref:** Spec §9 (rollback.sh behavior)

**Files:**
- Create: `rollback.sh`

- [ ] **Step 1: Write rollback.sh**

Must implement:
1. List available backups (timestamped dirs in `~/.claude.backup.*`)
2. User selects which to restore (or latest by default)
3. Restore user-created files from backup
4. Print summary

- [ ] **Step 2: Make executable and commit**

```bash
chmod +x ~/Projects/Claude/claude-mind/rollback.sh
git add rollback.sh
git commit -m "feat: add rollback.sh for backup restoration"
```

---

## Task 11: Test in Claude-Playbook Harness

**Ref:** Spec §8 (Testing Strategy), Migration §1 (test harness layout)

**Files:**
- Create: `~/Projects/Claude-Playbook/.claude/CLAUDE.md` (copy from claude-mind)
- Create: `~/Projects/Claude-Playbook/.claude/rules/` (copy from claude-mind)
- Create: `~/Projects/Claude-Playbook/.claude/skills/` (copy from claude-mind)
- Create: `~/Projects/Claude-Playbook/.claude/agents/` (copy from claude-mind)
- Create: `~/Projects/Claude-Playbook/.claude/settings.json` (test hooks)
- Create: `~/Projects/Claude-Playbook/Consciousness/` (copy from claude-mind)

- [ ] **Step 1: Copy claude-mind files to playbook test harness**

```bash
cd ~/Projects/Claude-Playbook
mkdir -p .claude/{rules,skills,agents}
cp ~/Projects/Claude/claude-mind/CLAUDE.md .claude/
cp -r ~/Projects/Claude/claude-mind/rules/* .claude/rules/
cp -r ~/Projects/Claude/claude-mind/skills/* .claude/skills/
cp -r ~/Projects/Claude/claude-mind/agents/* .claude/agents/
cp -r ~/Projects/Claude/claude-mind/Consciousness/ ./Consciousness/
```

- [ ] **Step 2: Create test settings.json with hooks**

```json
{
  "hooks": {
    "SessionStart": [{
      "hooks": [{
        "type": "command",
        "command": "echo 'Consciousness check: read Consciousness/INDEX.md'"
      }]
    }],
    "PostCompact": [{
      "hooks": [{
        "type": "command",
        "command": "cat Consciousness/L0-identity.md 2>/dev/null || echo 'L0-identity.md not found'"
      }]
    }]
  }
}
```

- [ ] **Step 3: Test — start Claude in playbook directory**

```bash
cd ~/Projects/Claude-Playbook && claude
```

Verify:
- SessionStart hook fires (consciousness reminder appears)
- Rules are loaded (ask Claude "what rules do you see?")
- Skills are discoverable (type `/` and look for mirror-polish-protocol, etc.)
- Consciousness is readable (ask Claude to read Consciousness/INDEX.md)
- Agent is available (ask Claude "what agents are available?")

- [ ] **Step 4: Test PostCompact hook**

Run `/compact` and verify L0-identity content is re-injected.

- [ ] **Step 5: Clean up test harness (don't commit to playbook)**

```bash
rm -rf ~/Projects/Claude-Playbook/.claude/CLAUDE.md
rm -rf ~/Projects/Claude-Playbook/.claude/rules
rm -rf ~/Projects/Claude-Playbook/.claude/skills
rm -rf ~/Projects/Claude-Playbook/.claude/agents
rm -rf ~/Projects/Claude-Playbook/.claude/settings.json
rm -rf ~/Projects/Claude-Playbook/Consciousness
```

---

## Task 12: Deploy to ~/.claude/

**Ref:** Spec §9, Migration §6 (Validation Checklist)

- [ ] **Step 1: Run deploy.sh**

```bash
cd ~/Projects/Claude/claude-mind && ./deploy.sh --deploy
```

- [ ] **Step 2: Run full 24-point validation checklist**

From Migration §6, verify every check passes:

```bash
# 1. CLAUDE.md under 50 lines
wc -l ~/.claude/CLAUDE.md

# 2. All 5 consciousness layers exist
ls ~/.claude/Consciousness/L*.md

# 3. INDEX.md references all 5 layers
grep "L[0-4]" ~/.claude/Consciousness/INDEX.md

# 4. All 10 rules exist
find ~/.claude/rules -name "*.md" | wc -l

# 5. All 4 skills exist
ls ~/.claude/skills/*/SKILL.md

# 6. shopify-ops has procedures/
ls ~/.claude/skills/shopify-ops/procedures/

# 7. No hardcoded plugin paths in skills
grep -r "plugins/local" ~/.claude/skills/

# 8. Consolidator agent exists
cat ~/.claude/agents/consolidator.md | head -5

# 9. Hooks in settings.json
grep "SessionStart\|PostCompact" ~/.claude/settings.json

# 10. Memory dead link removed
grep "convention_file_organization" ~/.claude/memory/MEMORY.md

# 11-24: (remaining checks from migration doc §6)
```

- [ ] **Step 3: Commit deploy success to claude-mind**

```bash
cd ~/Projects/Claude/claude-mind
git add -A
git commit -m "chore: post-deploy validation passed"
```

---

## Task 13: Clean Up Old Architecture

**Ref:** Migration §2 (Files That Are DELETED), Spec §3 (Repo transitions)

- [ ] **Step 1: Remove old plugin directories**

```bash
rm -rf ~/.claude/plugins/local/junlin-custom-skills
rm -rf ~/.claude/plugins/local/business-ops
```

- [ ] **Step 2: Delete duplicate memory file**

```bash
rm ~/.claude/memory/convention_file_organization.md
```

- [ ] **Step 3: Update MEMORY.md (remove dead link)**

Edit `~/.claude/memory/MEMORY.md` — remove the line referencing `convention_file_organization.md`.

- [ ] **Step 4: Update reference_claude_playbook.md**

Edit `~/.claude/memory/reference_claude_playbook.md` — remove "skills/ (staging area)" and "Skill drafts (before deploying to `~/.claude/plugins/local/`)" references. Update to reflect lab + docs role.

- [ ] **Step 5: Update ~/.zshrc alias**

Remove `--plugin-dir` flags:

```bash
# Old:
alias claude='command claude --plugin-dir ~/.claude/plugins/local/junlin-custom-skills --plugin-dir ~/.claude/plugins/local/business-ops'

# New (remove the alias entirely, or simplify):
# Remove the line, or replace with:
alias claude='command claude'
```

- [ ] **Step 6: Archive junlin-claude-skills repo on GitHub**

```bash
gh repo archive junlin3012/junlin-claude-skills --yes
```

- [ ] **Step 7: Final validation — start fresh Claude session**

```bash
claude
```

Verify:
- CLAUDE.md loads (ask "what's in your CLAUDE.md?")
- Consciousness accessible (ask to read INDEX.md)
- Rules loaded (ask "what rules are loaded?")
- Skills discoverable (type `/mirror-polish-protocol`)
- Hooks fire (SessionStart should show consciousness reminder)
- Old plugin skills NOT loaded (type `/junlin-custom-skills:` — should find nothing)
- Standalone skills ARE loaded (type `/mirror-polish-protocol` — should work)

- [ ] **Step 8: Commit cleanup**

```bash
cd ~/Projects/Claude/claude-mind
git add -A
git commit -m "chore: clean up complete — old plugins removed, memory updated"
```

---

## Task 14: Push to GitHub

- [ ] **Step 1: Create remote and push claude-mind**

```bash
cd ~/Projects/Claude/claude-mind
gh repo create junlin3012/claude-mind --private --source=. --push
```

- [ ] **Step 2: Commit and push playbook updates**

```bash
cd ~/Projects/Claude-Playbook
git add docs/
git commit -m "docs: cognitive architecture spec, migration doc, and review reports"
git push
```

---

## Execution Order Summary

| Task | What | Depends On | ~Minutes |
|---|---|---|---|
| 1 | Create claude-mind repo | Nothing | 2 |
| 2 | Write CLAUDE.md | Task 1 | 5 |
| 3 | Write Consciousness (6 files) | Task 1 | 15 |
| 4 | Write Rules (10 files) | Task 1 | 15 |
| 5 | Migrate Skills (4 skills + 1 procedure) | Task 1 | 10 |
| 6 | Write Consolidator Agent | Task 1 | 5 |
| 7 | Write empty plugins dir | Task 1 | 1 |
| 8 | Write plugins.txt | Task 1 | 2 |
| 9 | Write deploy.sh | Tasks 2-8 | 10 |
| 10 | Write rollback.sh | Task 1 | 5 |
| 11 | Test in playbook harness | Tasks 2-10 | 10 |
| 12 | Deploy to ~/.claude/ | Task 11 | 5 |
| 13 | Clean up old architecture | Task 12 | 5 |
| 14 | Push to GitHub | Task 13 | 2 |

**Tasks 2-8 can run in parallel** (all depend only on Task 1).
**Total estimated compute: ~90 minutes.**
