# Autonomous Cognitive Reflection — Design Spec

**Date**: 2026-03-18
**Author**: Jun Lin + Claude
**Status**: Approved — pending implementation

---

## 1. Problem Statement

The cognitive architecture's self-improvement pipeline currently requires user-triggered corrections. The agent cannot detect its own cognitive failures — only the user saying "no, don't do that" initiates learning. This means:

- Subtle thinking pattern failures go undetected unless the user notices
- The architecture improves reactively (user corrects) instead of proactively (agent reflects)
- Deep cognitive patterns (reasoning approach, assumption habits, philosophical framing) are never examined

## 2. Goals

1. **Autonomous reflection** — the agent examines its own thinking patterns after each session
2. **Depth over surface** — reflect on HOW the agent thought, not WHAT it got wrong (variable names, syntax)
3. **Dynamic weighting** — focus reflection on whichever cognitive lenses are most relevant to the session
4. **User-gated promotion** — insights are proposed, never auto-committed to Consciousness
5. **Intelligent scaling** — trivial sessions get minimal reflection; deep sessions get full analysis

## 3. Design

### 3.1 Components

| Component | Location | Type | Purpose |
|-----------|----------|------|---------|
| L5-reflection.md | `Consciousness/L5-reflection.md` | New Consciousness layer | Defines the reflection philosophy, 4 lenses, 3 presentation formats |
| cognitive-reflection skill | `skills/cognitive-reflection/SKILL.md` | New skill | `/reflect` command for manual invocation mid-session |
| Stop hook | `settings.json` hooks.Stop | New hook entry | Auto-triggers reflection at session end |

### 3.2 Architecture

```
SESSION ENDS (or user types /reflect)
    │
    ├── Assess session substance (1-5 scale)
    │
    ├── Scale 1-2 (trivial):
    │   → "No notable cognitive patterns this session."
    │
    ├── Scale 3 (moderate):
    │   → Brief 2-3 sentence observation, no proposal
    │
    ├── Scale 4-5 (substantial):
    │   → Full reflection through 4 lenses
    │   → 3-layer presentation (summary → depth → Socratic)
    │   → Proposal: "Should this enter Consciousness?"
    │
    └── If user approves:
        → Update appropriate L-file via L4 pipeline
        → Update INDEX.md
        → Sync to claude-mind repo
```

### 3.3 The Four Cognitive Lenses

Each reflection examines the session through all four, dynamically weighted by what happened:

| Lens | What it examines | Weighs heavily when... |
|------|-----------------|----------------------|
| **Reasoning patterns** | Deductive vs inductive leaps, conclusion-jumping, problem space exploration, correlation/causation confusion | Debugging, architecture, complex decisions |
| **Relational thinking quality** | Did the agent trace L1 relationships or operate on isolated objects? Integration vs recreation trap? | Platform work, codebase changes, system design |
| **Assumption archaeology** | Unverified assumptions about intent, complexity, problem framing, mental models matching reality | Misunderstandings occurred, user corrected direction |
| **Philosophical approach** | Right mode (POC/Integration/Recreation)? Right optimization target? Solving the real problem or a phantom? | Mode wasn't declared, scope crept, user seemed frustrated |

### 3.4 The Three Presentation Layers (combined)

Every substantial reflection uses all three, layered together:

**Layer 1 — One-line summary (always shown)**
> "I treated a design problem as a translation problem."

**Layer 2 — Progressive depth (read as deep as you want)**
> Observation → Chain of thought → Root cognitive pattern

**Layer 3 — Socratic proposal (for Consciousness-worthy insights)**
> The agent frames the insight as a principle, explains why it's universal/failure-tested/actionable, and asks a question that tests whether the insight survives scrutiny.

**Layer 4 — Clinical analysis table**
> Structured: Lens | Finding | Severity for each of the 4 lenses.

### 3.5 Substance Assessment Heuristic

The Stop hook evaluates session substance to scale reflection depth:

| Signal | Score |
|--------|-------|
| <5 tool calls, no code written | 1 (trivial) |
| Simple Q&A, file reads, git ops | 2 (light) |
| Code writing or moderate debugging | 3 (moderate) |
| Architecture decisions, multi-step plans, user corrections | 4 (substantial) |
| Major feature work, debugging failures, philosophical discussion | 5 (deep) |

### 3.6 Examples of Deep vs Shallow Reflection

