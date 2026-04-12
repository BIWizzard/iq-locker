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

---

## 2026-04-12 — Phase 2 Detour: Design System Consolidation
**Phase context:** Phase 2 (Build first-instance locker) — still in progress; major consolidation pass between Waves 1-4 (done) and Waves 5-6 (deferred)
**Session log:** `sessions/2026-04-12-design-system-consolidation.md`

### Discoveries

#### Cross-app drift between iQ Booking and iQ Publish is significant and not visible from the spec alone
A thorough audit of how the iQ design system is actually implemented in both apps surfaced multiple drift items that the spec and locker docs don't capture. iQ Publish has `--iq-color-info: #3B82F6` hardcoded (the exact issue that drove the Wave 1 base-selection reversal — it was never fixed in Publish itself), and iQ Publish is missing both Plus Jakarta Sans and JetBrains Mono from its font loading, so all headings silently fall back to system UI. iQ Booking has a calendar page badge hardcoded with Slate-800 colors that will not adapt to light mode, and its `glassCard` pattern is applied to L1 cards which may violate the 2026-03-29 panel decision. Full audit captured in `docs/plans/2026-04-12-cross-app-drift-report.md`.
- **Downstream:** Direct impact on Phase E consumer migration work. iq-publish migration (currently "S" effort) grows to include font loading work and the hardcode fix. booking-module migration needs the calendar badge tokenization and a glass policy re-evaluation. Recommended migration order from LINEAGE §3.4 still stands (iq-publish → booking-module → portfolio-front-end). Adds urgency to Phase E: the apps can only drift further until they're reconciled to vendor from the locker.

