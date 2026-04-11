# Claude Code Native Ecosystem Audit

**Date:** 2026-04-10
**Session:** 2026-04-10-4d94
**Purpose:** Understand Claude Code's native primitives (skills, subagents, agent teams, hooks, settings, MCP) so the iq-locker harness layer aligns with proven, tested functionality. Second of two audits; pairs with `claude-flow-capability-audit.md`.
**Method:** Three parallel Explore subagents, each fetching and navigating `code.claude.com/docs/en/*` and Anthropic's "Building Effective Agents" post. Findings verified against local `.claude/skills/` contents and the SKILL-LOCKER-PATTERN.md spec.

## Executive summary

**Four headline findings, in order of impact:**

1. **iq-locker's SKILL.md convention is compatible with — and should adopt — Claude Code's native SKILL.md format.** Native skills use YAML frontmatter (`name`, `description`, optional `triggers`, `allowed-tools`, `model`, `context`, etc.), live in `.claude/skills/<name>/SKILL.md`, and already implement progressive disclosure: descriptions load at startup (~200 chars each), full content loads only on invocation. SKILL-LOCKER-PATTERN.md is silent on frontmatter format, so adopting native is additive, not a contradiction. **This is load-bearing: it means iq-locker is a curation layer on top of native skills, not a parallel system.**

2. **The invocation protocol the locker prescribes (MANIFEST-first → SKILL.md → resources) extends what's native rather than replacing it.** Native Claude Code already decides *when* to invoke skills based on descriptions and context. The locker's MANIFEST adds a *human curation layer* (active/dormant split, promotion triggers, trust boundaries, explicit subagent bootstrapping) that native skills don't provide. MANIFEST.md is not a native concept — it's the iq-locker contribution.

3. **Agent teams are a new, experimental multi-session primitive** — **not** the right default choice. They require `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`, coordinate multiple independent sessions (not subagents in one session), carry significant coordination overhead and token cost, and work best for parallel independent work (research, multi-angle review, competing hypotheses). For most iq-locker workflows, **the native `Task`/`Agent` tool spawning a single subagent is still the default** — agent teams are an occasional tool for specific scenarios.

4. **Subagents (via `Task`/`Agent` tool) and agent team members both inherit project-level skills from disk but NOT the parent's conversation history.** This reinforces assumption A1 from the brief and the Claude Flow audit finding. Spawned contexts get CLAUDE.md, MCP servers, and skills from disk at startup — so a locker with SKILL.md files in `.claude/skills/` is automatically visible to every spawned subagent and teammate without any extra wiring. The MANIFEST-read protocol still matters *for curation* (which skills to activate) and *for teaching the subagent what to read when*, but the physical file availability is handled natively.

## The updated composition model

Refined from the Claude Flow audit's stack model. Claude Code's native layer is more substantial than the original model gave it credit for.

```
┌───────────────────────────────────────────────────────┐
│  Curation & policy (iq-locker)                        │
│  MANIFEST.md (active/dormant, triggers, trust),       │
│  CLAUDE.md entry-point signal, wire/sync scripts,     │
│  domain-specific content, locker boundaries           │
├───────────────────────────────────────────────────────┤
│  Content (native Claude Code skills)                  │
│  SKILL.md with YAML frontmatter + optional resources/ │
│  Progressive disclosure built-in (description →       │
│  full content on invocation). Works with slash        │
│  commands, subagent inheritance, auto-discovery.      │
├───────────────────────────────────────────────────────┤
│  Execution primitives (native Claude Code)            │
│  Task/Agent tool (subagents), agent teams             │
│  (experimental), hooks (8 event types),               │
│  CLAUDE.md loading, MCP discovery, settings           │
├───────────────────────────────────────────────────────┤
│  Orchestration & memory (Claude Flow)                 │
│  Swarms, claims/handoffs, HNSW memory,                │
│  autopilot, diagnostic primitives                     │
├───────────────────────────────────────────────────────┤
│  Execution primitives (Claude Flow + MCP)             │
│  Analyze, aidefence, performance, github, browser,    │
│  terminal, ruvllm, WASM                               │
└───────────────────────────────────────────────────────┘
```

**The shift from the original model:** native Claude Code occupies the middle *and* owns the content format. The locker layer shrinks in scope — it's no longer "content + curation" but *just* curation + policy + boundaries + entry-point teaching. That's a meaningfully smaller ask with a meaningfully larger leverage.

## Subsystem findings

### 1. Native skills (the foundation)

