# Claude Code Official Setup Reference

> Compiled from official Anthropic documentation at code.claude.com/docs (March 2026).
> Cross-referenced across multiple doc pages for accuracy.

---

## 1. Directory Structure: What Goes in `~/.claude/`

### Auto-created by Claude Code

| Path | Purpose | Auto-created? |
|------|---------|---------------|
| `~/.claude/` | Root config directory | Yes, on first run |
| `~/.claude/settings.json` | User-level settings | Yes (or create manually) |
| `~/.claude/CLAUDE.md` | Global personal instructions | No, user creates |
| `~/.claude/rules/` | User-level path-scoped rules | No, user creates |
| `~/.claude/agents/` | User-level custom subagents | No, user creates |
| `~/.claude/skills/` | User-level personal skills | No, user creates |
| `~/.claude/commands/` | Legacy commands (still works) | No, user creates |
| `~/.claude/memory/` | Global user auto-memory | Yes, when auto-memory writes |
| `~/.claude/projects/` | Per-project data (sessions, auto-memory) | Yes |
| `~/.claude/projects/<project>/memory/` | Project auto-memory directory | Yes |
| `~/.claude/plugins/` | Plugin cache and marketplace data | Yes |
| `~/.claude/plugins/cache/` | Cached installed marketplace plugins | Yes |
| `~/.claude/plugins/local/` | Local plugins (loaded via `--plugin-dir`) | No, user creates |
| `~/.claude/backups/` | Timestamped config backups (5 most recent) | Yes |
| `~/.claude/sessions/` | Session data | Yes |
| `~/.claude/debug/` | Debug logs | Yes |
| `~/.claude/plans/` | Plan files (default location) | Yes |
| `~/.claude/history.jsonl` | Command history | Yes |
| `~/.claude/agent-memory/` | Subagent persistent memory (user scope) | Yes, when subagent writes |
| `~/.claude.json` | OAuth, preferences, MCP servers (user+local scope) | Yes |

### Key directory NOT in `~/.claude/`

- **`~/.claude.json`** (note: at home root, not inside `.claude/`) -- stores OAuth session, theme preferences, notification settings, MCP server configs for user and local scopes, per-project state, and caches.

### Per-project auto-memory path format

```
~/.claude/projects/<project>/memory/
  MEMORY.md          # Concise index (first 200 lines loaded per session)
  debugging.md       # Topic files Claude creates
  api-conventions.md # More topic files
```

The `<project>` path is derived from the git repository. All worktrees and subdirectories within the same repo share one auto-memory directory.

### Common mistake

- Putting MCP server configuration in `~/.claude/mcp.json` -- this is wrong. MCP servers go in `~/.claude.json` (user/local scope) or `.mcp.json` (project scope).

---

## 2. Plugins

### Official plugin structure

```
my-plugin/
  .claude-plugin/
    plugin.json            # Manifest (ONLY this goes in .claude-plugin/)
  commands/                # At plugin root, NOT inside .claude-plugin/
    status.md
  agents/                  # At plugin root
    security-reviewer.md
  skills/                  # At plugin root
    code-review/
      SKILL.md
  hooks/                   # At plugin root
    hooks.json
  .mcp.json                # MCP server definitions
  .lsp.json                # LSP server configs
  settings.json            # Default settings (only `agent` key supported)
  scripts/                 # Utility scripts
```

### plugin.json (the manifest)

The file is **`plugin.json`** inside `.claude-plugin/`. The manifest is optional -- if omitted, Claude Code auto-discovers components in default locations and derives the plugin name from the directory name.

