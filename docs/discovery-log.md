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

---

## 2026-04-11 — Phase 2 Partial: IQ Design System Locker Build — Waves 1–4
**Phase context:** `/om-exec` on `docs/plans/iq-design-system-build-plan.md`. 18 of 19 tasks executed across Waves 1–4. Wave 5 (wire) + Wave 6 (validation spawn test) deferred to next session as a natural content-vs-wiring boundary.
**Session log:** `sessions/2026-04-11-iq-design-locker-build.md`

### Discoveries

#### Third drift hunk in `iq-tokens.css` — hardcoded `--iq-color-info: #3B82F6` in iq-publish
The prior brand audit tracked the teal/indigo accent drift but did not catch that iq-publish's 2026-03-28 rewrite also hardcoded `--iq-color-info: #3B82F6`, severing the semantic info ↔ accent linkage. Booking-module preserved `--iq-color-info: var(--iq-accent)` — the correct theming contract. Task 2's structured diff report surfaced it as a third unrecorded drift hunk within the same diff hunk region as the indigo accent rewrite.
- **Downstream:** This single discovery drove the Wave 2 base-selection reversal (the user explicitly overrode build-plan pre-decision #4 to base `core/iq-tokens.css` on booking-module instead of iq-publish). Every Wave 2+ canonical content depends on this reversal. It also changed Phase E's approach to iq-publish migration: the locker core no longer carries the hardcode, so iq-publish must preserve its defensive fix as a local one-line override in `src/app/globals.css` or lose its tenant-branding guard. Documented in `KGiQ-LLC/iq-design-locker/LINEAGE.md` §2.2 and §3.2.

#### portfolio-front-end's booking widget is a real runtime consumer of `--iq-color-info`
Task 10's consumer-delta investigation found that `portfolio-front-end/public/vendor/booking-widget.js` uses `var(--iq-color-info)` for info-chip background and border. It currently resolves to `#3B82F6` — iq-publish's defensive hardcode cascaded by accident because portfolio-front-end copied iq-publish's vendored CSS byte-identically. After Phase E migration to the locker, `--iq-color-info` reverts to `var(--iq-accent)` = gold (KGiQ parent). **Guaranteed visible UI shift** in the embedded booking widget.
- **Downstream:** Portfolio-front-end's Phase E migration bumps from M to L effort class — the only migration in Phase E with a visible behavior change. Requires a visual regression pass on the booking widget. Prior brand audit assumed the `--iq-color-info` drift was iq-publish-only; it transitively affects any consumer that byte-copied iq-publish's vendored CSS. Future audits should grep for `--iq-color-info` across ALL consumers, not just the projects with their own iq-tokens.css. Documented in `KGiQ-LLC/iq-design-locker/LINEAGE.md` §3.3.

#### Brief has a second stale datum beyond the Three-Layer Token Model
Task 12's spec verification found that `docs/iq-design-system-brief.md` "Surface Rules" table cites dark-surface values (`#424245 / #545458`) that match neither the authoring spec nor the locker core. The brief was last updated 2026-03-25 and carries booking-specific dark values that don't generalize to the multi-product canonical model. The pre-build brand audit flagged only the Three-Layer Token Model drift at line 25 (`--iq-accent = FIXED...`); this is a second one.
- **Downstream:** Brief update work item (Phase E) has two bullets now, not one. Brief update may be worth bundling with iq-publish's Phase E migration since that's where the brief originated. Non-blocking for Phase D closeout. Documented in LINEAGE §5.2 and §5.5 D8.

#### Structural validation is a sufficient substitute for `npm test` in content/docs lockers
The build ran without any unit tests — iq-locker and KGiQ-LLC/iq-design-locker are content/docs repos. Structural substitutes (CSS brace balance, YAML frontmatter validation, SKILL.md structure checks, shasum byte-compare for verbatim files, `bash -n` + end-to-end smoke test for the shell script) caught every real issue across 8 review cycles without any escape. The "fix all suggestions and re-review until clean" policy amplified this — every cycle 2 review passed cleanly after cycle 1 fixes.
- **Downstream:** Consider codifying structural-validation-in-lieu-of-tests as a canonical iq-locker framework pattern for content-only lockers. Phase 4 (extract reusable framework scaffolding) candidate. Reduces the "what counts as tests?" decision overhead for every future locker build.

#### LINEAGE.md format works as a quality gate, not just a provenance record
The cycle 1 code review of Wave 3 caught multiple factual errors in LINEAGE.md (swapped teal/indigo family labels on the publish/booking rows, wrong KGiQ base hex `#D4A84B` instead of `#FFD166` cited in two places) that would have silently propagated into consumer documentation. The reviewer used LINEAGE as cross-reference ground truth — which ironically was the same doc under review. This worked because LINEAGE's per-file provenance table made every claim greppable against reality.
- **Downstream:** LINEAGE.md is more than an audit trail — it's load-bearing infrastructure for future code-review cycles against the locker. The format deserves promotion to a framework template (A12 assumption validated this session). Future lockers should inherit the same 6-section structure: provenance / drift / consumer delta / completeness / spec verification / quality-gate history.

#### Vendor script `--accent` must be required to structurally block the parent-layer-without-accent footgun
Pre-decision #5 moved `--iq-product-accent` out of `parent-layer/iq-site-tokens.css` and into the accent files. A consumer vendoring `parent-layer/` alone would lose `--iq-product-accent`, breaking the dot-as-signature system. Task 16's `vendor-into.sh` makes `--accent` a required CLI flag — exit code 2 if missing — which structurally prevents the script-based path from hitting the footgun. Hand-copy workflows are still at risk, which is why LINEAGE §5.7 tracks adding a guard comment to `parent-layer/iq-site-tokens.css` as a Phase E precursor.
- **Downstream:** Confirms that for architectural dependencies between locker files, **CLI flag requirements are a legitimate enforcement mechanism**. Framework pattern: any locker with cross-file dependencies should bake enforcement into its vendor script via required flags. Alternative (documentation alone) is insufficient.

### Assumptions Validated

- [A3] Skill locker pattern generalizes from vendored-upstream to self-authored content (partially — build worked cleanly; drift reconciliation was harder than vendored-upstream but manageable)
- [A9] Native Claude Code SKILL.md format is the correct content format (all 8 skills written with valid frontmatter, descriptions under 250 chars, front-loaded trigger keywords)
- [A11] Locker lives at KGiQ-LLC/iq-design-locker/ and IS canonical (build landed there, session-separation convention worked cleanly)
- [A12] LINEAGE.md backwards-looking audit is a structural quality gate (validated strongly — LINEAGE caught factual errors during cycle 1 code review that would have silently propagated)

### Assumptions Invalidated

None this session.

### Open Questions

1. Task 19's spawn discoverability test — success bar (one pass vs 2-3 variants for flakiness reduction)?
2. Whether to fold the `parent-layer/iq-site-tokens.css` guard comment (Phase E precursor) into Wave 5 or defer to Phase E proper
3. Whether the brief update (D7 + D8) is its own mini-phase or bundled with iq-publish Phase E migration
4. How to handle the widget-tokens.css Shadow DOM re-projection — locker ships a sample regeneration script, or consumer owns it entirely?
5. Whether the structural-validation-in-lieu-of-tests pattern should be promoted to an iq-locker framework convention (Phase 4 candidate)
