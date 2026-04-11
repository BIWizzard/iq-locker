# Phase 0→1: iq-locker Framework Init + IQ Design System Locker Scoping — 2026-04-10

**Repo:** iq-locker
**Branch:** `main`
**Phase:** Phase 0 (Synthesis & scoping) → Phase 1 (First-instance locker picked) → Phase 2 (Build plan ready)
**Task(s):** Project initialization, framework design, three capability audits (Claude Flow + Claude Code native + IQ brand), first-instance locker selection (IQ Design System), canonical source framing resolution, build plan tagged for /om-exec

## Session Summary

A long multi-hour session that moved the iq-locker framework from initial concept to a validated build plan ready for automated execution. Session started with `/om-init` populating the Open Mind project scaffold, then `/om-go` into a synthesis mode that consolidated four source documents (SKILL-LOCKER-PATTERN.md, Stripe minions parts 1-2, and the T3 Hearst fabric-toolkit POC) into a real scoping document (`docs/brief.md`). The brief was iteratively refined over the session with major framing corrections as new audits surfaced better information.

Three capability audits were run in parallel-subagent mode. The first (Claude Flow, 5 slices) confirmed that Claude Flow and the skill locker are complementary layers — Claude Flow handles orchestration/memory/diagnostics while the locker handles curated content and trust boundaries. The second (Claude Code native, 3 slices) made a load-bearing discovery: the locker's SKILL.md format should adopt native Claude Code YAML frontmatter, which reframes iq-locker's contribution as a curation/trust/promotion layer on top of native skills rather than a parallel skill format. The third (IQ brand, 5 project surveys + Neon memory query) discovered that the IQ design system has no single canonical source today — three drifted copies of `iq-tokens.css` are scattered across booking-module, iq-publish, and portfolio-front-end, each with different product accents baked in. A 5-expert brand cohesion panel review from 2026-03-29 was found in Neon and ingested to inform the canonical framing.

The user picked **IQ Design System** as the first-instance locker and chose `KGiQ-LLC/iq-design-locker/` as its permanent home. A session-separation convention was established: iq-locker holds all ideation, planning, and session history for every locker instance; consumer project repos hold only the locker content. A framework-scoped registry (`docs/lockers-index.md`) was created to track all locker instances. Finally, the IQ Design System build plan was extracted into its own file and tagged via `/om-plan` with 19 tasks across 6 waves, pre-decided canonicalization choices, and model tier routing (1 opus / 12 sonnet / 6 haiku). Session ended at the pre-execution checkpoint.

## What Was Built/Changed

**New documents (all under `docs/`):**

- `docs/brief.md` — canonical scoping document (populated from stub). Philosophy, environment, scope decisions, candidate first-instance lockers, open questions, phase tracker, assumptions registry, design rebase queue. Iterated 3–4 times through the session as audits refined the framing.
- `docs/plans/claude-flow-capability-audit.md` — 5-slice capability audit of Claude Flow/ruflo, produced by parallel Explore subagents. Verdict: complementary layers, with explicit integration seams and anti-patterns documented. Resolves brief open question Q6.
- `docs/plans/claude-code-native-audit.md` — 3-slice audit of Claude Code native primitives (agent teams, subagents, skills, hooks, settings, MCP). Load-bearing finding: native SKILL.md format is the correct content format. Reframes iq-locker scope. Resolves brief open question Q8 partially.
- `docs/plans/iq-design-system-brand-audit.md` — 5-project brand survey + Neon memory query. Load-bearing finding: no single canonical source for the IQ design system exists; three drifted copies + a 2026-03-29 panel review document harmonization decisions that partially happened. Revised mid-session (twice) as the understanding of canonical source inverted and then corrected.
- `docs/plans/iq-design-system-build-plan.md` — focused build plan extracted from the brand audit, tagged via `/om-plan` with 19 tasks across 6 waves. Deterministic execution path ready for `/om-exec`.
- `docs/lockers-index.md` — framework-scoped registry of all iq-locker-managed locker instances. Documents the session-separation convention ("iq-locker holds memory; consumer repos hold content") and registers the IQ Design System locker with status `scoping`.

**Brief updates across the session (A1–A12 assumptions, Q1–Q10 open questions):**