Required field (if manifest exists): only `name`.

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Brief description",
  "author": { "name": "Your Name" },
  "homepage": "https://...",
  "repository": "https://...",
  "license": "MIT",
  "keywords": ["keyword1"],
  "commands": ["./custom/commands/special.md"],
  "agents": "./custom/agents/",
  "skills": "./custom/skills/",
  "hooks": "./config/hooks.json",
  "mcpServers": "./mcp-config.json",
  "lspServers": "./.lsp.json",
  "outputStyles": "./styles/"
}
```

Custom paths in plugin.json **supplement** default directories -- they don't replace them.

### How plugins are loaded

1. **Marketplace plugins**: Installed via `/plugin install`. Copied to `~/.claude/plugins/cache/`. Registered in `enabledPlugins` in settings.json.
2. **Local plugins via CLI**: `claude --plugin-dir ./my-plugin` (session-only). No installation needed.
3. **Local plugins via shell alias** (for persistent loading):
   ```bash
   alias claude='command claude --plugin-dir /path/to/plugin-a --plugin-dir /path/to/plugin-b'
   ```

### Plugin installation scopes

| Scope | Settings file | Use case |
|-------|--------------|----------|
| `user` (default) | `~/.claude/settings.json` | Personal plugins across all projects |
| `project` | `.claude/settings.json` | Team plugins shared via VCS |
| `local` | `.claude/settings.local.json` | Project-specific, gitignored |

### Common mistakes

- Putting `commands/`, `agents/`, `skills/`, `hooks/` inside `.claude-plugin/` -- only `plugin.json` goes there.
- Using `manifest.json` instead of `plugin.json` -- wrong filename.
- Using absolute paths in plugin.json -- all paths must be relative, starting with `./`.

---

## 3. Skills

### Official skill format

Skills live in a directory with a `SKILL.md` file as the entrypoint:

```
my-skill/
  SKILL.md           # Required
  template.md        # Optional supporting file
  examples/
    sample.md        # Optional
  scripts/
    validate.sh      # Optional
```

The file **must** be named `SKILL.md` (uppercase).

### SKILL.md frontmatter (all fields optional)

```yaml
---
name: my-skill                      # Display name; defaults to directory name
description: What this skill does   # Recommended. Claude uses this to decide when to use it
argument-hint: "[issue-number]"     # Hint shown during autocomplete
disable-model-invocation: true      # Prevent Claude from auto-loading. Default: false
user-invocable: false               # Hide from / menu. Default: true
allowed-tools: Read, Grep, Glob     # Tools allowed without permission when skill is active
model: sonnet                       # Model override
context: fork                       # Run in isolated subagent
agent: Explore                      # Which agent type when context: fork
hooks:                              # Lifecycle hooks scoped to this skill
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/check.sh"
---

Your skill instructions in markdown here...
```

### Where skills live

| Location | Path | Scope |
|----------|------|-------|
| Personal | `~/.claude/skills/<skill-name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<skill-name>/SKILL.md` | This project only |
| Plugin | `<plugin>/skills/<skill-name>/SKILL.md` | Where plugin is enabled |

Priority: enterprise > personal > project. Plugin skills use `plugin-name:skill-name` namespace.

### Skills vs commands

Commands (`.claude/commands/deploy.md`) and skills (`.claude/skills/deploy/SKILL.md`) both create `/deploy` and work the same way. Your existing commands files keep working. Skills add: a directory for supporting files, frontmatter options, and auto-loading by Claude. If both exist with the same name, the skill takes precedence.

### String substitutions in skills

| Variable | Description |
|----------|-------------|
| `$ARGUMENTS` | All arguments passed when invoking |
| `$ARGUMENTS[N]` or `$N` | Specific argument by 0-based index |
| `${CLAUDE_SESSION_ID}` | Current session ID |
| `${CLAUDE_SKILL_DIR}` | Directory containing the SKILL.md |

### Common mistakes

- Using `skill.md` (lowercase) -- must be `SKILL.md`.
- Creating a `<name>.md` file instead of `SKILL.md` in a directory.
- Missing `description` -- without it, Claude doesn't know when to auto-load the skill.

---

## 4. MCP Servers

### Configuration locations

| Scope | Location | Who sees it | Shared? |
|-------|----------|-------------|---------|
| Local (default) | `~/.claude.json` (under project path) | You, this project only | No |
| Project | `.mcp.json` at project root | All collaborators | Yes (VCS) |
| User | `~/.claude.json` (global section) | You, all projects | No |
| Managed | `managed-mcp.json` in system dirs | All users on machine | IT-deployed |

### Adding MCP servers

```bash
# HTTP remote server (recommended for cloud services)
claude mcp add --transport http <name> <url>

