# iq-locker — Project Brief

## Overview

**Goal:** Build a right-sized framework for effective multi-agent coding workflows at the scale of a freelance developer, an individual contributor inside a small team, or a small-to-medium business development group. The framework should let me compose agent teams and swarms that are as smart as they need to be when they need to be — with progressive disclosure of skills, tools, and knowledge — without over-engineering for a scale I'll never operate at.

The work starts from a validated pattern (the **skill locker**, proven out in the T3 Hearst project on 2026-04-10) and uses Stripe's published minion architecture as a **North Star** — a source of design ideas to learn from, not a template to replicate.

## Philosophy & scope lens

- **Stripe is the ceiling, not the floor.** Their system handles ~1,300 PRs/week across hundreds of millions of lines of code with ~500 MCP tools in a centralized Toolshed. I will never operate at that scale. What I want from Stripe's work is the *shape* of the ideas — where they drew lines, what earned its complexity, what patterns generalize down.
- **Right-sized beats comprehensive.** Every piece of the framework needs to earn its keep at freelance/SMB scale. A locker is only worth building when content is dense enough, stable enough, and reused enough. A blueprint is only worth building when the determinism guarantees matter. An MCP tool curation layer is only worth building when I have enough tools that curation is a real problem.
- **Progressive disclosure is the core principle.** Don't cram everything into context. Index first, read what applies, skip the rest. This applies to skills, tools, rules, and orchestration alike.
- **Horizon awareness.** Design choices should serve me where I am now (solo, a few projects) while leaving a clean growth path for where I expect to be (more projects, possibly small teams, more complex agentic workflows). Don't over-invest in capabilities I'll use in 2 years when I need capabilities that work today.

## Current workflow & environment

The framework has to live inside the tools I actually use today. These are not permanent commitments — they're the environment I'm designing for right now, with awareness of where drift is likely.

### Primary harness: Claude Code

Claude Code is where most of my development happens today and is the **primary target**. Build Claude-Code-first.

That said, the framework should be **portable enough** that a future move to Codex, Gemini CLI, or another harness doesn't require a rewrite. Portability constraints:
- Core content (`SKILL.md` files, MANIFEST format, `common/` references) should be plain Markdown with no Claude-Code-specific syntax.
- Harness-specific glue (symlinks into `.claude/`, CLAUDE.md entry-point snippet, wire/sync scripts) should be isolated in a clearly labeled "harness layer" so it can be swapped or duplicated without touching content.
- Don't bake assumptions about a specific agent-spawning mechanism into skill content. The MANIFEST-read invocation protocol is harness-agnostic; keep it that way.

### Agentic enablement: Claude Flow (ruflo)

I use Claude Flow for agentic enablement — swarms, memory, MCP tools, hooks, session management. It's initialized in this project (`.claude-flow/`, `.claude/`, `.mcp.json`). Open question: **how does the skill locker framework compose with Claude Flow?** Three possibilities:

1. **Complementary (likely).** Claude Flow provides orchestration, memory, and tools; the locker provides curated domain knowledge. They operate at different layers and don't overlap. This is the default assumption.
2. **Overlapping in places.** Claude Flow has some skill/pattern/memory features that could overlap with locker content (e.g. pattern extraction, HNSW-indexed intelligence). Need to understand what Claude Flow already does before duplicating it.
3. **Partial replacement.** If Claude Flow's intelligence layer is doing something locker-adjacent well, maybe the locker focuses on the content-curation side and lets Claude Flow handle the routing/retrieval side. Worth exploring but not assuming.

**What I want to avoid:** building locker scaffolding that duplicates Claude Flow capability, or building something that fights Claude Flow conventions. Goal is *additive*, not competing.

### Existing skill packages: superpowers

I use parts of the superpowers skills package regularly — **brainstorming**, **subagent-driven development**, and **quality-gate / code-review** skills. These are already working for me and I don't want to replace them casually.