**Format.** Native skills live at `.claude/skills/<name>/SKILL.md` (project) or `~/.claude/skills/<name>/SKILL.md` (user). YAML frontmatter has a required `name` (≤64 chars) and `description` (≤1024 chars, should include *what* and *when*), plus optional fields:
- `triggers` — explicit trigger phrases
- `allowed-tools` — permission allowlist
- `model` — preferred Claude model
- `disable-model-invocation` — make it user-invocable only
- `user-invocable` — expose as a slash command
- `context` — e.g. `fork` to spawn in a child context
- `paths` — glob patterns for file-scoped auto-triggering
- `version` — for curation/drift tracking (non-standard but widely used)

**Progressive disclosure is built-in.** Only the frontmatter (including the description) loads into context at session start. The full SKILL.md body loads only when the skill is explicitly invoked or auto-matched. Supporting files in `resources/` load only when the skill itself reads them. **This is exactly what the locker pattern prescribes — and it's free.**

**Slash command integration.** A skill with a defined name is automatically available as `/skill-name`. Arguments can be passed via `$ARGUMENTS` placeholder in the body. Skills are the preferred mechanism over the legacy `.claude/commands/` directory (commands are deprecated but backward-compatible).

**Empirical ground-truth from iq-locker's current state:** the project has 30 skills in `.claude/skills/` from Claude Flow init (agentdb-*, reasoningbank-*, swarm-*, github-*, browser, skill-builder, etc.). Inspecting `skill-builder/SKILL.md` confirms native frontmatter (`name`, `description`). Inspecting `browser/SKILL.md` confirms extended fields (`triggers`, `tools`, `version`). The iq-locker project *already hosts* native-format skills; it just doesn't own any of them yet.

**Composition verdict for iq-locker:** **adopt the native format unconditionally.** Any iq-locker-authored SKILL.md file should use native frontmatter. This buys auto-discovery, slash commands, progressive disclosure, and subagent inheritance for free.

### 2. Native subagents (`Task` / `Agent` tool)

**Mechanism.** A single session delegates to a subagent via the `Task`/`Agent` tool. The subagent runs in an isolated context window, receives a self-contained spawn prompt, works autonomously, and returns a final summary message (verbatim) to the parent. Subagents cannot spawn other subagents.

**Built-in types.** Claude Code ships with three:
- `general-purpose` — balanced tools, no specialization
- `Explore` — read-only (Glob, Grep, Read) for research
- `Plan` — read-only for analysis and planning without implementation

Custom types live in `.claude/agents/<type>.md` with YAML frontmatter (`name`, `description`, `tools`, `model`). iq-locker already has 98 agent files from Claude Flow init, available as custom subagent types when the Agent tool supports them.

**Context isolation — what a subagent sees on spawn:**
- The spawn prompt
- Project and user CLAUDE.md files
- Auto memory `MEMORY.md` (first 200 lines / 25KB)
- MCP server tool definitions (loaded on demand)
- **Native skill descriptions at startup; full content loads on invocation**
- Built-in tools per the subagent type's allowlist

**What a subagent does NOT see:**
- Parent conversation history
- Parent tool results or file reads
- Session-scoped temporary permissions
- Parent's current working state (subagent sees disk state only)

