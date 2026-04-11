# Claude Flow Capability Audit

**Date:** 2026-04-10
**Session:** 2026-04-10-4d94
**Purpose:** Resolve iq-locker brief open question #6 ("Claude Flow composition") before picking a first-instance locker.
**Method:** Five parallel subagents, each auditing one subsystem slice, fetching the ruflo README (https://github.com/ruvnet/ruflo), exploring the local `.claude-flow/` + `.claude/` install, and empirically calling read-only MCP tools. Each slice delivered a composition verdict independently; this doc synthesizes their findings.

## Executive summary

**Verdict: COMPLEMENTARY with specific integration seams. Not overlapping in a way that should change the locker's scope, and not replaceable by any Claude Flow capability.**

All five audit slices arrived at the same top-level verdict independently. Claude Flow and the skill locker operate at genuinely different layers:

- **Claude Flow** = runtime orchestration, auto-learned memory, lifecycle hooks, diagnostic primitives, and execution plumbing. "How does work flow through agents, and what gets learned from it?"
- **Skill locker** = curated, human-authored domain knowledge with explicit invocation. "What does the agent need to know before it starts the task, and how does it find it?"

The most important finding is that **spawned subagents inside Claude Flow still don't auto-load skills** — WASM gallery agents start as blank slates, swarm agents don't inherit parent skill registrations, and Claude Flow's routing layer stops at agent-type resolution, never at skill content. This *validates* the load-bearing insight from `SKILL-LOCKER-PATTERN.md`: the MANIFEST-read invocation protocol is still necessary regardless of which orchestration layer sits underneath it.

**Impact on iq-locker brief:** Assumption A7 (Claude Flow and skill locker are complementary layers) is now validated. Open question #6 is resolved. Three of the candidate first-instance lockers (IQ brand, quality-gating, agentic foundation) remain viable; the audit adds weight to quality-gating by showing it composes cleanly with Claude Flow's diagnostic primitives.

## The composition model

Think of this as a layered stack. Each layer does one thing well and the layers above/below it treat it as a stable interface.

```
┌─────────────────────────────────────────────────────┐
│  Curated domain knowledge (iq-locker)               │
│  SKILL.md files, MANIFEST routing, CLAUDE.md entry  │
│  Human-authored, version-controlled, portable       │
├─────────────────────────────────────────────────────┤
│  Agent practice & invocation (Claude Code + iq)     │
│  MANIFEST-first protocol, agent judgment, spawn     │
├─────────────────────────────────────────────────────┤
│  Orchestration & memory (Claude Flow)               │
│  Swarm topology, claims/handoffs, HNSW memory,      │
│  hooks lifecycle, auto-learned patterns             │
├─────────────────────────────────────────────────────┤
│  Execution primitives (Claude Flow + MCP)           │
│  Terminal, browser, github, analyze, aidefence,     │
│  performance, ruvllm, WASM                          │
└─────────────────────────────────────────────────────┘
```

The locker sits at the top (content + curation). Claude Flow occupies the middle and bottom (orchestration + primitives). Agent practice is the glue — the invocation protocol that makes curated content reachable by code flowing through orchestration.

**The single most important "what layer does what" split:**

| Concern | Layer |
|---------|-------|
| What does an agent need to *know* before starting? | Skill locker |
| What *agent type* should handle this task? | Claude Flow (`hooks_route`, `guidance_recommend`) |
| Which *model* is cost-effective for this task? | Claude Flow (`hooks_model-route`) |
| How do agents coordinate across a swarm? | Claude Flow (claims, topology, consensus) |
| What patterns emerged from execution? | Claude Flow (SONA, ReasoningBank, HNSW memory) |
| What curated domain policy applies? | Skill locker |
| What diagnostic facts are true about this diff? | Claude Flow (`analyze_*`, `aidefence_*`, `performance_*`) |

## Subsystem findings

### 1. Orchestration (agents, swarms, hive-mind, workflows, claims, coordination)

**Capability:** Multi-layered agent spawning and coordination system with hierarchical-mesh topology (default), 15-agent swarms, 60+ agent role types, Byzantine/Raft/Gossip/CRDT/Quorum consensus options, claim/steal/handoff work distribution, and workflow DAGs with parallel/sequential/conditional/loop step types. Hive-mind is an *optional* collective-decision layer; most coordination happens through claims + task assignment. **Empirical reality:** in the current install, the swarm is configured but empty (0 agents, 0 tasks). Infrastructure is ready; nothing auto-runs.

**Composition verdict:** **Complementary.** No overlap on core primitives. The locker does content delivery; this layer does agent lifecycle. Specific synergy points:
- Flow's `hooks_route` / `guidance_recommend` dispatch to agent *types*; a locker MANIFEST could fire on top to load *domain knowledge* into the chosen agent's context.
- The claims system is an implicit execution audit trail — if a skill is invoked during a claimed task, that invocation can be recorded as claim metadata for later analysis.
- Workflow DAGs are static templates, not learned routes. A quality-gating locker could define gate sequences (lint → test → security → perf → review) as workflow templates Claude Flow executes.

**Things to know:**
- Agents auto-spawn sub-workers; nesting is opaque to the swarm orchestrator (Flow only sees top-level claims).
- Load balancing needs CPU/memory/task-queue metrics; without them, rebalancing is blind.
- There is **no built-in skill reader** in this layer — you'd wire one via a hook or via the agent's own system prompt.

### 2. Memory & intelligence (the highest-stakes slice)

**Capability:** Unified vector-backed semantic memory built on AgentDB with three tiers (working/episodic/semantic), HNSW indexing (150×–12,500× faster than brute-force), SONA neural adaptation, ReasoningBank strategy optimization, and a MemoryGraph (PageRank + community detection, max 5000 nodes). A four-step pretraining pipeline (retrieve → judge → distill → consolidate) can ingest codebase content into semantic patterns. Confidence scoring with decay (+0.03 per access, −0.005/hour) and automatic consolidation above a threshold. **Empirical reality:** fresh install; 0 patterns, 0 learned strategies, state INITIALIZING despite today's pretrain run over 5 files.

**Composition verdict:** **Complementary at the layer, but with substantial conceptual adjacency.** Both systems curate domain knowledge for agents. The difference is the mechanism and the trust model:

| | Skill locker | Claude Flow memory |
|---|---|---|
| Authoring | Human-written SKILL.md | Auto-extracted from execution |
| Routing | MANIFEST.md (human-curated) | Semantic search / HNSW / hyperbolic |
| Agent interaction | Explicit file reads | Passive context injection via hooks |
| Portability | Plain markdown, harness-agnostic | Bound to project's AgentDB |
| Human-readable | Yes | No (embeddings + patterns) |
| Version-controlled | Yes (git) | No (runtime state) |
| Decay | No (static files) | Yes (confidence scoring) |

**The key question — could SKILL.md content just live in Claude Flow memory?** Technically yes via `hooks pretrain` or `memory_import_claude`. But that collapses the locker's core value props: loses human readability, loses version control, loses portability across harnesses, loses the MANIFEST as a human synchronization artifact, and binds knowledge to a runtime database that has to be rebuilt on every fresh clone.

**Correct model:** files are the source of truth. Claude Flow memory is a complementary layer that learns from execution *about* which skills worked well — it doesn't replace the skills themselves.

**Things to know:**
- **Terminology collision:** Claude Flow uses "pattern" for both (a) auto-learned insights from SONA/ReasoningBank and (b) curated stored knowledge. The locker's "skill" vocabulary needs explicit namespacing in any shared context: `skill:iq-brand:typography` vs `pattern:learned:model-routing`.
- **There is no MANIFEST equivalent in Claude Flow.** Semantic routing is a different primitive. Keep MANIFEST as the human curation artifact.
- **Integration seam:** a post-task hook could store skill invocations as structured metadata in Claude Flow memory, building a learned "which skills worked for which task shapes" index *on top of* the file-based locker.
- **No first-class skill type in Claude Flow** — just "agents" and "patterns." Skills remain a locker-native concept.

### 3. Hooks & lifecycle

**Capability:** Declaratively-configured lifecycle interception layer wired through `.claude/settings.json` as shell commands. Seven hook types (PreToolUse, PostToolUse, SessionStart, SessionEnd, SubagentStart/Stop, Notification, Stop/PreCompact) fire on events. A secondary routing engine (`hooks_route`, `hooks_model-route`) is *callable* — it uses HNSW similarity to suggest agent types and model tiers but does not auto-fire. Autopilot is a re-engagement loop that respawns task contexts when work remains incomplete. **Empirical reality:** 7 hook types are enabled in iq-locker's settings.json from today's init.

**Composition verdict:** **Complementary, and there's an explicit anti-pattern to avoid.** Hooks are agent-centric routing (which agent? which model?), not domain-centric skill loading.

**Anti-pattern: do NOT auto-load skills via pre-task hooks.** The audit agent specifically flagged this. Three reasons:
1. It violates the locker's judgment-at-agent-level property. The agent should *decide* which skills apply, not have them force-fed.
2. It duplicates routing logic. Claude Flow already routes to agent types; adding skill-level routing on top creates two competing layers.
3. Hooks fire on lifecycle events (post-edit, session-start), not on task analysis. Skill invocation is task-analysis-time.

**Correct integration:** let agents read the MANIFEST and invoke skills themselves. Use *post*-task hooks to record what skills they chose and what worked — that's read-after-write knowledge sharing for the swarm, not automatic skill loading.

**Things to know:**
- Hooks respond with JSON (`continue: true|false`, warnings) — they can block tool execution but cannot mutate spawn parameters.
- `worker-detect` / `worker-dispatch` are mentioned in architecture docs but not callable as MCP tools — that's a gap if you were hoping for skill-triggered worker dispatch.
- Model-routing uses "tiny-dancer-neural" inference (~512μs). A skill could carry a `model-preference` field and a custom pre-task hook could pass hints to `hooks_model-route`.

### 4. Analysis, quality & guidance

**Capability:** Five tool families (analyze/aidefence/guidance/performance/progress) delivering diff risk classification, PII/prompt-injection scanning, task-to-capability routing across 16 capability areas, performance profiling, and progress tracking. **This is a recommendation and diagnostic layer, not an enforcement layer.** Risk scores are 0–100 but domain-agnostic; guidance returns workflow templates (bugfix, feature, refactor, testing, security, performance, release, swarm) but not domain-specific policies.

**Composition verdict:** **Complementary, and this strengthens the quality-gating locker candidate.** Claude Flow provides the diagnostic primitives; the locker provides the policy layer on top.

**What Claude Flow does well:**
- Diff risk scoring and classification (feature/bugfix/refactor/security/perf/docs/test/chore)
- PII detection and prompt-injection scanning (`aidefence_scan`, `is_safe`, `has_pii`)
- Performance bottleneck identification (`performance_bottleneck`, `performance_profile`)
- Task-to-capability routing (`guidance_recommend`)

**What Claude Flow explicitly does NOT do:**
- Define domain-specific quality policies ("no merge with >50% untested code", "security review required for cryptographic changes")
- Enforce gates (it can *recommend* a reviewer, not *block* a merge)
- Orchestrate test suites (performance metrics are runtime-only, not test-coverage-aware)
- Coordinate review workflows ("wait for 2 approvals", "escalate if reviewers disagree")
- Provide domain-specific quality rules (e.g., "Spark notebooks must have markdown structure validated")

**Integration seam for a quality-gating locker:** a quality skill invokes `analyze_diff-risk`, `aidefence_scan`, `performance_metrics`, and `guidance_recommend` as diagnostic inputs, then layers domain policy on top. This is a clean compositional boundary.

### 5. Peripherals & integrations

**Capability:** Heterogeneous plumbing layer covering GitHub (issue_track, pr_manage, repo_analyze, workflow), browser automation (open/click/fill/snapshot/screenshot), terminal sessions (stateful, named), a transfer store for distributing patterns/plugins (IPFS-backed, signature-verified), WASM agent gallery (isolated sandboxes with virtual filesystems), RuVLLM (HNSW routing, SONA adaptation, MicroLoRA fine-tuning), config management, and system introspection. **Empirical reality:** transfer store is immature — some calls returned errors; the gallery is builtin but not extensible via MCP.

**Composition verdict:** **Complementary, and reinforces the locker's value.**

**The most important finding in this slice:** WASM gallery agents spawn as *blank slates* with only CLI tools and file I/O. They have no built-in mechanism to inherit parent skill registrations or auto-load knowledge. If you want a WASM agent to use iq-locker content, you must either document the locker in the agent's system instructions or pass MANIFEST.md path as a startup parameter — which is exactly the pattern the locker already prescribes via the CLAUDE.md entry-point signal. **Claude Flow's architecture actively validates the locker pattern's necessity.**

**Things to know:**
- **No naming conflict:** Claude Flow uses "pattern" and "plugin" vocabulary. "Skill" is reserved by Claude Code (global skills registry, skill-creator). The locker's SKILL.md files don't collide.
- **Transfer store could eventually distribute lockers** as a pattern/plugin package, but it's not production-grade yet. Defer.
- **GitHub/terminal/browser skills already exist as shipped examples** in `.claude/skills/`. They demonstrate the "skill wraps MCP tools" pattern — useful reference for writing iq-locker skills that wrap Claude Flow primitives.
- **Skills referencing specific MCP tool names couple the locker to Claude Flow's API stability.** Worth noting an API-stability convention in the locker (e.g., skills document target MCP version).

## Key integration seams (consolidated)

Concrete hooks where iq-locker content can plug into Claude Flow capabilities:

1. **Skills invoke diagnostic primitives.** A quality-gating or security-review skill directly calls `mcp__claude-flow__analyze_diff-risk`, `aidefence_scan`, `performance_metrics`, `guidance_recommend` as inputs. No reimplementation. This is the cleanest seam.

2. **Post-task hooks record skill invocations.** Use `hooks_post-task` (or a custom post-edit hook) to store `{ task, skills_used, outcomes }` into Claude Flow memory. Subsequent agents can query via `memory_search` to learn which skills worked for which task shapes. Read-after-write knowledge sharing.

3. **Claims metadata as skill audit trail.** When a Claude Flow agent claims a task, the claim record is a natural place to attach which locker skills it consulted. Enables later analysis of skill effectiveness per task type.

4. **Workflow templates encode skill sequences.** A quality-gating locker's MANIFEST can define gate sequences (lint → test → security → perf → review) as Claude Flow workflow templates. Workflow executes; each stage invokes a skill; each skill invokes Claude Flow diagnostics. Three layers compose without duplication.

5. **Pretrain as optional enhancement, not replacement.** `hooks pretrain` *could* consume SKILL.md files to produce a semantic index. This would let agents find skills by fuzzy query instead of reading MANIFEST first. Treat as an optional enhancement for projects with many skills; files remain the source of truth.

6. **Model routing with skill hints.** Skills can carry a `model-preference` field. A custom pre-task hook reads it and passes hints to `hooks_model-route`. Cheap optimization.

## Anti-patterns to avoid

1. **Don't auto-load skills via pre-task hooks.** Violates judgment-at-agent-level, duplicates routing, wrong lifecycle moment. Let agents read MANIFEST and decide.
2. **Don't collapse SKILL.md content into Claude Flow memory as the primary store.** Loses human readability, version control, portability, and the MANIFEST curation artifact. Files are canonical.
3. **Don't duplicate Claude Flow routing logic at the skill level.** Agent-type routing is Flow's job. Skill content is the locker's. Two layers, two jobs.
4. **Don't treat the transfer store as a mature distribution channel for lockers.** Not yet. Keep lockers per-project for now.
5. **Don't build custom worker-detect / worker-dispatch logic assuming those MCP tools work** — they're architecture-documented but not callable.
6. **Don't conflate "pattern" (Claude Flow) with "skill" (iq-locker).** Namespace explicitly in any shared context.

## Impact on iq-locker brief

### Resolved open questions

- **Q6 (Claude Flow composition)** → resolved as **complementary layers**. Specific integration seams documented in this audit. Locker stays file-based, CLAUDE.md entry-point signal stays necessary, MANIFEST stays the human curation artifact.

### Validated assumptions

- **A1** (subagents don't auto-inherit skills) → reinforced. Claude Flow's WASM agents are explicit blank slates; swarm agents don't auto-load skills either. The MANIFEST-read protocol is still necessary, regardless of which orchestration layer sits underneath.
- **A7** (Claude Flow and skill locker are complementary layers) → validated across all five audit slices.

### Updated candidate evaluation

- **Quality-gating locker** → *strengthened*. Claude Flow provides strong diagnostic primitives (`analyze_*`, `aidefence_*`, `performance_*`) but explicitly no policy enforcement. A quality-gating locker is additive, not redundant. Cleanest composition story of the three candidates.
- **IQ brand/design system locker** → *unchanged*. Content is stable, cross-project, frontend-agent-friendly. No Claude Flow overlap at all (brand knowledge is not in-scope for any Claude Flow subsystem). This is the "safest" candidate.
- **Agentic foundation skills locker** → *unchanged but with a note*. Some overlap risk with Claude Flow's `guidance_*` capabilities (task-to-capability routing, workflow templates) — not a collision, but the foundation skills locker should cover gaps that `guidance_*` doesn't, not duplicate its "which agent type handles this" routing.

### New working guidance for locker design

- **Harness layer discipline becomes concrete.** The "content vs harness layer" split from the brief now has specific referents: Claude Flow lifecycle hooks, CLAUDE.md entry snippet, symlinks into `.claude/`, wire/sync scripts — all of these belong in the harness layer. SKILL.md content stays Claude-Flow-agnostic.
- **Terminology namespacing is required.** Any content that references "pattern" or "skill" must be explicit about which system's vocabulary it's using. Suggested convention: `iq:skill:*` for locker content, leave `pattern:*` to Claude Flow's auto-learned content.
- **API stability convention needed.** Skills that invoke `mcp__claude-flow__*` tools should document a target version so breakage is traceable.

## Open follow-up questions (new)

1. **Should SKILL.md files carry front-matter metadata that Claude Flow can consume?** E.g., `model-preference`, `capability-area`, `diagnostic-tools-invoked`. Would enable some integration seams without coupling the content to Claude Flow.
2. **Post-task hook for skill invocation tracking — in scope for iq-locker v1 or deferred?** The integration seam is real, but it's also feature creep for a first-instance locker. Defer until we have a locker to track.
3. **Does the agentic foundation skills candidate need a gap analysis against Claude Flow's `guidance_*` capabilities?** If `guidance_recommend` already covers "task decomposition" adequately, the foundation locker shrinks. Worth a follow-up investigation before picking that candidate.

---

*This audit was produced by five parallel Explore subagents on 2026-04-10. Empirical data was gathered via read-only MCP tool calls against the local Claude Flow install. Discrepancies between documented and empirical behavior are noted per-slice. Re-run this audit if Claude Flow moves more than one minor version.*