#### Canonical Figma product accent map has 5 reserved colors for future products
The session surfaced a Figma artifact that contains the complete product accent registry: KGiQ gold, iQ Booking teal, iQ Publish indigo, iQ Circle rose (active), plus Menu red (#EF4444), Family Finance green (#22C55E), Ledger purple (#A855F7), Next? lime (#84CC16), and Blue Variant / Tight Corridor blue (#0284C7) (reserved). Before this session, no document, code file, or memory system had this map — each new product had to independently decide its color. Now documented in three redundant locations (`iq-brand-system.md` §1, `guidelines.md` v3.0 §3, Claude Flow memory key `product-accent-color-map`).
- **Downstream:** Future new iQ products have their accent colors pre-decided. Eliminates the iteration that iQ Publish went through (violet → coral → indigo). When Menu, Family Finance, Ledger, Next?, or Blue Variant is built as an app, creating its accent CSS file is a mechanical operation — no design decision needed.

#### `KGiQ-LLC/branding/guidelines.md` v2.0 had fundamentally different design system values than the locker
The guidelines doc used Tailwind Slate surfaces (canvas `#0f172a`, primary `#1e293b`) while the locker uses Apple-style neutral blacks (canvas `#000000`, primary `#1C1C1E`). It also used a completely different token namespace (`--brand-*`, `--surface-*`, `--bg-*`) compared to the locker's `--iq-*`. This isn't incidental drift — it's two design philosophies that were never reconciled during the v2.0 rebrand. Any agent reading guidelines.md for implementation guidance would produce visibly different UIs from what the locker's CSS would render.
- **Downstream:** Resolved this session by rewriting guidelines.md to v3.0 with narrowed scope (brand identity only, no implementation). v3.0 is 386 lines (vs 624 for v2.0) and complements `iq-brand-system.md` with clean scope separation. Future updates: guidelines covers "who we are / how we look"; locker covers "how to build it." No overlap, no drift potential. Note: `parent-layer/kgiq-brand-guidelines.md` in the locker is a byte-for-byte copy and must be manually synced on every guidelines update until the iq-locker framework adds a sync pattern.

#### Both iQ Booking and iQ Publish define typography utility classes (`.iq-title-1`, `.iq-display`, etc.) but neither app uses them
Both apps' vendored `iq-typography.css` files carry the full set of utility classes (10 styles) that reference the font and size tokens, but components in both apps apply typography via inline `style` props referencing tokens directly. The utility classes are dead code.
- **Downstream:** Non-blocking. `iq-brand-system.md` §4 documents this and recommends new apps adopt the utility classes. iQ Circle (the newest app) should use them from day one as a cleaner pattern. Existing apps could migrate during Phase E but it's not urgent.

#### LINEAGE.md has robust write-time discoverability but weak read-time discoverability
LINEAGE is well-wired as a write target: the MANIFEST's curation cadence rules say "record in LINEAGE" when adding an accent, and the promotion rules say "record in LINEAGE" when promoting a skill. But there's no equivalent routing for READ use cases — an agent doing Phase E migration wouldn't naturally find §3's consumer delta reports, and an agent investigating drift wouldn't find §2's reconciliation log. The MANIFEST referenced LINEAGE only generically ("see for audit history").
- **Downstream:** Resolved this session by adding §6 "When to consult LINEAGE.md" to the MANIFEST with a situation → section routing table (consumer migration → §3, drift investigation → §2, etc.). Also added section-specific LINEAGE pointers to the `product-accents` and `kgiq-parent-brand` SKILLs. **Pattern worth codifying at the iq-locker framework level**: "every locker's MANIFEST should have a 'When to consult LINEAGE' section." Would benefit all future locker instances.

#### All three product base logos use byte-identical iQ letterform coordinates
iQ Booking, iQ Publish, and iQ Circle base wordmarks have the exact same SVG path data for the "iQ" element — only the dot `<circle>` element's `fill` color differs. This was verified during the Circle mark generation and confirmed again when back-filling Booking and Publish marks.
- **Downstream:** Mark and favicon variants can be generated from any base wordmark by viewBox cropping + dot color substitution, with zero risk of geometric drift. Eliminates the need to manually derive mark geometry for new products. When future reserved products (Menu, Family Finance, etc.) get their base wordmarks authored in Figma, the mark/favicon set can be generated in minutes by copying the Circle pattern and substituting colors.

#### iQ Publish base wordmarks had `#6366F2` instead of canonical `#6366F1` for the indigo dot
One-digit drift between the logo files and the token system / accent CSS / brand guidelines. The panel doc `docs/history/2026-03-29-brand-cohesion-expert-panel.md` has the same typo (flagged in LINEAGE §5 D3).
- **Downstream:** User corrected both SVGs in Figma mid-session; locker copies synced. The panel doc still has the typo but it's historical (no runtime impact, no files reference it for rendering). Low-priority footnote for next panel-doc edit if one happens; otherwise leave it as historical record.

### Assumptions Validated

- **[A3]** Skill locker pattern generalizes from vendored-upstream to self-authored content. → **further reinforced**. The locker absorbed a major consolidation (new canonical doc, archived legacy docs, reorganized directories) without breakage. Three-layer content model held up under expansion to four products.
- **[A9]** Native Claude Code SKILL.md format is the correct content format. → **validated again**. All 7 active SKILLs were updated cleanly in-place with new routing.
- **[A11]** Locker lives at `KGiQ-LLC/iq-design-locker/` and IS canonical. → **reinforced**. iQ Circle registration worked exactly as the framework anticipated.
- **[A12]** LINEAGE.md backwards-looking provenance audit is a structural quality gate. → **validated with caveat**. Worked as a write target; read discoverability needed improvement (fixed this session via MANIFEST §6 and SKILL pointers). Worth codifying as framework pattern.
- **[A-new from 2026-04-11]** Structural validation is a sufficient substitute for `npm test`. → **further reinforced**. No bugs escaped this session despite 30+ files touched across three repos.

### Assumptions Invalidated

None this session.

### Open Questions

1. **Should future new iQ products get accent CSS files created proactively (from the Figma map), or wait until an actual app needs them?** Leaning toward "wait — don't speculate on unused files." But the deterministic Figma map means the decision is trivially recoverable when the time comes.
2. **Should the iq-locker framework codify "every MANIFEST should have a 'When to consult LINEAGE' section" as a pattern?** The discoverability improvement from §6 was meaningful and would benefit all future locker instances.
3. **How should the locker handle its byte-for-byte copy of `branding/guidelines.md`?** Currently manual sync; options include symlinks, pointer docs, or a sync test. Not urgent but worth solving at the framework level before the next locker instance is built.
4. **When Wave 6 Task 19 (spawn discoverability test) runs next session, should the success criteria be updated to verify the subagent finds `iq-brand-system.md` via SKILL routing?** The original criteria only mention MANIFEST and SKILL.md files. The new source-of-truth doc is a natural target to verify.

---

## 2026-04-12 — Phase 2 Closure: Wave 5-6 Wiring + Plan Cleanup + Spawn Discoverability Test
**Phase context:** Phase 2 (Build first-instance IQ Design System Locker) — **closed**. Executed Tasks 17, 18, 19 from the 19-task build plan. User-directed mid-session pivots into legacy-path cleanup (build plan + brand audit) and stale Brand Standards remediation in the parent CLAUDE.md. Spawn test passed first try; 3 content gaps surfaced for a dedicated closure session before Phase E.
**Session log:** `sessions/2026-04-12-phase-2-closure.md`

### Discoveries

#### Top-of-file pointer banners on historical docs must live in the doc being read, not in a sibling
Last session's decision was to preserve the build plan's historical task text and rely on `LINEAGE.md §7.9` path resolution. That works only for readers who already know to consult LINEAGE — an agent opening the build plan cold has no pointer at all. This session overrode that decision and added an "AS-BUILT AMENDMENT" banner at the top of the build plan (5 numbered deltas) and a shorter "AS-BUILT POINTER" at the top of the sibling brand audit doc, each redirecting to LINEAGE §7 and the build plan amendment.
- **Downstream:** Framework pattern to codify at Phase 4: *"Wherever historical docs are preserved, a pointer banner must live at the top of the historical doc itself — not elsewhere."* Generalizes to every future locker that maintains historical planning artifacts. Open Question #4 below picks this up.

#### The spawn discoverability test empirically validates Assumption [A1] with direct evidence for the first time
Prior A1 validation was indirect (T3 Hearst POC, Claude Flow audit, Claude Code native audit). This session produced a direct, reproducible test: a blank-slate general-purpose subagent with zero prior knowledge of the locker, given only a button-variant design task in KGiQ-LLC, found the canonical source of truth in 3 reads via `CLAUDE.md` → `MANIFEST.md` → `docs/iq-brand-system.md`, explicitly read `iq-components/SKILL.md` binding rules before acting, and refused to use `--iq-accent` for interactive elements (cited §2 token routing rules). The subagent's own verdict: *"The routing from CLAUDE.md → MANIFEST.md → docs/iq-brand-system.md is excellent — a fresh agent can land on the source of truth in 3 reads."*
- **Downstream:** This is now the gold standard test pattern for any future locker instance. Phase 4 candidate: codify "every locker instance passes a domain-specific spawn discoverability test as its structural quality gate."

#### SKILL.md binding rules actively steer subagent behavior, not just document intent
The Task 19 subagent found `iq-components/SKILL.md` said *"The canonical component set is the source of truth — don't invent variants without updating `docs/iq-brand-system.md` section 6."* It then surfaced this as a scope concern, delivered a fully-resolved version of the existing Secondary variant instead of inventing a new one, and flagged in its ambiguity section that "new secondary variant" could mean three different things. The binding rule wasn't just read — it constrained output behavior.
- **Downstream:** Framework pattern: *"SKILLs that enforce scope discipline should use explicit binding language that agents can cite."* Extract to `SKILL-LOCKER-PATTERN.md` as guidance for future SKILL.md authors.

#### Parent-layer fill-token override gap — unresolved ambiguity about runtime theme assumption
`parent-layer/iq-site-tokens.css` overrides `--iq-color-surface-*` and text colors but NOT `--iq-color-fill-secondary` / `--iq-color-fill-tertiary`. In dark-first context the inherited light-mode values (`#F5F5F7`, `#EBEBED`) would glare on a slate-900 canvas. Either a real bug (parent layer should override fill tokens) or an implicit assumption that `data-theme="dark"` is set at runtime. Docs alone cannot resolve which. The spawn test subagent flagged it as an ambiguity rather than guessing.
- **Downstream:** Gap 1 action item in `docs/plans/2026-04-12-locker-gap-closure.md`. ~15 minutes investigation of how the KGiQ parent site initializes theme. Must be resolved before Phase E consumer migrations — otherwise consumers would inherit the ambiguity.

#### `iq-brand-system.md §6` is thin on concrete button specifics
§6 defines button hierarchy (5 variants) and token routing (fill, text, radius, focus ring, press transform) but does not specify padding, font-size, font-weight, hover background, hover text. The spawn test subagent had to fall back to `docs/history/iq-components-guide.md` (archived/legacy) for values — `padding: 8px 16px`, `font-size: 14px`, `font-weight: 500`. Legacy values map cleanly to existing canonical tokens (`--iq-space-2`, `--iq-space-4`, `--iq-type-body-emphasis-*`).
- **Downstream:** Gap 2 action item. Promote legacy values into §6 with canonical token routing so future consumers don't need to consult `docs/history/`. ~30-45 min with design review on hover rules. Must be resolved before Phase E.

#### Possible "Secondary = Steel Blue" regression from legacy guide
Legacy `iq-components-guide.md` documents Secondary as "Steel Blue `#5E7F9B` + White" which maps to `--iq-tint-base` on the parent site (varying by product on Booking/Publish/Circle). Canonical §6 routes Secondary to `--iq-color-fill-secondary` (neutral fill). These are fundamentally different design intents. Either §6 intentionally dropped the tinted pattern (consistent with §2's "interactive = tint, not accent" rule, possibly favoring neutral fills for Primary/Secondary hierarchy) or §6 accidentally dropped it during the 2026-04-12 consolidation rewrite.
- **Downstream:** Gap 3 action item. Confirm intent with design owner. Directly affects `iq-circle` front-end work currently in progress. Must be resolved before Phase E.

#### KGiQ-LLC had never committed the 3-session locker build — framework gap in `/om-wrap`
Last session's wrap committed iq-locker (session logs, brief, plans) but not KGiQ-LLC, where the actual locker content lives. Today's session status check surfaced that all ~130 files of locker content were sitting untracked in the KGiQ-LLC working tree across 3 sessions. Resolved for this session via a single 134-file catch-up commit with a multi-section body. But the pattern will re-emerge for any future locker that writes to a different repo than where its framework memory lives.
- **Downstream:** Framework enhancement for Phase 4: `/om-wrap` should detect working-tree state in project-related external repos and prompt for commits there too. Alternative: each locker instance declares its "write target" repos in its registry entry and wrap iterates. Open Question #5 below.

#### VENDOR-PIN in iq-circle references a commit that doesn't contain the locker
`iq-circle/iq-design-locker-vendor/VENDOR-PIN` is pinned at commit `72d9a55` — that was the KGiQ-LLC HEAD at vendoring time (earlier 2026-04-12), but it predates the locker being committed in git (which happened this session in `ab863d7`). So the VENDOR-PIN references a commit that cannot resolve the locker files via `git show`. Re-vendoring or auditing the pin would fail.
- **Downstream:** Framework bug to fix: update the VENDOR-PIN to `ab863d7` or a post-gap-closure commit. Add a pre-vendor check to `vendor-into.sh` refusing to vendor if the locker isn't committed upstream. Open Question #6 below.

#### Git rename detection kept massive reorganizations clean when staged as a unit
The 2026-04-12 branding consolidation (archive/, v1-archive/, logos/exports/, logos reorganization) initially showed 60+ file deletions + additions in `git status`. After staging with `git add -A branding/`, git auto-detected that most "deletions" were byte-identical to the new files and reclassified them as renames (`R`), preserving blame/history across the reorg. Worth knowing for future multi-file content moves.
- **Downstream:** Framework pattern: *"For content/docs work that involves large reorganizations, stage via `git add -A <dir>` rather than individual paths to leverage rename detection."* Non-critical but worth noting in framework docs.

### Assumptions Validated

- **[A1]** MANIFEST-read protocol necessary — **validated with direct evidence** via spawn discoverability test (previously indirect). This is now the strongest A1 validation.
- **[A3]** Skill locker pattern generalizes — **reinforced**. Locker absorbed Wave 5-6 wiring cleanly with no structural issues.
- **[A9]** Native Claude Code SKILL.md format is correct content format — **reinforced**. Two new CLAUDE.md entry-point files (parent + mid-tree anchor) integrated cleanly with auto-loading.
- **[A11]** Locker at `KGiQ-LLC/iq-design-locker/` is canonical — **fully validated**. Spawn test spawned in KGiQ-LLC, discovered the locker, navigated via MANIFEST, read canonical source of truth. Entire canonical-source claim held up empirically.
- **[A12]** LINEAGE.md is a structural quality gate — **reinforced with §8 addition**. Continues to be the authoritative source for "what happened to this locker" across sessions.
- **[A-new from 2026-04-11]** Structural validation is sufficient substitute for `npm test` in content/docs lockers — **strongly reinforced**. Spawn test IS the structural test. Passed first try with zero escapes. Phase 4 candidate: promote to first-class framework pattern.

### Assumptions Invalidated

None this session.

### Open Questions

1. **Framework pattern: "preserved historical docs need in-place pointer banners, not sibling redirects."** Should this be extracted to `SKILL-LOCKER-PATTERN.md` as a first-class pattern for future lockers? Meaningful discoverability improvement from this session's work on the build plan and brand audit.
2. **Framework pattern: "spawn discoverability test as structural quality gate."** Every future locker instance should probably have to pass a domain-specific spawn test. The pattern is empirically decisive and takes ~2 minutes of subagent time. Phase 4 candidate.
3. **Framework pattern: "SKILL.md binding language actively constrains subagent behavior."** Extract to `SKILL-LOCKER-PATTERN.md` as guidance: *"SKILLs enforcing scope discipline should use explicit binding language the agent can cite."* Low-urgency but worth capturing.
4. **Should `/om-wrap` detect working-tree state in project-related external repos?** This session's catch-up fix worked, but the pattern will re-emerge for any future locker that writes to a different repo than where its framework memory lives. Options: (a) `/om-wrap` auto-detects and prompts, (b) each locker instance declares its "write target" repos in the registry, (c) leave it to the user to remember. (a) is cleanest.
5. **Should `vendor-into.sh` gain a pre-vendor check refusing vendoring if the locker isn't committed upstream?** iq-circle's VENDOR-PIN references a commit that doesn't contain the locker — a real framework bug. A pre-vendor check would prevent the class of issues.
6. **iq-circle VENDOR-PIN update** — mechanical fix (`72d9a55` → `ab863d7` or later) but needs to happen before the next iq-circle vendoring. Could be bundled with the gap closure session or a separate framework-hardening task.
7. **Phase E migration order re-verification.** `LINEAGE §3.4` recommends iq-publish → booking-module → portfolio-front-end. The 2026-04-12 cross-app drift report confirms this ordering but should be verified again after the 3 locker gaps are closed (some of the gap closures may affect consumer migration scope).