Open question: **where do superpowers skills fit in the locker model?** Options:

1. **Out of scope — they stay as installed skills.** Superpowers is already a curated package maintained by someone else; treating it as a vendored locker (like fabric-toolkit) would add ceremony without value. Just let it coexist.
2. **Vendored as a locker instance.** Pin a version of superpowers into a per-project `docs/superpowers-locker/` with a MANIFEST that marks which skills are active per project. Gets us per-project curation and the "promote when" trigger discipline, at the cost of extra maintenance.
3. **Referenced, not vendored.** Leave superpowers installed globally; the iq-locker MANIFEST just *references* which superpowers skills are relevant per-domain, without copying content. Lightweight cross-reference.

**What I want to avoid:** reinventing skills that already exist in superpowers. If brainstorming and subagent-driven-development are already solved well, the agentic foundation skills candidate shouldn't duplicate them — it should cover the *gaps* superpowers doesn't address, or codify my personal conventions on top of the shared base.

### Things to watch for as the framework evolves

- **Will any of my current tools become redundant?** If the framework absorbs a capability that Claude Flow or superpowers already provides, that's a sign I'm overbuilding — stop and use the existing thing.
- **Will any of my current tools need adaptation?** Some interaction points may need small tweaks — e.g. my CLAUDE.md may need to teach agents about *both* the skill locker entry-point *and* the Claude Flow hooks, without conflict.
- **Portability drift.** Every time I write a Claude-Code-ism into content (as opposed to the harness layer), I lose portability. Worth flagging during review.

## What's known (distilled from source material)

### The skill locker pattern (SKILL-LOCKER-PATTERN.md)

An on-disk, curated knowledge store consumed by spawned subagents via progressive disclosure. Five load-bearing parts:

1. **Content store** — files organized as skills with `SKILL.md` + optional `resources/`, plus a `common/` tree for cross-skill references.
2. **Curation index (MANIFEST.md)** — the routing catalog with active/dormant status and "promote when" triggers.
3. **Invocation protocol** — MANIFEST-first, then SKILL.md, then resources. Agent picks what applies; caller doesn't pre-compute.
4. **Entry-point signal in CLAUDE.md** — tells every spawned agent the locker exists and how to use it.
5. **(Optional) Specialized agents inside the locker** — personas with their own delegation maps.

Separation principles (three axes): provenance & update cadence · scope · trust & licensing. Locker boundaries must align with git boundaries and sharing boundaries.

**The load-bearing insight:** spawned subagents in Claude Code don't inherit parent-session skill registrations. The MANIFEST-read protocol isn't a nice-to-have — it's the workaround that makes skills reachable by subagents at all. If the platform ever auto-registers skills into subagents, the workaround becomes unnecessary but the curation/filtering/separation value remains.

### The T3 Hearst POC (validated instance)

Concrete implementation: `docs/fabric-toolkit/` — vendored `microsoft/skills-for-fabric` (MIT, pinned upstream). Structure:

- `upstream/` holds the full vendored content, synced via script.
- `.claude/skills/` holds symlinks to the **active subset only** (2 agents + 5 skills; 9 additional assets dormant with explicit promotion triggers).
- `MANIFEST.md` lists everything with status + purpose + promotion trigger.
- `scripts/fabric-toolkit-wire.sh` recreates symlinks after fresh clone (since `.claude/` is gitignored).
- `scripts/fabric-toolkit-sync.sh` pulls upstream updates without clobbering local curation.
- CLAUDE.md snippet teaches the invocation protocol explicitly.

Validation: controlled spawn test comparing blind baseline vs skill-loaded agent on the same design task. Skill-loaded output was materially more detailed, more concrete, and contained refinements traceable to specific skill references. Delta was large enough to move the pattern from "probably useful" to "demonstrably load-bearing" in a single test.

### The Stripe minion architecture (North Star reference)

Stripe's unattended coding agent system. Published architecture details that are relevant to my horizon:

- **Toolshed = centralized MCP server** (~500 tools) with curated per-agent subsets. This is a **tools registry**, not a file-based knowledge store — distinct from the skill locker pattern, though complementary. (Stripe's README reference to "tool shed" that inspired me was actually about this MCP layer, not skill files.)
- **Blueprints = workflows-with-agent-nodes hybrid.** Fixed graph with both deterministic nodes (lint, push, test) and agent-loop nodes (implement, fix-CI). Guarantees that non-negotiable steps always run; lets agent creativity operate inside contained boxes. Teams build custom blueprints for domain-specific flows (e.g. tricky LLM-assisted migrations).
- **Subdirectory-scoped rule files.** Don't cram rules globally; attach context as the agent traverses the filesystem. Cursor format, synced to Claude Code format. Same rule files work for humans and agents.
- **Shift feedback left.** Pre-push local hooks with cached heuristics catch most lint issues in <1s. CI is capped at 2 rounds max — diminishing returns past that.
- **Isolation as precondition.** Devboxes give parallelism, predictability, and isolation. Unattended agents only work safely when blast radius is contained.
- **What Stripe explicitly calls out:** "what's good for humans is good for agents" — investments in human developer productivity (rule files, devboxes, fast feedback) pay dividends in the agent world.

## Scope decisions

### In scope (initial)

**Post-audit refinement (2026-04-10):** iq-locker is a **curation + trust + promotion layer on top of native Claude Code skills**, not a new skill format. See `docs/plans/claude-code-native-audit.md` for the rationale. The locker's contributions are the parts native skills don't provide: MANIFEST curation, trust boundaries, promotion discipline, and CLAUDE.md entry-point teaching.

- Native Claude Code SKILL.md format as the content layer. No custom format.
- MANIFEST.md as the iq-locker-native curation index (active/dormant split, promotion triggers, trust boundaries, cross-skill reference index) — the part native skills don't provide.
- CLAUDE.md entry-point signal teaching spawned subagents about the locker's existence and protocol — the "discoverability" piece.
- At least one concrete locker instance for a real domain I work in (candidates: IQ brand/design system, quality-gating/test orchestration, agentic foundation).
- Right-sized curation conventions (MANIFEST format, active/dormant split, promotion triggers, trust-zone separation).
- Wire/sync scripts for vendored-upstream lockers (borrowed from the fabric-toolkit reference implementation).
- Cross-project portability — the framework should work across my various IQ-branded and client projects.

### Deferred / "maybe later" (explicit)

- **MCP tool curation layer** (the Stripe Toolshed analog). Defer until I actually have enough MCP tools that curation is a real problem. May never reach that threshold; if I do, revisit what a right-sized version looks like.
- **Blueprints / deterministic-agent-node orchestration.** Interesting pattern, possibly relevant for quality-gating workflows, but adds meaningful complexity. Defer until I have a concrete workflow that needs the determinism guarantees.
- **Global locker hosting.** SKILL-LOCKER-PATTERN.md flags this as an explicit open question. Per-project copies are fine until locker count grows past 2–3.
- **Unattended / one-shot agent mode.** Stripe's entire minion premise. My current workflow is human-in-the-loop. Not on the roadmap, but the framework shouldn't foreclose it.

### Out of scope (explicit)

- Replicating Stripe's scale-specific infrastructure (devboxes, central MCP registry for hundreds of agents, CI integration with millions of tests).
- Building a product / service for other people. This is for me, shared selectively with other independent developers if and when it's useful.

## Candidate first-instance lockers (from README brainstorm)

These are real candidates, not commitments. Evaluate each against: *is content dense enough? stable enough? reused enough? does progressive disclosure pay off?*

- **IQ brand & design system skills.** Shared styling, voice, component patterns across all IQ-branded apps. Well-suited to the locker pattern because content is stable, cross-project, and frontend agents clearly benefit. *Post-audit note (2026-04-10):* zero Claude Flow overlap — brand knowledge isn't in-scope for any Claude Flow subsystem. Safest candidate from a collision-risk standpoint.
- **Quality-gating / test orchestration skills.** Test orchestrator + writer + runner + feedback loop. May want blueprint-style determinism eventually; could start as a plain locker. *Post-audit note (2026-04-10):* **strengthened.** Claude Flow provides strong diagnostic primitives (`analyze_diff-risk`, `aidefence_scan`, `performance_metrics`, `guidance_recommend`) but explicitly no policy enforcement. A quality-gating locker invokes those as inputs and layers domain policy on top. Cleanest composition story of the three.
- **Agentic foundation skills.** Skills about the *practice* of agentic development itself — task decomposition, spawn discipline (when to delegate, how to write self-contained prompts), handoff protocols (continuation prompts, context compression), planning rhythm, review practices, quality-gate discipline. Packaged as skills so spawned subagents inherit the same working discipline I use. Most abstract of the three candidates (harder to measure impact) but most directly dog-foods the framework — every agentic workflow would benefit. *Post-audit note (2026-04-10):* needs gap analysis against Claude Flow's `guidance_*` capabilities before selection — some overlap risk on task-to-capability routing and workflow templates. Foundation locker should cover what `guidance_*` doesn't, not duplicate it.

## Open questions

1. **Scope the framework to file-based skills only, or include MCP tool curation?** Depends on whether my MCP tool count ever justifies curation. Current lean: file-based only.
2. **What's the minimum viable first-instance locker?** Need to pick one of the three candidates above and build it to validate the framework at freelance scale.
3. **Global vs per-project hosting** (inherited from SKILL-LOCKER-PATTERN.md). Per-project for now; revisit at 3+ lockers.
4. **How does the framework interact with Open Mind?** Open Mind already provides the session-memory and discovery layer. The locker pattern provides the skill-delivery layer. They should compose cleanly — likely via CLAUDE.md conventions that teach agents about both.
5. **What does portability look like?** SKILL-LOCKER-PATTERN.md is already portable, but the framework needs sharable scaffolding (init script? template locker?) to actually be reused across my projects.
6. ~~**Claude Flow composition.**~~ **RESOLVED 2026-04-10** — complementary layers, not overlapping scope. See `docs/plans/claude-flow-capability-audit.md` for the five-slice audit and integration seams. Key outcome: locker stays file-based, CLAUDE.md entry-point signal stays necessary, MANIFEST stays the human curation artifact, and Claude Flow's diagnostic/memory/orchestration primitives are *inputs to* skills, not replacements for them.
7. **Superpowers integration.** Do superpowers skills stay as-is (installed globally, out of the locker model), get vendored as a locker instance, or get referenced by MANIFEST without vendoring? Leaning option 1 or 3 — vendoring feels like ceremony without value.
8. ~~**Harness portability line.**~~ **PARTIALLY RESOLVED 2026-04-10.** Content layer = native Claude Code SKILL.md format (YAML frontmatter, `.claude/skills/<name>/SKILL.md`, portable to any harness that adopts the Anthropic skill spec). Harness layer = MANIFEST.md curation tooling, CLAUDE.md entry-point signal (top-level or nested — see Q10), wire/sync scripts. See `docs/plans/claude-code-native-audit.md`.
9. **SKILL.md front-matter metadata — in scope for v1 or defer?** Post-audit clarification: prefer native Claude Code frontmatter fields (`model`, `allowed-tools`, `triggers`, etc.) where they exist. Any iq-locker-specific fields should go in a namespaced sub-key (e.g. `iq-locker:`) to avoid colliding with current or future native fields. Defer most of this until we have a real locker; don't over-design up front.
10. **Nested CLAUDE.md per-locker scoping — adopt or stay with top-level entry-point snippet?** Claude Code auto-loads nested `CLAUDE.md` when agents read files in a subtree. This could mean `docs/<locker>/CLAUDE.md` auto-activates only when relevant — more elegant than a top-level section, but less discoverable. Needs empirical testing. Defer decision to first-instance locker design.

## Phase Tracker

| Phase | Status | Dates | Deliverables |
|-------|--------|-------|-------------|
| 0. Synthesis & scoping | in progress | 2026-04-10 | This brief |
| 1. Brainstorm & pick first-instance locker | pending | — | Decision on MVP locker target + rough design |
| 1. First-instance locker picked | **complete** | 2026-04-10 | **IQ Design System**, will live at `KGiQ-LLC/iq-design-locker/`. See `docs/plans/iq-design-system-brand-audit.md` |
| 2. Build first-instance locker (IQ Design System at KGiQ-LLC) | pending | — | Working locker + `LINEAGE.md` backwards-looking audit + validation spawn test. Phases A–D detailed in the brand audit doc. |
| 3. Reconcile consumer projects against the canonical locker | pending | — | Each consumer project (iq-publish, booking-module, portfolio-front-end) replaces its stray copies with locker-vendored files |
| 4. Extract reusable framework scaffolding | pending | — | Template / init pattern for additional projects (reference implementation: T3 Hearst `fabric-toolkit`) |

## Assumptions Registry

| ID | Assumption | Status | Validated |
|----|-----------|--------|-----------|
| A1 | Subagents in Claude Code still don't auto-inherit parent-session skill registrations. MANIFEST-read protocol remains necessary. | **validated** | 2026-04-10 via T3 Hearst POC spawn test + Claude Flow audit + Claude Code native audit. *Nuanced:* subagents DO inherit project-level skills from disk (description layer), but NOT the parent's conversation-level discovery. MANIFEST remains necessary for curation (active/dormant), trust boundaries, and promotion discipline — not for physical file availability. |
| A2 | Freelance/SMB-scale agentic coding needs curation but NOT centralized infrastructure (no devboxes, no Toolshed equivalent). | assumed | unvalidated — framework is built on this assumption |
| A3 | Skill locker pattern generalizes cleanly from vendored-upstream (Fabric) to self-authored content (IQ brand). | assumed | unvalidated — first-instance locker will test this |
| A4 | Per-project locker hosting is adequate until locker count > 2–3. | assumed | inherited from SKILL-LOCKER-PATTERN.md |
| A5 | Open Mind and the skill locker compose cleanly via CLAUDE.md conventions without deeper integration. | assumed | unvalidated |
| A6 | Claude Code is the primary target; portability to Codex / Gemini CLI / other harnesses is achievable via a clean content-vs-harness-layer separation and deferred until a real need arises. | assumed | unvalidated |
| A7 | Claude Flow and the skill locker are complementary layers (orchestration/memory vs curated content) and don't functionally overlap in ways that require replacement. | **validated** | 2026-04-10 via five-slice capability audit — see `docs/plans/claude-flow-capability-audit.md` |
| A8 | Superpowers skills (brainstorming, subagent-driven-dev, quality-gate) stay in place; the agentic foundation skills locker covers *gaps*, not duplicates. | assumed | unvalidated. *Post-audit note:* superpowers skills ARE native-format Claude Code skills, so they're already a locker by another name — the real question is whether iq-locker's MANIFEST should cross-reference them or leave them untouched. Lean: leave untouched. |
| A9 | Native Claude Code skills (SKILL.md with YAML frontmatter at `.claude/skills/<name>/SKILL.md`) are the correct content format for iq-locker. The locker does not invent a new format; it curates and adds a trust/promotion layer on top of native skills. | **validated** | 2026-04-10 via Claude Code native audit — see `docs/plans/claude-code-native-audit.md` |
| A10 | Agent teams (experimental Claude Code v2.1.32+ feature) are an occasional tool for parallel-independent-work workflows, NOT the default orchestration pattern. Default stays single-subagent spawning via the `Task`/`Agent` tool. | assumed | based on docs (experimental flag, token-cost warnings, "when to use" guidance). Will revisit if agent teams graduate from experimental. |
| A11 | The first-instance IQ Design System locker lives at `KGiQ-LLC/iq-design-locker/` and BECOMES the canonical source of truth for the IQ design system. It is not vendoring from an upstream — it IS the upstream. Consumer projects (iq-publish, booking-module, portfolio-front-end, future) will vendor FROM it. | **decided** | 2026-04-10 user decision + brand audit verified no single canonical source currently exists (three drifted copies of iq-tokens.css scattered across projects). See `docs/plans/iq-design-system-brand-audit.md`. |
| A12 | Every locker build includes a backwards-looking provenance audit (`LINEAGE.md`) that records per-file source, byte-diffs against origin copies, and drift reconciliation decisions. This is a structural quality gate, not optional. | assumed | based on the IQ design system build plan where the drift problem is the primary justification for the locker's existence. |

## Design Rebase Queue

- If/when Claude Code starts auto-registering skills into subagents: re-evaluate whether MANIFEST-read protocol is still necessary. Curation/filtering value remains regardless.
- If MCP tool count grows past ~20 distinct tools: revisit "MCP curation layer" deferral.
- If I start doing any unattended/one-shot runs: revisit blueprint pattern.
- If I migrate primary harness away from Claude Code: re-check the harness layer boundary and portability assumptions (A6).
- If Claude Flow ships a feature that overlaps with locker content/routing: pause and decide whether the locker still earns its keep in that area.
- If superpowers publishes a skill that overlaps with planned agentic foundation content: de-scope the overlap from the locker rather than duplicate.
- If Anthropic finalizes a formal SKILL.md spec (reserved fields, schema): audit iq-locker skills against it.
- If agent teams graduate from experimental and their token-cost profile improves meaningfully: reassess whether any recurring iq-locker workflow benefits enough to justify team-first design.
- If Claude Code ships a native MANIFEST-equivalent curation feature (active/dormant split, promotion triggers, trust-zone boundaries): evaluate whether to retire iq-locker's MANIFEST layer in favor of it.

## References

- `README.md` — origin stream-of-consciousness (preserved as-is)
- `SKILL-LOCKER-PATTERN.md` — generalized pattern doc (portable, instance-agnostic)
- `stripe-minions.md` / `stripe-minions-2.md` — Stripe's published minion architecture (North Star reference)
- `/Users/kmgdev/dev_projects/t3-hearst/docs/fabric-toolkit/` — the validated POC instance
- `docs/plans/claude-flow-capability-audit.md` — five-slice Claude Flow audit (2026-04-10), resolves open Q6
- `docs/plans/claude-code-native-audit.md` — three-slice Claude Code native ecosystem audit (2026-04-10), resolves Q8 partially and clarifies content vs harness layer boundary
- `docs/plans/iq-design-system-brand-audit.md` — five-project + Neon brand audit (2026-04-10). Load-bearing findings: IQ design system has no single canonical source today (three drifted copies); locker becomes canonical; locker lives at `KGiQ-LLC/iq-design-locker/`; backwards-looking audit (`LINEAGE.md`) is a required build step.
- `docs/lockers-index.md` — framework-scoped registry of all iq-locker-managed locker instances. Records status, home, scope, consumers, design audit, session history, and LINEAGE pointer per instance. Also documents the separation convention: ideation/planning/session history stays in iq-locker; content lives in consumer project repos.
- `/Users/kmgdev/dev_projects/booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md` — the original authoring source for the iQ design system (not in iq-locker; reference location)
- `/Users/kmgdev/KGiQ-LLC/docs/plans/2026-03-29-brand-cohesion-expert-panel.md` — 5-expert panel review that drove the parent-side migration work (not in iq-locker; reference location)