**The five workflow patterns** (from Anthropic's "Building Effective Agents" and echoed in the docs):
- **Prompt chaining** — fixed sequential decomposition for known paths
- **Routing** — classify input, direct to specialized handler
- **Parallelization** — independent branches (sectioning) or redundant runs (voting)
- **Orchestrator-workers** — central LLM dynamically delegates when subtasks are unpredictable
- **Evaluator-optimizer** — generator + critic loop for quality-sensitive work

**Anti-patterns Anthropic explicitly calls out:**
- Vague spawn prompts without success criteria
- Passing large file contents in prompts (tell the subagent which files to read instead)
- Expecting subagents to report interactively — they work autonomously, return once
- Using subagents for sequential coordination (use chaining or orchestrator-workers instead)
- Building custom orchestration when native delegation fits

**Composition verdict for iq-locker:** the locker's subagent-bootstrapping concern is mostly a non-issue at the filesystem layer — spawned subagents auto-see SKILL.md descriptions. The remaining concerns that the MANIFEST-read protocol addresses:
- **Curation.** Which skills are active vs dormant for this project? Native doesn't track this; MANIFEST does.
- **Trust boundaries.** Which skills are vendored upstream vs self-authored vs client-confidential? Native doesn't track this; MANIFEST does.
- **Promotion discipline.** When should a dormant skill become active? Native doesn't track this; MANIFEST does.
- **Cross-skill references.** Which skills share a `common/` layer? Native skills can reference siblings, but there's no curated index; MANIFEST provides one.
- **Entry-point teaching for CLAUDE.md.** "This locker exists, here's the protocol" — native provides no convention for this; the locker pattern does via the CLAUDE.md snippet.

The locker remains valuable. It just doesn't need to invent content format or progressive disclosure.

### 3. Agent teams (experimental, use carefully)

**What they are.** A coordination primitive (Claude Code v2.1.32+) that manages multiple *independent* Claude Code sessions working together — not subagents inside one session, but fully separate sessions with their own context windows and permissions. A lead session spawns teammates, each with its own session, sharing a task list and supporting direct inter-teammate messaging (bypassing the lead).

**Experimental status.** Disabled by default. Requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Docs explicitly warn that they "add coordination overhead and use significantly more tokens than a single session." Not the default choice.

**When to use them** (from the docs):
- Parallel research / multi-angle investigation
- New modules or features where teammates own separate files
- Competing-hypotheses debugging (each teammate tests a theory)
- Cross-layer coordination (frontend / backend / tests each owned separately)
- Large code reviews split by criteria (security / performance / coverage)

**When NOT to use them:**
- Sequential tasks (use chaining or a single session)
- Same-file edits across teammates (causes overwrites — docs explicitly warn)
- Work with many cross-dependencies (coordination cost exceeds benefit)
- Routine tasks (token cost scaling makes single sessions cheaper)

**Configuration.** Agent teams are **not pre-authored files.** You create them dynamically in natural language ("create an agent team to..."); the lead spawns teammates, references `.claude/agents/` definitions when picking teammate types, manages a shared task list. Runtime state lives in `~/.claude/teams/<name>/config.json` and `~/.claude/tasks/<name>/` — *do not edit by hand*; it's overwritten on state updates. There is no project-level team format to commit.

**Skill inheritance.** Teammates load project context (CLAUDE.md, MCP servers, skills) at spawn time, same as a regular session or subagent. They don't inherit the lead's conversation history. **The locker's CLAUDE.md entry-point signal reaches them automatically** — same mechanism as subagents.

**Composition verdict for iq-locker:** agent teams are a **sometimes-tool**, not the foundation. They're worth understanding and having a lightweight "team-spawn" skill prepared for the few workflows where they earn their cost. They don't change the locker design. Important implication: if a locker's content is relevant to multi-teammate workflows, it must be *discoverable at spawn time*, which means the CLAUDE.md entry-point signal needs to be terse enough to be useful but specific enough to teach each teammate what to do.

**Open questions from the docs that need empirical testing before committing to team-heavy workflows:**
- How are skill descriptions loaded into each teammate's context — all at once, or lazily?
- How does the lead verify teammate task success vs. false "done" signals?
- Can teammates use `isolation: worktree`? Docs don't say.
- Can agent teams be resumed after lead shutdown?

### 4. Native hooks (distinct from Claude Flow hooks)

**Event taxonomy** (native to Claude Code, fires regardless of Claude Flow):

*Once per session:* `SessionStart`, `SessionEnd`
*Once per turn:* `UserPromptSubmit`, `Stop`, `StopFailure`, `PreCompact`, `PostCompact`
*Per tool call:* `PreToolUse` (highest power: allow/deny/ask/defer, modify input), `PermissionRequest`, `PermissionDenied`, `PostToolUse`
*Subagent lifecycle:* `SubagentStart`, `SubagentStop`
*Task lifecycle:* `TaskCreated`, `TaskCompleted`
*Filesystem/environment:* `FileChanged`, `CwdChanged`, `WorktreeCreate`, `WorktreeRemove`
*Misc:* `Notification`, `InstructionsLoaded`

**Contract.** Hooks are shell commands configured in `.claude/settings.json`. Exit 0 = success, 2 = blocking error, other = non-blocking. Hooks can return JSON with `hookSpecificOutput` to mutate behavior (allow/deny a tool call, modify input, replace MCP tool output, inject context).

**Relationship to Claude Flow hooks.** The 7 hook types Claude Flow enabled in iq-locker's settings.json on init are *native Claude Code hook slots* wired to Claude Flow's `hook-handler.cjs` helper. They're not a separate system — Claude Flow is *using* the native hook mechanism as its execution layer. This means adding custom iq-locker hooks requires coexistence with Claude Flow's handlers via the matcher system. Order and matcher specificity matter.

**Composition verdict for iq-locker:** native hooks are the right integration seam for any locker-aware runtime behavior. Specifically:
- A `SubagentStart` hook could inject a MANIFEST path into the subagent's spawn prompt (if the CLAUDE.md entry-point signal alone isn't enough).
- A `PostToolUse` hook on skill invocations could record which locker skills were used, into Claude Flow memory (the integration seam identified in the Claude Flow audit).
- A `SessionStart` hook could validate MANIFEST freshness and warn on stale curation.