**Shallow (what we DON'T want):**
> "I used a variable name that was unclear. Should I add a naming convention to L2?"

**Deep (what we DO want):**
> "I approached the Shopify theme migration as a translation problem — converting Dawn patterns to Horizon equivalents 1:1. But the deeper issue: I was treating two different design philosophies as equivalent because they share vocabulary ('sections', 'blocks', 'settings'). I confuse shared terminology with shared semantics. This is a relational thinking failure — I pattern-matched on naming instead of tracing how each system's composition model actually works. L1 already warns about this at line 34 ('Am I assuming relationships based on naming?') but it's buried. Should naming-semantic confusion be elevated to a standalone principle in L2?"

### 3.7 User Interaction Flow

```
┌─────────────────────────────────────────────────────────┐
│  COGNITIVE REFLECTION                                    │
│─────────────────────────────────────────────────────────│
│                                                          │
│  [ONE-LINE SUMMARY]                                      │
│  "I treated a design problem as a translation problem."  │
│                                                          │
│  [PROGRESSIVE DEPTH]                                     │
│  Observation: ...                                        │
│  Chain of thought: ...                                   │
│  Root cognitive pattern: ...                             │
│                                                          │
│  [CLINICAL ANALYSIS]                                     │
│  | Lens | Finding | Severity |                           │
│  | Relational | ... | High |                             │
│  | Assumptions | ... | High |                            │
│  | Reasoning | ... | Medium |                            │
│  | Philosophical | ... | Medium |                        │
│                                                          │
│  [SOCRATIC PROPOSAL]                                     │
│  This is an instance of [broader pattern]. It would      │
│  refine [specific layer] because [reason]. The question  │
│  is: [challenge that tests the insight's validity].      │
│                                                          │
│  Should this be promoted to Consciousness?               │
│─────────────────────────────────────────────────────────│
│  User: yes / no / refine                                 │
│  If yes → L4 pipeline → update L-file → INDEX.md →      │
│           sync claude-mind                               │
└─────────────────────────────────────────────────────────┘
```

## 4. Files to Change in ~/.claude/

### Create

| File | Content |
|------|---------|
| `Consciousness/L5-reflection.md` | Reflection philosophy: 4 lenses, dynamic weighting, 3 presentation formats, connection to L4 pipeline |
| `skills/cognitive-reflection/SKILL.md` | Manual `/reflect` skill — reads conversation, applies L5 framework, presents findings |

### Modify

| File | Change |
|------|--------|
| `Consciousness/INDEX.md` | Add L5 row to layer table |
| `Consciousness/L4-evolution.md` | "5-layer" → "6-layer", add L5 to layer criteria |
| `settings.json` | Add Stop hook that triggers reflection |

### Delete (user-approved)

| File | Why |
|------|-----|
| `memory/feedback_no_hardcoded_values.md` | Redundant with L2-principles.md + security.md |
| `memory/feedback_use_plugins.md` | Superseded by mandatory-skills.md |

### Update after deletion

| File | Change |
|------|--------|
| `memory/MEMORY.md` | Remove 2 entries pointing to deleted files |

## 5. Integration with Existing Architecture

### L4 Pipeline Connection

L5 reflection produces PROPOSALS. Proposals enter the existing L4 pipeline:
- Must meet: universal + failure-tested + actionable
- User must approve
- Agent updates the appropriate L-file
- INDEX.md updated (mandatory per L4)

### Regression Resistance

L5 proposals inherit L4's regression resistance:
- Emotional corrections can't overwrite proven principles
- The Socratic question format forces the user to engage critically
- The 250 IQ audience assumption means proposals must be intellectually rigorous

### Stop Hook Design

```json
{
  "Stop": [
    {
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Read Consciousness/L5-reflection.md. Assess this session's cognitive substance (1-5). If 4+, perform full reflection with all 4 lenses and 3 presentation layers. If 3, brief observation. If 1-2, one line. Present to user."
        }
      ]
    }
  ]
}
```

Note: The Stop hook type "prompt" does a single-turn LLM evaluation. It has access to the conversation context. This is ideal for reflection since it needs to see what happened in the session.

## 6. Known Limitations

1. **Prompt hook depth** — Stop hooks with type "prompt" are single-turn. Complex multi-step reflection may need the skill approach instead.
2. **Context after compaction** — If the session was compacted, the Stop hook may not have full conversation history to reflect on.
3. **Self-assessment accuracy** — The agent assessing its own substance score is self-referential. May need calibration over time.
4. **First-session cold start** — L5 has no prior reflections to learn from. Quality improves as Consciousness accumulates reflection-derived principles.

## 7. Inspiration

| Source | What we took |
|--------|-------------|
| Schön's "Reflective Practitioner" | Reflection-in-action vs reflection-on-action (L5 is reflection-on-action) |
| Kahneman's System 1/System 2 | The 4 lenses map to different cognitive failure modes |
| Socratic method | Proposal format: present insight + challenge it with a question |
| L4-evolution.md | Promotion criteria (universal, failure-tested, actionable) filter shallow insights |