# SSE remote server (deprecated, use HTTP)
claude mcp add --transport sse <name> <url>

# Local stdio server
claude mcp add --transport stdio --env KEY=value <name> -- <command> [args...]

# From JSON
claude mcp add-json <name> '<json>'

# Specify scope
claude mcp add --transport http --scope user <name> <url>    # all projects
claude mcp add --transport http --scope project <name> <url>  # shared .mcp.json
claude mcp add --transport http --scope local <name> <url>    # default, private
```

### .mcp.json format (project-scoped)

```json
{
  "mcpServers": {
    "server-name": {
      "command": "/path/to/server",
      "args": [],
      "env": {}
    }
  }
}
```

Supports environment variable expansion: `${VAR}` and `${VAR:-default}` in `command`, `args`, `env`, `url`, and `headers`.

### ~/.claude.json structure

This file stores MCP servers alongside other data (OAuth, preferences, per-project state). MCP servers for user and local scopes live here. The exact internal structure is managed by Claude Code -- use `claude mcp add` rather than editing manually.

### Scope precedence

Local > Project > User. When servers share the same name, the higher-priority scope wins.

### Managed MCP paths

- macOS: `/Library/Application Support/ClaudeCode/managed-mcp.json`
- Linux/WSL: `/etc/claude-code/managed-mcp.json`
- Windows: `C:\Program Files\ClaudeCode\managed-mcp.json`

### Common mistakes

- Putting MCP config in `~/.claude/mcp.json` -- does not exist. Use `~/.claude.json` or `.mcp.json`.
- Confusing "local" scope (stored in `~/.claude.json` per-project) with "local settings" (`.claude/settings.local.json`).
- Not using `--` separator before stdio server commands, causing flag conflicts.

---

## 5. Memory

### Two complementary systems

| | CLAUDE.md files | Auto memory |
|---|---|---|
| **Who writes** | You | Claude |
| **Contains** | Instructions and rules | Learnings and patterns |
| **Scope** | Project, user, or org | Per working tree / per project |
| **Loaded** | Every session (full) | Every session (first 200 lines of MEMORY.md) |
| **Use for** | Coding standards, architecture | Build commands, debugging insights, preferences |

### Auto memory storage

```
~/.claude/projects/<project>/memory/
  MEMORY.md          # Index, first 200 lines loaded every session
  debugging.md       # Topic files, loaded on demand
  api-conventions.md
```

- `<project>` derived from git repository path
- All worktrees/subdirectories in same repo share one auto-memory directory
- Machine-local, not shared across machines

### MEMORY.md behavior

- First 200 lines loaded at session start
- Content beyond line 200 is NOT loaded at startup
- Claude keeps it concise by moving details to topic files
- Topic files are read on demand, not at startup

### Managing auto memory

- Toggle: `/memory` command, or `autoMemoryEnabled` setting, or `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`
- Custom directory: `autoMemoryDirectory` in user or local settings (NOT project settings for security)
- Browse/edit: `/memory` command or edit files directly (plain markdown)

### Common mistakes

- Expecting MEMORY.md beyond 200 lines to be loaded -- it's not.
- Confusing `~/.claude/memory/` (user-level auto memory when NOT in a git repo) with `~/.claude/projects/<project>/memory/` (project-level).
- Saving project-specific memories from `~` (home directory) -- they'll load in every session started from home.

---

## 6. CLAUDE.md

### Locations and scope

| Scope | Location | Purpose | Shared? |
|-------|----------|---------|---------|
| Managed policy | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md` | Org-wide, cannot be excluded | IT-deployed |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Team instructions | Yes (VCS) |
| User | `~/.claude/CLAUDE.md` | Personal preferences | No |

### How CLAUDE.md loads