But the anti-pattern from the Claude Flow audit still applies: **don't auto-load skills via `PreToolUse`**. Agent judgment stays at the agent level.

### 5. CLAUDE.md conventions

**Loading model.** Walk up the directory tree from cwd; load `CLAUDE.md` and `CLAUDE.local.md` at each level. Nested CLAUDE.md files in subdirectories load on-demand when Claude reads files in those subdirs. This means a locker can have its own nested CLAUDE.md at `docs/<locker>/CLAUDE.md` that activates only when agents are working in that subtree — a form of scoping that matches the locker pattern's "one CLAUDE.md entry-point per locker" convention.

**Scopes:**
- `./CLAUDE.md` or `./.claude/CLAUDE.md` — project-level
- `~/.claude/CLAUDE.md` — user-level (global across projects)
- `./CLAUDE.local.md` — local (gitignored, not committed)
- Enterprise-managed policy files exist but are out of scope

**Composition verdict:** the locker's CLAUDE.md entry-point snippet is already aligned with native conventions. The nested-CLAUDE.md auto-loading is a *bonus* iq-locker should exploit: per-locker scoping becomes trivial.

### 6. Settings, MCP, slash commands (quick hits)

- **Settings.** `.claude/settings.json` fields relevant to the locker: `hooks`, `permissions` (allow/deny for tools, skills, file paths), `statusLine`, `env`. Claude Flow extends settings with its own `claudeFlow` namespace; those extensions don't affect the locker.
- **MCP.** `.mcp.json` declares MCP servers; tools are auto-namespaced `mcp__<server>__<tool>`. Skills can specify `allowed-tools` to grant permission to specific MCP tools. **Locker skills that wrap MCP tools should document their target tool names and version expectations explicitly** — API stability is not guaranteed across MCP server versions.
- **Slash commands.** Legacy `.claude/commands/` still works but skills with `user-invocable` are the current way. Don't build slash commands as a separate system for iq-locker.

## Updated integration guidance for iq-locker

The Claude Flow audit identified integration seams; this audit identifies *native* integration points that should take priority.

### Adopt wholesale

1. **Native SKILL.md format.** YAML frontmatter with `name`, `description`, and whichever extended fields are relevant. No custom locker format.
2. **`.claude/skills/<name>/SKILL.md` location.** Project-level skills; user-level for cross-project.
3. **Native progressive disclosure.** Description loads at startup; body on invocation. No reinvention.
4. **Nested CLAUDE.md for per-locker scoping.** Place a locker's entry-point snippet at `docs/<locker>/CLAUDE.md` so it auto-loads only when relevant. Potentially more elegant than a top-level CLAUDE.md section.
5. **Native subagent inheritance.** Don't worry about "how will spawned subagents see the skills" — they will, automatically, at the description level.
6. **Built-in subagent types as defaults.** `general-purpose`, `Explore`, `Plan` cover most spawning needs. Custom types in `.claude/agents/` for specialized personas (the fabric-toolkit pattern is already this).

### Add on top (the iq-locker contribution)

1. **MANIFEST.md curation layer.** Active/dormant tracking, promotion triggers, trust boundaries, cross-skill reference index. Not a native concept.
2. **Top-level entry-point signal in CLAUDE.md.** "This project uses lockers at X, Y, Z — protocol is MANIFEST-first."
3. **Wire/sync scripts for vendored-upstream lockers.** Symlinks into `.claude/` for active-set exposure; sync script for pulling upstream updates without clobbering local curation. Borrowed from the fabric-toolkit reference implementation.
4. **Trust / provenance boundaries.** Separate lockers per trust zone, aligned to git boundaries.
5. **Locker-specific promotion discipline.** "Last curated" dates, review cadences, MANIFEST freshness guards.

### Defer / out of scope

1. **Agent teams as a default orchestration pattern.** Keep `Task`/`Agent` subagents as the default. Reach for teams only when parallel independent work is clearly the right shape.
2. **Custom runtime for skill invocation.** Native handles this. Don't build a router.
3. **Custom CLAUDE.md loading.** Native handles hierarchical loading. Use nested CLAUDE.md files instead of building something new.
4. **Custom slash command system.** Skills handle this.
5. **Pre-task hooks that auto-load skills.** Anti-pattern (violates judgment-at-agent-level). Covered in the Claude Flow audit.