- A1 (subagents don't auto-inherit skills) reinforced three times — via T3 Hearst POC spawn test, Claude Flow WASM agents being blank slates, and Claude Code native subagents inheriting only disk-level skills
- A7 (Claude Flow complementary) validated via capability audit
- A9 (native SKILL.md format) validated via Claude Code native audit
- A11 (locker lives at KGiQ-LLC) decided
- A12 (LINEAGE.md backwards-looking audit is a structural quality gate) assumed
- Q6 resolved (Claude Flow composition)
- Q8 partially resolved (harness portability line — content layer = native SKILL.md, harness layer = MANIFEST + scripts)
- Q10 added (nested CLAUDE.md per-locker scoping) then resolved via master-index convention
- Candidate evaluation: quality-gating strengthened by Claude Flow audit; IQ brand identified as safest candidate and ultimately picked; agentic foundation skills candidate preserved but with a gap-analysis note vs Claude Flow `guidance_*`

**Project initialization artifacts (from `/om-init`):**

- `docs/open-mind.yaml` (project config)
- `docs/sessions/`, `docs/plans/` directories
- `.claude-flow/`, `.claude/`, `.mcp.json`, `.swarm/`, `ruvector.db`, `CLAUDE.md` (Claude Flow v3 init output)
- Claude Flow pretrain run across 5 files; statusline restored to canonical V3 version
- Neon bootstrap: 8 files embedded from the project on session start

## Key Decisions

| Decision | Rationale |
|----------|-----------|
| **iq-locker is a curation layer on native Claude Code skills**, not a new skill format | Claude Code native audit confirmed SKILL.md YAML frontmatter exists, progressive disclosure is built-in, and subagents inherit skills from disk. The locker's contribution shrinks to MANIFEST curation, trust boundaries, promotion discipline, and CLAUDE.md entry-point teaching — a meaningfully smaller scope with higher leverage. |
| **Stripe as North Star, not template** | Explicit philosophy captured in brief. Right-sized for freelance/SMB tier; don't replicate Stripe's devboxes, Toolshed scale, minion architecture. MCP tool curation and blueprints both deferred with explicit revisit triggers. |
| **IQ Design System = first-instance locker** | Three candidates evaluated (IQ brand, quality-gating, agentic foundation). IQ brand won because: zero Claude Flow overlap, content is stable and cross-project, canonical source problem is clearest justification, acts as first real validation of the framework on self-authored content. |
| **Locker lives at `KGiQ-LLC/iq-design-locker/`** | KGiQ-LLC is the parent company repo and natural canonical host. iq-locker holds the framework and design thinking; consumer repos hold content. Separation keeps ideation out of consumer project histories. |
| **Locker BECOMES the canonical source** | Not vendoring from an upstream — it IS the upstream. Consumer projects (iq-publish, booking-module, portfolio-front-end) will eventually vendor from it. This inverts the fabric-toolkit reference implementation pattern because the upstream is first-party. |
| **Master-index convention at project-level CLAUDE.md** | Single lightweight section listing all locker pointers. Three-level progressive disclosure: CLAUDE.md (always loaded, cheap) → MANIFEST.md (loads when agent navigates) → SKILL.md (loads when invoked). Cookie-cutter templateable across projects; no per-project decisions about nested CLAUDE.md placement. |
| **Session separation convention locked in** | iq-locker holds ALL ideation/planning/session history for every locker instance, regardless of where the locker physically lives. Consumer repos hold only content. This prevents iterative design work from polluting consumer project git histories. |
| **Three-layer token model: core → accents → parent-layer** | Matches how the content is actually structured in the wild but with drift fixed. `core/` is neutral (no product accent baked in); `accents/` has per-product files (gold/indigo/teal); `parent-layer/` has KGiQ-specific visual overrides. Consumer projects import core + their accent + optionally the parent layer. |
| **Backwards-looking `LINEAGE.md` audit is mandatory** | Assumption A12. Every locker build includes a per-file provenance audit with byte-compare against sources, consumer-project delta report, completeness grep, and spec verification. This is both the quality gate for the build AND the "where is the source of truth?" answer the user explicitly asked for. |
| **Task 4 gets opus tier; everything else sonnet or haiku** | The `core/iq-tokens.css` canonicalization is the only task with creative judgment calls that downstream tasks depend on. All other tasks are mechanical given the pre-decided canonicalization choices in the build plan's top section. |
| **/om-plan output preserved; execution deferred** | Plan tagged with 19 tasks / 6 waves / dependencies / model tiers, but execution deliberately pushed to a fresh session. Cleaner monitoring context; separates design history from build history. |

## Discoveries

- **Discovery: Native Claude Code skills format is already progressive disclosure.** Descriptions load at startup (~200 chars per skill), full SKILL.md body loads only on invocation, resources in `resources/` load only when skill reads them. Slash command integration is automatic. Subagents inherit all native skills from disk at spawn time.
  - **Impact:** Reframes iq-locker's scope from "build a skill delivery system" to "add curation + trust + promotion + entry-point teaching on top of native skills." Much smaller and sharper.

- **Discovery: The IQ design system has three drifted copies, not a single canonical source.** `iq-tokens.css` exists at `booking-module/packages/shared-ui/src/styles/` (teal accent, March 24 — original authoring), `iq-publish/vendored/` (indigo accent, March 28 — rewritten with misleading "iQ Publish product override" comment), and `portfolio-front-end/src/styles/vendored/` (March 30 — byte-identical copy of iq-publish's version, corrected at runtime via `iq-site-tokens.css`).
  - **Impact:** The locker's primary justification becomes "solve the distributed-copies problem by being canonical." Phase E reconciliation (bring consumer projects in line) is in-scope as a future activity.

- **Discovery: The original iQ design system authoring spec lives at `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md`.** Found via Neon query — not visible in a filesystem-only survey.
  - **Impact:** Locker docs/ pulls from this authoritative source. Spec verification in LINEAGE.md compares extracted tokens against this spec.

- **Discovery: A 5-expert brand cohesion panel ran on 2026-03-29** (web design, brand identity, UI/UX, Apple PM, "Steve Jobs channel") and produced binding harmonization decisions: Plus Jakarta Sans for headings is priority #1; white "iQ" + sage green reclassified from primary to legacy; light background logo variant is blocking; dot-as-signature system validated (KGiQ Gold / Publish Indigo / Booking Teal); glassmorphism forbidden on customer-facing surfaces.
  - **Impact:** These decisions are THE latest canonical thinking and supersede older KGiQ-LLC/branding/guidelines.md content. Locker should reflect panel outcomes. Also: `KGiQ-LLC/branding/design-tokens.css` is stale (Feb 4) and should NOT be used as a vendoring source.

- **Discovery: portfolio-front-end IS the canonical parent runtime expression**, not KGiQ-LLC/branding/. The parent's effective design system = the vendored `iq-*.css` files (originating from iq-publish) + `iq-site-tokens.css` override layer. `iq-site-tokens.css` overrides `--iq-tint-base` to steel blue and sets `--iq-product-accent` to gold — exactly what the parent needs — so the runtime renders correctly despite the core file's stray indigo comment.
  - **Impact:** Locker's `parent-layer/iq-site-tokens.css` is extracted from portfolio-front-end, not authored fresh. Cleanly sidesteps the question of "where do we store the parent visual layer?"

- **Discovery: "KGIQ CRM" in Neon = `dev_projects/upwork` on disk.** Neon's project-level organization uses a different name than the filesystem directory. Noted for future cross-referencing between filesystem and Neon memory.
  - **Impact:** Sanity check when interpreting Neon query results — project names in Neon may not match disk paths.

- **Discovery: Agent teams (Claude Code v2.1.32+) are experimental and require `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.** Not a default orchestration primitive. Work best for parallel independent work with high coordination overhead (research, multi-angle review, competing hypotheses). Do NOT replace the default single-subagent `Task`/`Agent` tool pattern.
  - **Impact:** Assumption A10. Agent teams remain an occasional tool; the locker framework is built around single-subagent spawning as the default.

- **Discovery: Claude Flow hooks in `.claude/settings.json` use the NATIVE Claude Code hook mechanism** — Claude Flow doesn't have a separate hook system; it registers command handlers against Claude Code's hook slots.
  - **Impact:** Adding custom iq-locker hooks requires coexistence with Claude Flow's hook-handler.cjs via the matcher system. Order and matcher specificity matter.

## Assumptions Validated/Invalidated

Cross-reference with `docs/brief.md` Assumptions Registry:

- **[A1]** Subagents don't auto-inherit parent-session skill registrations. MANIFEST-read protocol remains necessary. → **validated** (T3 POC + Claude Flow WASM agents + Claude Code native subagent context model). Nuanced: subagents DO inherit file-level skill availability but NOT conversation-level discovery. MANIFEST still necessary for curation/trust/promotion.
- **[A2]** Freelance/SMB-scale agentic coding needs curation but not centralized infrastructure. → unchanged (framework built on this; not falsified)
- **[A3]** Skill locker pattern generalizes cleanly from vendored-upstream to self-authored. → unchanged (test awaits Phase 2 build). Partial: IQ design system is first-party-vendored rather than third-party, a middle case.
- **[A4]** Per-project locker hosting adequate until locker count > 2–3. → unchanged
- **[A5]** Open Mind and skill locker compose cleanly via CLAUDE.md conventions. → unchanged. Session-separation convention in lockers-index.md operationalizes this.
- **[A6]** Claude Code primary; portable via content-vs-harness-layer separation. → reinforced by native audit. Content layer now clearly identified as native SKILL.md (portable to any harness that adopts the Anthropic spec).
- **[A7]** Claude Flow and skill locker are complementary layers. → **validated** via 5-slice capability audit.
- **[A8]** Superpowers skills stay in place; foundation locker covers gaps. → unchanged. Post-audit note: superpowers skills ARE native-format already, so the question becomes whether to cross-reference from iq-locker MANIFEST or leave untouched. Lean: untouched.
- **[A9]** Native Claude Code SKILL.md is the correct content format for iq-locker. → **validated** via Claude Code native audit. New assumption introduced this session.
- **[A10]** Agent teams are an occasional tool, not default orchestration. → assumed (new this session, based on experimental status + docs warnings).
- **[A11]** Locker lives at KGiQ-LLC/iq-design-locker/ and IS canonical. → **decided** this session.
- **[A12]** LINEAGE.md backwards-looking audit is a structural quality gate per locker build. → assumed (new this session, based on drift problem surfaced by brand audit).

## Problems & Solutions

| Problem | Resolution |
|---------|------------|
| Brand audit framing went through two reversals (KGiQ-LLC = canonical → iQ products = canonical → locker = canonical) before settling | User flagged each reversal and we verified empirically with file timestamps, directory contents, and panel review history. Final framing: no single canonical source exists today; locker becomes canonical. |
| `/om-plan` skill expected `### Task N:` headings; my initial plan used `### A1.`, `### B2.` etc. with phase prefixes | Rewrote the plan file with 19 global `### Task N:` headings, collapsing fine-grained phase sub-tasks into executable units. Phase organization preserved via section headers and body content. |
| Claude Flow daemon PID file was repeatedly stale across saves | MCP tool calls worked regardless — the runtime routes around the stale PID. Noted as a known quirk but non-blocking for `/om-save` operations. |
| The user's initial claim "portfolio-front-end is the canonical parent version" was correct but needed nuancing | Verified with file diffs: portfolio-front-end's vendored files are byte-identical copies of iq-publish's, corrected at runtime by `iq-site-tokens.css`. "Canonical parent RUNTIME expression" is accurate; "canonical parent SOURCE" is not (no single source exists). Distinction captured in audit doc. |

## Open Questions

1. Whether to execute `/om-exec` on the tagged build plan in a fresh session (recommended) or push through in the current one. **Pending user decision at wrap time.**
2. Hover/dark-mode color derivations for gold in the core iq-tokens file: proposed `#E6BC5C` hover, `#FFDB8F` dark, `#FFE4A8` dark-hover. Eyeballed from `#FFD166`; may need proper color-system treatment. Flagged for human review during Task 12 (spec verification).
3. Whether `scripts/vendor-into.sh` should be shell or Node. Lean: shell for first build, upgrade if needed.
4. Whether to include logos SVG files (Task 6) by copy or by relative-path reference from KGiQ-LLC/branding/logos/. Lean: copy for self-containment.
5. Task 12 may surface substantive drift between the authoring spec and the current implementations. If so, need to decide which is canonical — spec or current code.
6. Brief Q9 (SKILL.md front-matter metadata for Claude Flow integration) is still open. May resolve during Phase 2 build or may be deferred to Phase 3 (extract reusable framework scaffolding).

## Tracked Files Changed

Files in the tracked_files list from `docs/open-mind.yaml` that were created or updated this session:

- `docs/brief.md` (populated from stub → ~200 lines with philosophy, scope, candidates, 12 assumptions, 10 open questions, phase tracker, design rebase queue, references)
- `docs/plans/claude-flow-capability-audit.md` (new, ~2500 words)
- `docs/plans/claude-code-native-audit.md` (new, ~2500 words)
- `docs/plans/iq-design-system-brand-audit.md` (new, ~4000 words — largest artifact)
- `docs/plans/iq-design-system-build-plan.md` (new, ~650 lines — tagged with 19 task blocks)
- `docs/sessions/2026-04-10-iq-design-locker-scoping.md` (this file)
- `docs/discovery-log.md` (about to be appended in Step 5)

Files NOT in tracked_files but created this session (candidates for future tracking):

- `docs/lockers-index.md` — framework-scoped registry of locker instances
- `docs/open-mind.yaml` — project config
- `CLAUDE.md` — Claude Flow generated project instructions
- `.claude/settings.json` and the broader `.claude/` + `.claude-flow/` + `.mcp.json` scaffolding

## Next Steps

1. **Wrap this session** (in progress) — commit everything and restore continuity for the next session
2. **Start a fresh session from iq-locker** with `/om-go`, choose "Continue last session's work," and get briefed on the IQ Design System locker build plan
3. **Run `/om-exec docs/plans/iq-design-system-build-plan.md`** to execute the tagged 19-task build. The plan is fully deterministic given the pre-decided canonicalization choices.
4. **Review Task 4 output closely** — this is the only opus-tier task and the only one with creative judgment. Specifically: verify `core/iq-tokens.css` has no misleading comments, uses gold as the neutral default, and hover/dark-mode derivations are sane.
5. **Phase B (Tasks 8–12) produces the `LINEAGE.md` provenance audit** — treat this as the critical quality gate. Any surprises here should stop the build.
6. **Phase D Task 19 (empirical spawn test)** is the validation signal that flips `docs/lockers-index.md` status from `scoping` to `validated` and marks Phase 2 complete in the brief's tracker.
7. **After the locker is validated**, decide whether to reconcile consumer projects (Phase 3) in immediate follow-up sessions or defer.

## Continuation Prompt

> Continue IQ Design System locker build in iq-locker.
>
> **Last session recap:** Completed Phase 1 (first-instance locker picked = IQ Design System, living at `/Users/kmgdev/KGiQ-LLC/iq-design-locker/`). Produced three audits (Claude Flow capability, Claude Code native ecosystem, IQ brand across 5 projects + Neon memory). Key insight: no single canonical source exists for the IQ design system today — three drifted `iq-tokens.css` copies scattered across booking-module / iq-publish / portfolio-front-end. The locker BECOMES canonical. Build plan tagged via `/om-plan` with 19 tasks across 6 waves, pre-decided canonicalization choices, and ready for `/om-exec`.
>
> **Priority:** Run `/om-exec docs/plans/iq-design-system-build-plan.md`. Pay close attention to Task 4 (the only opus-tier task — canonicalizing `core/iq-tokens.css` with the neutral gold default) and Phase B (Tasks 8–12 produce the mandatory `LINEAGE.md` backwards-looking audit). Phase D Task 19 is the validation spawn test that flips locker status to `validated`.
>
> **Key context:**
> - Locker lives at `KGiQ-LLC/iq-design-locker/`, not inside iq-locker. Session history stays in iq-locker regardless — per the session-separation convention in `docs/lockers-index.md`.
> - The locker IS canonical — it's not vendoring from an upstream. Consumer projects (iq-publish, booking-module, portfolio-front-end) will eventually reconcile to vendor from it in Phase 3.
> - The original authoring spec lives at `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md`. That's THE source for design system intent. Copied into the locker at Task 7.
> - The 2026-03-29 brand cohesion panel review (`KGiQ-LLC/docs/plans/2026-03-29-brand-cohesion-expert-panel.md`) carries the binding harmonization decisions: Plus Jakarta Sans headings, white iQ + sage green reclassified, dot-as-signature system validated, no glassmorphism on customer-facing. Locker must respect these.
> - Don't modify `KGiQ-LLC/branding/design-tokens.css` (stale Feb 4) — it's superseded by the vendored iq-*.css model. The locker intentionally excludes it.
> - Pre-decided canonicalization choices are at the top of the build plan. Don't re-litigate them during execution — they exist specifically to make Task 4 deterministic.
>
> **Brief:** Read `docs/brief.md` for full project context (12 assumptions, 10 open questions, phase tracker showing Phase 2 pending). Read `docs/plans/iq-design-system-brand-audit.md` for the full audit that justifies the build. Read `docs/plans/iq-design-system-build-plan.md` for the tagged execution plan.