1. Walks UP the directory tree from cwd, loading CLAUDE.md at each level
2. Subdirectory CLAUDE.md files load on demand when Claude reads files there
3. User `~/.claude/CLAUDE.md` always loaded
4. Additional directories via `--add-dir` do NOT load CLAUDE.md unless `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1`

### .claude/rules/ directory

For modular instructions. Each `.md` file covers one topic:

```
.claude/
  CLAUDE.md
  rules/
    code-style.md
    testing.md
    security.md
```

Rules without `paths` frontmatter load at startup. Path-scoped rules load on demand:

```yaml
---
paths:
  - "src/api/**/*.ts"
---
# API rules here
```

User-level rules: `~/.claude/rules/` (loaded before project rules).

### @import syntax

```markdown
See @README for project overview and @package.json for available commands.
@~/.claude/my-project-instructions.md
@docs/git-instructions.md
```

Relative paths resolve relative to the file containing the import. Max depth: 5 hops.

### Best practices

- Target under 200 lines per CLAUDE.md file
- Use markdown headers and bullets
- Be specific ("Use 2-space indentation" not "Format code properly")
- Run `/init` to auto-generate a starting CLAUDE.md
- Use `claudeMdExcludes` setting to skip irrelevant files in monorepos

### Common mistakes

- Making CLAUDE.md too large (>200 lines reduces adherence)
- Contradicting instructions across multiple CLAUDE.md files
- Expecting CLAUDE.md to be enforced like config -- it's context, not configuration

---

## 7. Settings

### settings.json vs settings.local.json

| File | Location | Shared? | Purpose |
|------|----------|---------|---------|
| `~/.claude/settings.json` | User home | No | Personal global settings |
| `.claude/settings.json` | Project | Yes (VCS) | Team settings |
| `.claude/settings.local.json` | Project | No (gitignored) | Personal project overrides |

### Scope precedence (highest to lowest)

1. **Managed** -- cannot be overridden
2. **Command line arguments** -- session overrides
3. **Local** (`.claude/settings.local.json`)
4. **Project** (`.claude/settings.json`)
5. **User** (`~/.claude/settings.json`)

### Key settings

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": ["Bash(npm run lint)"],
    "deny": ["Read(./.env)"],
    "defaultMode": "default"
  },
  "hooks": { ... },
  "env": { "FOO": "bar" },
  "model": "claude-sonnet-4-6",
  "autoMemoryEnabled": true,
  "autoMemoryDirectory": "~/custom-memory",
  "claudeMdExcludes": ["**/monorepo/CLAUDE.md"],
  "enabledPlugins": ["plugin-name@marketplace"],
  "worktree": {
    "symlinkDirectories": ["node_modules"],
    "sparsePaths": ["packages/my-app"]
  }
}
```

### What goes where

- **User settings** (`~/.claude/settings.json`): Personal preferences, global allow/deny rules, plugins you use everywhere
- **Project settings** (`.claude/settings.json`): Team permissions, hooks, MCP server approvals, shared plugins
- **Local settings** (`.claude/settings.local.json`): Personal overrides for this project, testing configs, machine-specific settings

### Common mistakes

- Confusing `settings.json` (Claude Code settings) with `~/.claude.json` (OAuth/MCP/preferences store)
- Editing `~/.claude.json` manually for MCP -- use `claude mcp add` instead

---

## 8. Hooks

### Configuration locations

| Location | Scope | Shared? |
|----------|-------|---------|
| `~/.claude/settings.json` | All projects | No |
| `.claude/settings.json` | This project | Yes (VCS) |
| `.claude/settings.local.json` | This project | No |
| Plugin `hooks/hooks.json` | When plugin enabled | Yes |
| Skill/agent frontmatter | While component active | Yes |

### Hook events (24+)

**Session**: `SessionStart`, `SessionEnd`, `InstructionsLoaded`
**User input**: `UserPromptSubmit`
**Tool execution**: `PreToolUse`, `PermissionRequest`, `PostToolUse`, `PostToolUseFailure`
**Agents**: `SubagentStart`, `SubagentStop`, `Stop`, `TeammateIdle`, `TaskCompleted`
**Config**: `ConfigChange`, `Notification`
**Worktree**: `WorktreeCreate`, `WorktreeRemove`
**Context**: `PreCompact`, `PostCompact`
**MCP**: `Elicitation`, `ElicitationResult`

### Hook types

1. **`command`** -- Shell script, receives JSON on stdin
2. **`http`** -- POST to a URL
3. **`prompt`** -- Single-turn LLM evaluation
4. **`agent`** -- Spawn a subagent with tools

### Configuration format

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/format.sh",
            "timeout": 600
          }
        ]
      }
    ]
  }
}
```