### Empirically test before committing

1. **Agent team skill discovery behavior.** Do teammates see skill descriptions in context at startup, or only on explicit invocation? This affects whether agent-team workflows need a different MANIFEST protocol.
2. **Nested CLAUDE.md triggering.** Does `docs/fabric-toolkit/CLAUDE.md` actually load when an agent reads files in that subtree? Or only at session start?
3. **Subagent inheritance of locker-specific CLAUDE.md.** Does a spawned subagent see the nested CLAUDE.md, or only the top-level one?

## Impact on iq-locker brief

### Resolved / clarified

- **Q8 (harness portability line)** → partially resolved. Content layer = native SKILL.md (portable to any harness that adopts the Anthropic skill format). Harness layer = CLAUDE.md signal, MANIFEST curation tooling, wire/sync scripts, nested CLAUDE.md scoping. Clean boundary.
- **Scope of the iq-locker contribution** → narrowed and clarified. Previously: "file-based skill locker with progressive disclosure." Now: "curation + trust + promotion + entry-point layer on top of native Claude Code skills." Smaller scope, higher leverage.

### Validated assumptions

- **A1** (subagents don't auto-inherit skill registrations in a conversation-history sense, and the MANIFEST-read protocol is still necessary) → reinforced for a *third* time. Native subagents and agent team members inherit *file-based* skill availability but not *conversation-level* discovery. MANIFEST curation remains necessary for the "which active, which dormant, which trust zone" questions.
- **A6** (Claude Code primary, portable via harness-vs-content separation) → strengthened. Native SKILL.md format is the Anthropic spec; adopting it maximizes portability to any harness that supports it, while the MANIFEST layer stays harness-agnostic markdown.

### New assumptions to add

- **A9:** Native Claude Code skills (SKILL.md with YAML frontmatter at `.claude/skills/<name>/SKILL.md`) are the correct content format for iq-locker. The locker does not invent a new format. — *status: validated by this audit.*
- **A10:** Agent teams are an occasional tool for parallel-independent-work workflows, not the default orchestration pattern. Default stays single-subagent spawning via the `Task`/`Agent` tool. — *status: assumed, based on docs and experimental flag.*

### New Design Rebase Queue entries

- If Anthropic finalizes a formal SKILL.md spec (frontmatter schema, reserved fields): audit iq-locker skills against it.
- If agent teams graduate from experimental and their cost profile improves: reassess whether any recurring workflow benefits enough to justify team-first design.
- If Claude Code ships a native MANIFEST-equivalent curation feature: evaluate whether to retire iq-locker's MANIFEST layer in favor of it.

### New open question

- **Q10: nested CLAUDE.md per-locker scoping — adopt or stay with top-level entry-point snippet?** Nested is more elegant (auto-loads only when relevant) but less visible (easy for a developer new to the project to miss). Top-level is more discoverable but loads unconditionally. Decision should probably come during first-instance locker design, not now.

## Cross-cutting open questions after both audits

With both Claude Flow and Claude Code native audits complete, a few remaining questions cut across both:

1. **SKILL.md front-matter for Claude Flow integration.** The Claude Flow audit suggested optional fields like `model-preference`, `capability-area`, `diagnostic-tools-invoked` that Claude Flow could consume. Some of these overlap with native Claude Code frontmatter (`model`, `allowed-tools`). Prefer native fields where they exist; add locker-specific fields in a namespaced sub-key (e.g. `iq-locker:` namespace) for anything else, if needed at all.
2. **Superpowers integration revisited.** Superpowers skills are already native-format Claude Code skills (they had to be, to work as they do). So the "vendor / reference / leave-alone" question simplifies: superpowers skills *are* a locker already, just not one you curate. The real question is whether iq-locker's MANIFEST convention should cross-reference them or leave them untouched. Lean: leave untouched, reference from CLAUDE.md only if relevant.
3. **Open Mind / skill locker composition.** Open Mind already uses native skills for its slash commands (`om-go`, `om-save`, etc.). Adding iq-locker skills is purely additive at the file layer — no conflict, no special handling needed.

---

*Three parallel Explore subagents produced this audit. Findings verified by direct inspection of local `.claude/skills/` contents and SKILL-LOCKER-PATTERN.md. Agent teams content is from `code.claude.com/docs/en/agent-teams`. Re-run if Claude Code releases a major version that changes skill or agent team semantics.*
