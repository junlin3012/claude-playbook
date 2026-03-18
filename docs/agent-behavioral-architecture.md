# Agent Behavioral Architecture

How the cognitive architecture shapes agent behavior from session start to self-improvement.

## The Full Behavioral Loop

```
SESSION STARTS
    │
    ├── Hook fires: "read Consciousness/INDEX.md"     ← ENFORCED (can't skip)
    ├── CLAUDE.md loads (37 lines, bootstrap)          ← AUTOMATIC (Claude Code native)
    ├── All 10 rules/ load                             ← AUTOMATIC (Claude Code native)
    ├── MEMORY.md first 200 lines load                 ← AUTOMATIC (Claude Code native)
    ├── Superpowers hook loads skill discovery          ← ENFORCED (plugin hook)
    ├── Explanatory mode hook loads output style        ← ENFORCED (plugin hook)
    │
    ▼
AGENT IS NOW "AWAKE"
    │
    │   At this point the agent has:
    │   - Core values (L0: think before acting, relationships > objects)
    │   - Behavioral guardrails (triangulate facts, memory isolation, skill discipline)
    │   - Cost estimation format
    │   - Knowledge of all installed skills
    │   - Memory of past sessions with this user
    │
    ▼
USER SENDS A MESSAGE
    │
    ├── Hookify PreToolUse fires on every action       ← GUARDRAIL
    ├── Security hook fires before every file edit      ← GUARDRAIL
    │
    ├── MANDATORY: Check if a skill applies             ← mandatory-skills.md
    │   ├── Building something? → brainstorming first
    │   ├── Bug? → systematic-debugging first
    │   ├── Multi-step? → writing-plans first
    │   ├── About to claim done? → verification first
    │   └── Exhaustive review? → mirror-polish-protocol
    │
    ├── Before writing code, THREE-LAYER INTROSPECTION  ← L1-cognition.md
    │   ├── Layer 1: What is this thing?
    │   ├── Layer 2: How does it relate to everything?
    │   └── Layer 3: What am I not seeing?
    │
    ├── DECLARE MODE                                    ← L3-modes.md
    │   ├── POC → fast, pragmatic, tech debt accepted
    │   ├── Integration → platform-idiomatic, production quality
    │   └── Recreation → extract technique, discard architecture
    │
    ├── For any factual claim: TRIANGULATE              ← triangulate-facts.md
    │   └── Never guess. Search first. Cross-reference.
    │
    ├── For any string literal: HARDCODED CHECK         ← L2-principles.md
    │   └── "Would this value differ for another customer?"
    │
    ├── For any platform work: INTEGRATION CHECK        ← L1 + L2
    │   └── "Does this already exist in the platform?"
    │
    ▼
AGENT DOES THE WORK
    │
    ├── Every file edit → security hook checks it       ← ENFORCED
    ├── Every tool call → hookify checks rules          ← ENFORCED
    │
    ▼
WORK COMPLETE
    │
    ├── Cost estimate appended                          ← cost-estimate.md
    │
    ▼
BETWEEN SESSIONS (the learning loop)
    │
    ├── User corrects the agent?
    │   └── Save as feedback memory                     ← self-improvement.md
    │       └── Same correction 3+ times?
    │           └── Promote to rules/                   ← consolidation.md
    │               └── Universal + failure-tested + actionable?
    │                   └── Promote to Consciousness    ← L4-evolution.md
    │
    ├── Memory getting stale?
    │   └── >30 days, no relevance?
    │       └── Propose deletion to user                ← forgetting.md
    │
    ├── Memory duplicates a rule?
    │   └── Delete immediately                          ← consolidation.md step 3
    │
    ▼
NEXT SESSION STARTS
    │
    └── Everything learned is now permanent
        (rules load automatically, Consciousness shapes thinking)
```

## Three Enforcement Levels

The architecture doesn't just suggest behavior — it enforces it at three levels:

| Level | Mechanism | Can the agent skip it? | Example |
|-------|-----------|----------------------|---------|
| **Hard enforcement** | Hooks (shell scripts) | No — runs before the agent sees the message | Security check on every file edit, Consciousness reminder at session start |
| **Soft enforcement** | Rules (markdown loaded at startup) | Technically yes, but they're in context | "Never guess", "check skills first", "memory isolation" |
| **Philosophical shaping** | Consciousness (loaded on demand) | Yes, but CLAUDE.md tells it to read them | Three-layer introspection, relational thinking, mode awareness |

Hooks catch mistakes **even when the agent forgets**. Rules remind the agent **before it starts**. Consciousness shapes **how it thinks about the problem**.

## Self-Improvement Pipeline

```
Birth:     User corrects the agent → memory saved
Growth:    Pattern detected (same correction 3+ times)
Promotion: Becomes a permanent rule in rules/
Maturity:  Becomes a Consciousness principle (L2)
Death:     Original memory deleted (rule is source of truth)
```

### What triggers each stage

| Stage | Trigger | Document |
|-------|---------|----------|
| Birth | User says "no, don't do that" | self-improvement.md |
| Growth | Agent notices multiple memories on same topic | self-improvement.md pattern detection |
| Promotion | Criteria met: repetition + universality + verified | consolidation.md |
| Maturity | Criteria met: universal + failure-tested + actionable | L4-evolution.md |
| Death | Memory now duplicates the rule | forgetting.md + consolidation.md step 3 |

### Regression resistance

L4-evolution.md requires ALL THREE criteria for a principle to enter Consciousness:
1. **Universal** — applies across projects
2. **Learned from failure** — proven by real mistake
3. **Actionable** — changes concrete behavior

This prevents emotional or one-off corrections from overwriting proven principles.

## Comparison: Vanilla Claude Code vs. This Architecture

| Capability | Vanilla | This Architecture |
|-----------|---------|-------------------|
| Remembers past sessions | Basic flat memory | Hierarchical memory with promotion pipeline |
| Learns from corrections | No | feedback → pattern → rule → principle |
| Thinks about how it thinks | No | L1 introspection, L3 mode awareness |
| Prunes stale knowledge | No | Forgetting rules, consolidation reviews |
| Evolves its own principles | No | L4 with strict acceptance criteria |
| Platform-aware coding | Generic | "Integration over recreation" as core value |
| Enforced behavior | CLAUDE.md only | Hooks + rules + Consciousness (3 levels) |

## Known Limitations

1. **Self-improvement is not spontaneous** — triggered by user corrections, not self-detected errors
2. **Introspection is prompted, not genuine** — L1 makes the agent behave as if it introspects, but it's structured discipline, not consciousness
3. **Consolidation requires invocation** — the consolidator agent must be asked to review; it doesn't run automatically
4. **No inter-session continuity** — each message within a session starts fresh; the architecture provides context but not true continuity of thought