### Exit codes for command hooks

- **0**: Success
- **2**: Blocking error (blocks tool call, denies permission, etc.)
- **Other**: Non-blocking error

### Plugin hooks format

File: `hooks/hooks.json` at plugin root:

```json
{
  "description": "Code formatting hooks",
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format.sh"
          }
        ]
      }
    ]
  }
}
```

### Common mistakes

- Not making hook scripts executable (`chmod +x`)
- Case-sensitive event names (`PostToolUse`, not `postToolUse`)
- Forgetting `${CLAUDE_PLUGIN_ROOT}` for plugin-relative paths

---

## 9. Worktrees

### Built-in worktree support

```bash
# Create worktree and start Claude in it
claude --worktree feature-auth
# or auto-name:
claude --worktree
```

- Creates at `<repo>/.claude/worktrees/<name>/`
- Branch named `worktree-<name>`
- Branches from default remote branch

### Cleanup behavior

- **No changes**: worktree and branch removed automatically
- **Changes exist**: Claude prompts to keep or remove

### Subagent worktrees

Add `isolation: worktree` to subagent frontmatter. Each subagent gets its own worktree, auto-cleaned when it finishes without changes.

### Worktree settings

```json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".cache"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  }
}
```

### Non-git VCS

Use `WorktreeCreate` and `WorktreeRemove` hooks to provide custom worktree logic for SVN, Perforce, Mercurial, etc.

### Best practice

Add `.claude/worktrees/` to your `.gitignore`.

### Common mistakes

- Not running `npm install` (or equivalent) in new worktrees
- Forgetting to add `.claude/worktrees/` to `.gitignore`

---

## Summary: What's Auto-Created vs User-Created

### Auto-created by Claude Code

- `~/.claude/` directory
- `~/.claude.json`
- `~/.claude/projects/` and per-project subdirectories
- `~/.claude/projects/<project>/memory/MEMORY.md` (when auto-memory writes)
- `~/.claude/plugins/cache/`
- `~/.claude/backups/`
- `~/.claude/sessions/`
- `~/.claude/debug/`
- `~/.claude/history.jsonl`
- `.claude/settings.local.json` (auto-gitignored when created)

### User-created

- `~/.claude/CLAUDE.md` (global instructions)
- `~/.claude/settings.json` (user settings)
- `~/.claude/rules/` (user-level rules)
- `~/.claude/skills/` (personal skills)
- `~/.claude/agents/` (personal subagents)
- `~/.claude/commands/` (legacy, still works)
- `.claude/CLAUDE.md` or `./CLAUDE.md` (project instructions)
- `.claude/settings.json` (project settings)
- `.claude/rules/` (project rules)
- `.claude/skills/` (project skills)
- `.claude/agents/` (project subagents)
- `.mcp.json` (project-scoped MCP servers)

---

## Quick Setup Checklist (Clean Start)

1. Install Claude Code: `curl -fsSL https://claude.ai/install.sh | bash`
2. Run `claude` once to authenticate and create `~/.claude/`
3. Create `~/.claude/CLAUDE.md` with personal preferences
4. Create `~/.claude/settings.json` with personal settings
5. Add MCP servers: `claude mcp add --transport http --scope user <name> <url>`
6. Per project: run `/init` to generate project CLAUDE.md
7. Per project: create `.claude/settings.json` for team settings
8. Add `.claude/worktrees/` and `.claude/settings.local.json` to `.gitignore`
