# iq-locker — Discovery Log

Discoveries with cross-phase implications. Append-only.

---

## 2026-04-10 — Phase 0→1: iq-locker Framework Init + IQ Design System Locker Scoping
**Phase context:** Project initialization, framework design, three capability audits (Claude Flow / Claude Code native / IQ brand), first-instance pick, build plan tagged
**Session log:** `sessions/2026-04-10-iq-design-locker-scoping.md`

### Discoveries

#### Native Claude Code skills ARE the right content format for iq-locker
Claude Code's native skill format (`.claude/skills/<name>/SKILL.md` with YAML frontmatter — `name`, `description`, optional `triggers` / `allowed-tools` / `model` / `context`) already implements progressive disclosure. Descriptions load at startup; full SKILL.md body loads on invocation; resources in `resources/` load only when read. Subagents (including WASM gallery agents in Claude Flow) inherit skills from disk at spawn time.
- **Downstream:** Reframes iq-locker's scope from "build a skill delivery system" to "add curation + trust + promotion + entry-point teaching on top of native skills." Affects every locker instance the framework will ever produce. Assumption A9 added and validated same session.

#### No single canonical source for the IQ design system currently exists
Three drifted copies of `iq-tokens.css` scattered across booking-module (teal accent, 2026-03-24 original authoring), iq-publish (indigo accent rewrite 2026-03-28 with misleading "iQ Publish product override" comment), portfolio-front-end (byte-identical copy of iq-publish's version 2026-03-30, corrected at runtime via `iq-site-tokens.css`). The authoring spec lives at `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md`. The 2026-03-29 5-expert brand cohesion panel review documented harmonization decisions that only partially propagated.
- **Downstream:** The IQ Design System locker's primary justification becomes "solve the distributed-copies problem by being canonical." The locker IS the canonical source — it's not vendoring from an upstream. Phase 3 (reconcile consumer projects) is now in scope as a future activity. Assumption A11 (locker at KGiQ-LLC, is canonical) decided this session. Assumption A12 (backwards-looking LINEAGE.md audit as structural quality gate) added this session.

#### Claude Flow and the skill locker pattern operate at complementary layers
Five-slice capability audit confirmed: Claude Flow handles orchestration (swarms, claims, workflows), memory/intelligence (HNSW-indexed three-tier memory, SONA neural learning), hooks (native Claude Code hook slots), analysis/guidance (diagnostic primitives), and peripherals (GitHub/browser/terminal/WASM/transfer plumbing). The skill locker handles curated content, trust boundaries, promotion discipline, and subagent discoverability via the MANIFEST-read invocation protocol. No overlap on core primitives.
- **Downstream:** Integration seams documented: skills invoke Claude Flow diagnostic tools directly; post-task hooks record skill invocations into CF memory; workflow templates could encode skill sequences. Explicit anti-patterns: don't auto-load skills via pre-task hooks (violates judgment-at-agent-level); don't collapse SKILL.md into CF memory as primary store (loses portability and readability). Assumption A7 validated. Brief open question Q6 resolved.

#### The MANIFEST-read invocation protocol is still necessary even with native skill inheritance
Subagents in Claude Code inherit skills from disk at spawn time — meaning SKILL.md descriptions are automatically visible. BUT: they don't inherit the parent's conversation-level discovery state (which skills the parent has used), they don't track active/dormant curation, they don't know about trust boundaries, and they don't have a project-level "master index" of what's relevant. MANIFEST curation fills these gaps. WASM gallery agents in Claude Flow reinforce this further — they spawn as blank slates.
- **Downstream:** Assumption A1 reinforced from three independent sources (T3 Hearst POC + Claude Flow audit + Claude Code native audit). The MANIFEST format is now defined as: curation index with active/dormant status, trust boundaries, promotion triggers, pointer to `LINEAGE.md` for provenance.

#### Agent teams in Claude Code v2.1.32+ are experimental and NOT the default orchestration
Gated behind `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. High coordination overhead and token cost. Work best for parallel independent work (research, multi-angle review, competing hypotheses). Default remains single-subagent spawning via the `Task`/`Agent` tool. Teammates inherit project context (CLAUDE.md, MCP servers, skills) from disk at spawn but do not inherit the lead's conversation history.
- **Downstream:** Assumption A10 added. iq-locker's default orchestration story stays single-subagent. Agent teams noted as an occasional tool for specific scenarios. If they graduate from experimental, revisit.

#### Master-index convention at project-level CLAUDE.md is the right discovery mechanism
Three-level progressive disclosure: project-level `CLAUDE.md` "Lockers" section (always loaded, ~40–60 tokens per locker entry) → `docs/<locker>/MANIFEST.md` (loaded when agent navigates into a locker) → `SKILL.md` files inside the locker (loaded when invoked). This is templateable across projects — no per-project decisions about where nested CLAUDE.md stubs should live.
- **Downstream:** Q10 resolved. This convention is documented in `docs/lockers-index.md` and becomes the standard for every iq-locker-managed locker. Nested CLAUDE.md files become optional reinforcement, not the primary signal.

#### Session-separation convention: iq-locker holds memory; consumer repos hold content
Every locker instance, regardless of where it physically lives, has its ideation/planning/session logs/design audits stored in iq-locker's `docs/` tree. Consumer project repos only get the locker content (files in a directory like `iq-design-locker/`). This keeps iterative design work out of consumer project git histories and in one coherent place.
- **Downstream:** Operationalized in `docs/lockers-index.md`. Every locker entry in the registry includes a "session history" field pointing back to iq-locker's sessions directory. This is a structural pattern the framework enforces, not a judgment call per locker.

### Assumptions Validated

- [A1] Subagents don't auto-inherit parent-session skill registrations (validated 3 ways: T3 POC + Claude Flow WASM + Claude Code native)
- [A7] Claude Flow and skill locker are complementary layers (5-slice capability audit)
- [A9] Native Claude Code SKILL.md is the correct content format (Claude Code native audit)
- [A11] Locker lives at KGiQ-LLC/iq-design-locker/ and IS canonical (user decision + brand audit verified no single canonical source exists)

### Assumptions Invalidated

None this session. A couple of earlier framings were corrected mid-session:
- Initial framing "KGiQ-LLC/branding/ is canonical" — corrected: it's a parent brand layer with stale token exports. The iQ product design system (vendored iq-*.css files) is the effective canonical layer, BUT no single file is canonical today (three drifted copies).
- Initial framing "parent (6/10) vs products (9/10) drift" from the panel review — corrected: the panel snapshot is from 2026-03-29; by 2026-04-10 portfolio-front-end has partially migrated (vendored the iq-*.css files March 30), though `KGiQ-LLC/branding/design-tokens.css` is still stale.

### Open Questions

1. Whether `/om-exec` runs in the current session or a fresh one (pending user decision at wrap time)
2. Hover/dark-mode gold derivations in the core file — eyeballed values may need proper color-system treatment (flagged for Task 12 spec verification)
3. Brief Q9 (SKILL.md front-matter metadata for Claude Flow integration) remains open — may resolve during Phase 2 build or defer to Phase 4 (extract reusable scaffolding)
4. Whether `scripts/vendor-into.sh` should be shell or Node (lean: shell for first build)
5. How Phase 3 (reconcile consumer projects) interacts with each consumer's existing session history and git conventions
