# Phase 2 Detour: Design System Consolidation — 2026-04-12

**Repo:** iq-locker
**Branch:** `main`
**Phase:** Phase 2 (Build first-instance locker) — still in progress; Waves 1-4 done from prior session; Waves 5-6 deferred to next session
**Task(s):** Originally scoped to execute Waves 5+6 (Tasks 17-19: wire `KGiQ-LLC/CLAUDE.md` + spawn discoverability test). Session direction shifted after the first substantive request surfaced the need for a major consolidation: iQ Circle registration, single source of truth for branding, stale doc archival, logo reorganization, and cross-app drift documentation.

## Session Summary

This was an unusually long, user-directed consolidation session that never got to Waves 5+6 by design. The work started with a simple product change — the former "IQCRM" space is being reimagined as iQ Circle, and the user is actively building the front-end — and cascaded into a full audit-and-cleanup pass of the iQ design system as it exists across the locker, the KGiQ-LLC brand guidelines, and the two existing consumer apps (iQ Booking, iQ Publish). Every step was explicitly user-approved. The outcome is that the locker is substantially more rigorous, cleaner, and more usable than it was at the end of Wave 4 — but at the cost of an extra session before the original scope completes.

The session produced three major deliverables: (1) a single-source-of-truth implementation reference (`iq-brand-system.md`, 508 lines) synthesized from cross-app audits of iQ Booking and iQ Publish plus the original spec and components guide; (2) a clean rewrite of `KGiQ-LLC/branding/guidelines.md` from v2.0 (624 lines, mixed content, significant drift) to v3.0 (386 lines, brand identity only, complementary to the locker); and (3) full registration of iQ Circle as a new iQ-family product including the accent CSS file, vendored locker installation in `/Users/kmgdev/dev_projects/iq-circle/`, updated open-mind.yaml, and a full logo set (5 files: wordmarks, marks, favicon) — subsequently back-filled for iQ Booking and iQ Publish so all four products have parity.

Secondary but important work: archived 8 stale branding docs under `KGiQ-LLC/branding/archive/` with an INDEX.md timeline; reorganized the locker's logo layout from `parent-layer/logos/` into three dedicated subdirectories (`logos/kgiq/`, `logos/products/`, `logos/icons/`) with consistent kebab-case naming; moved the locker's own legacy docs (spec, brief, components guide) into `docs/history/`; rerouted all 7 SKILL.md files from legacy doc references to `iq-brand-system.md` and added LINEAGE.md section-specific pointers; added a new §7 to LINEAGE.md documenting the 2026-04-12 reorganization as a first-class provenance event with a path resolution table; captured the cross-app drift findings in a standalone report at `docs/plans/2026-04-12-cross-app-drift-report.md`; and embedded everything to Neon + Claude Flow so the new source-of-truth map is queryable from any project in the future.

## What Was Built/Changed

All content changes landed in three repos: `iq-locker` (this repo, planning and session history), `KGiQ-LLC/iq-design-locker/` (the locker content), and `KGiQ-LLC/branding/` (the parent brand working directory). `iq-circle` was also seeded as a new consumer.

### New files created

**In `KGiQ-LLC/iq-design-locker/`:**
- `docs/iq-brand-system.md` (508 lines) — single source of truth for implementation
- `accents/iq-accent-circle.css` — rose accent family (`#EC4899 / #DB2777 / #F472B6 / #F9A8D4`)
- `logos/kgiq/` — 14 files (13 KGiQ SVGs + proof sheet, kebab-case names)
- `logos/products/` — 15 files (5 per product × booking, publish, circle: dk-bg/lt-bg/mark-dk/mark-lt/favicon)
- `logos/icons/` — 4 files (iq-square.svg, iq-square-dark.svg, iq-icon.svg, kgiq-transparent.svg)

**In `iq-locker`:**
- `docs/plans/2026-04-12-cross-app-drift-report.md` — Booking vs. Publish implementation drift audit

**In `iq-circle/`:**
- `iq-design-locker-vendor/` — full locker vendor (core + circle accent + parent-layer + 7 skills), pinned at commit `72d9a55`

**In `KGiQ-LLC/branding/`:**
- `archive/INDEX.md` — archive index with full brand system evolution timeline (January through April 2026)
- `logos/exports/` — PNG exports consolidated from multiple subdirectories
- `logos/v1-archive/` — pre-rebrand (v1) logo assets
- `social/sources/` — render HTML source files separated from final social assets

### Files moved or reorganized

**Inside the locker (`iq-design-locker/`):**
- `docs/iq-design-system-spec.md` → `docs/history/` (superseded by `iq-brand-system.md`)
- `docs/iq-design-system-brief.md` → `docs/history/` (superseded; had known stale data D7/D8)
- `docs/iq-components-guide.md` → `docs/history/` (superseded)
- `parent-layer/logos/KGiQ-*.svg` (10 files) → `logos/kgiq/kgiq-*.svg` (renamed to kebab-case)
- `parent-layer/logos/` (directory) — removed after contents moved

**In `KGiQ-LLC/branding/`:**
- `guidelines.md` v2.0 → `archive/guidelines-v2.0.md` (snapshot before rewrite)
- `guidelines-v1.1-proposed.md` → `archive/`
- `branding-audit-report.md` → `archive/`
- `components-guide.md` → `archive/`
- `design-tokens.css` → `archive/`
- `style-guide.html` → `archive/`
- `USAGE-GUIDE.md` → `archive/`
- `component-consistency-matrix.md` → `archive/`
- `colors-typography/brand-analysis.md` → `archive/`
- `logos/logo-*` (18 v1 files) → `logos/v1-archive/`
- `logos/iQ Brand-Logos/` → `logos/v1-archive/iQ-Brand-Logos-v2-svgs/` (SVG sources now in locker)
- `logos/logo-icon-iq*.svg` → `logos/v1-archive/`
- `logos/logo-icon-iq-square*.png` → `logos/exports/iq-square-*.png`
- `logos/iQ Brand-Logos/*.png` → `logos/exports/`
- `social/kgiq-*-render.html` → `social/sources/`

### Files modified

**In `KGiQ-LLC/iq-design-locker/`:**
- `MANIFEST.md` — updated content layout and file index for logos reorg, new `iq-brand-system.md` primary reference, added §6 "When to consult LINEAGE.md" routing table
- `README.md` — updated three-layer model to include circle accent, updated file map to point at new `logos/` subdirs and `iq-brand-system.md`
- `LINEAGE.md` — added top-of-file notice pointing to §7, added new §7 (110+ lines) documenting the 2026-04-12 reorganization with files added/moved, path resolution guide, and §2.7 flagged uncertainty status updates (brief drift now RESOLVED by archival)
- `scripts/vendor-into.sh` — added `circle` as valid `--accent` value in all 4 affected places
- `skills/iq-tokens-and-theme/SKILL.md` — updated routing from spec to `iq-brand-system.md` §2-3; added circle to accent list
- `skills/iq-typography/SKILL.md` — routing updated to `iq-brand-system.md` §4
- `skills/iq-motion/SKILL.md` — routing updated to `iq-brand-system.md` §5
- `skills/iq-components/SKILL.md` — routing updated to `iq-brand-system.md` §6 (removed components-guide and spec refs)
- `skills/iq-voice-and-tone/SKILL.md` — routing updated to `iq-brand-system.md` §8
- `skills/kgiq-parent-brand/SKILL.md` — updated logo paths to new locker structure; clarified guidelines is current (not legacy); added LINEAGE §2.6 and §7 pointers
- `skills/product-accents/SKILL.md` — added iQ Circle; expanded to four accents; added LINEAGE §2.5 and §2.7 pointers
- `logos/products/iq-publish-dk-bg.svg` and `iq-publish-lt-bg.svg` — user corrected indigo from `#6366F2` to canonical `#6366F1`; synced from branding/

**In `KGiQ-LLC/branding/`:**
- `guidelines.md` — rewritten v2.0 → v3.0 (624 → 386 lines, brand identity only, complementary scope with the locker's implementation reference)

**In `iq-circle/`:**
- `CLAUDE.md` — added Lockers section, added iq-design-locker to sibling projects, updated project structure
- `docs/open-mind.yaml` — stale `KGIQ CRM` sibling reference updated to `iq-locker`
- `docs/plans/2026-04-11-crm-evolution-concept.md` — `--iq-accent (teal, immutable)` corrected to `--iq-accent (rose #EC4899 for iQ Circle)`

**In `iq-locker`:**
- `docs/lockers-index.md` — added iq-circle as known consumer
- `docs/captures.md` — full capture with synthesized source-of-truth reference map
- `LINEAGE.md` — (see above; this is in the locker)

### Neon + Claude Flow embeddings

- **Neon (`embed_session.py` / `chunk.py`):** embedded `captures.md` (1 chunk), `iq-brand-system.md` (13 chunks), `guidelines.md` v3.0 (13 chunks; **superseded 14 stale v2.0 chunks**), and `cross-app-drift-report.md` (1 chunk)
- **Claude Flow memory_store:** three keys stored with ONNX vector embeddings — `iq-design-system-source-of-truth`, `product-accent-color-map`, `iq-locker:status-current`

## Key Decisions

| Decision | Rationale |
|----------|-----------|
| **Create a single `iq-brand-system.md` rather than fixing the existing spec/brief/guidelines** | The legacy docs had too much drift and mixed-era content to reconcile incrementally. A clean synthesis grounded in what's actually implemented (Booking + Publish audit) plus the spec and panel decisions is cleaner than 60% rewrites of three polluted files. |
| **iQ Circle accent = rose `#EC4899`** | Sourced from the canonical Figma product accent map (which also contains 5 reserved colors for future products). Without the Figma, the initial search for a reserved color turned up empty — Claude Flow memory had no product accent map, and no existing doc or CSS file pre-assigned one. The Figma screenshot provided the answer directly. |
| **Archive `branding/guidelines.md` v2.0 and write v3.0 fresh** | v2.0 was 624 lines and ~60% technically stale (wrong token namespace, wrong surface values, stale CSS code blocks, incomplete product list). Cleaning up the 60% incrementally would be error-prone and would leave residual drift. Fresh rewrite with narrowed scope (brand identity only, not implementation) is cleaner. v3.0 is 386 lines. |
| **Scope split: guidelines.md = identity, iq-brand-system.md = implementation** | Before the locker existed, guidelines.md had to be everything. Now the locker owns implementation (tokens, components, motion, setup). Guidelines should own the "who we are / how we look" half — logos, dot-as-signature, tech-nerd aesthetic, voice. Every section in v3.0 that touches implementation has a one-line pointer to the locker. Mutual non-overlap, no drift potential. |
| **Logo reorganization: `logos/kgiq/`, `logos/products/`, `logos/icons/`** | The previous layout had two overlapping sets of KGiQ logos (v1 in branding root, v2 in `iQ Brand-Logos/`) plus inconsistent naming (spaces, mixed casing, two different prefix conventions). The new structure puts canonical SVG sources in the locker with consistent kebab-case naming, and PNG exports stay in `branding/logos/exports/`. v1 goes to `v1-archive/`. |
| **Generate product marks and favicons by viewBox cropping, not redrawing** | The three base product logos (Booking, Publish, Circle) use identical iQ letterform path coordinates — only the dot color differs. Cropping the viewBox around just the iQ mark gives a clean mark variant with zero risk of geometric drift. Same technique for favicons: base path + centered group transform + rounded rect background. |
| **Favicon uses dark slate background, not product accent background** | KGiQ's favicon uses a gold background (because gold is a very light color — luminance ~0.76 — allowing dark iQ letters with contrast). Rose and teal and indigo are medium-dark (luminance ~0.24-0.30), which would make both white and dark iQ letters borderline for contrast. Cleaner to use a dark slate `#1E293B` background with white iQ and the product-colored dot — matches the dk-bg wordmark aesthetic and keeps the product dot as the signature. |
| **Add LINEAGE §7 rather than rewriting §1 rows with new paths** | §1-6 document Waves 1-4 as they happened. Rewriting rows to show new paths would destroy the historical record of "where did this file originally land." Adding §7 as a first-class LINEAGE event (with a path resolution table in §7.9) preserves history and keeps the file accurate. |
| **Archive 8 stale branding docs to `branding/archive/` with an INDEX.md** | The user wanted aggressive archival with no data loss. The INDEX.md explains what each archived file was, why it was archived, and a full timeline of brand system evolution from January through April 2026. Anyone doing historical research has a single reference. |
| **Pin vendored locker in iq-circle at commit `72d9a55`** | Standard vendor-pin convention established during Wave 4. `VENDOR-PIN` file records the exact locker commit used so re-vendoring is auditable. |
| **Back-fill Booking and Publish with marks and favicons for parity with Circle** | iQ Circle ended up with 5 logo files (wordmarks + marks + favicon) while Booking and Publish only had 2 (wordmarks). User direction: all products should have the same coverage. Generated 6 new files by cropping/substituting colors on the Circle pattern. |
| **Do not rewrite historical session logs or plans that reference archived paths** | `docs/sessions/2026-04-10-*.md`, `docs/sessions/2026-04-11-*.md`, `docs/plans/iq-design-system-build-plan.md`, and `docs/plans/iq-design-system-brand-audit.md` all reference `parent-layer/logos/` and `iQ Brand-Logos/` paths. These are historical records describing what was true at the time they were written. LINEAGE §7.9 provides a path resolution table for anyone reading them later. |
| **Three-layer persistence for the source-of-truth map** | The "where does iQ branding live" knowledge is the kind of thing that should survive anything. Stored in: files (captures.md), Claude Flow memory (HNSW vectors, fast local search), and Neon (pgvector embeddings, durable cross-project). Any future agent query in any project can find the answer. |

## Discoveries

- **Discovery: The cross-app audit surfaced significant implementation drift between iQ Booking and iQ Publish that was not visible from the spec alone.**
  - **Impact:** iQ Publish has `--iq-color-info: #3B82F6` hardcoded (the exact issue that drove the Wave 1 base-selection reversal a session ago — it was never fixed in Publish itself, only in the locker core). iQ Publish is also missing Plus Jakarta Sans and JetBrains Mono from its font loading, so all headings silently fall back to system UI. iQ Booking has a calendar page badge hardcoded with Slate-800 colors that will not adapt to light mode. Booking's `glassCard` pattern is applied to L1 cards, which may violate the 2026-03-29 panel decision that restricts glassmorphism to L3+ admin surfaces. The booking page surface uses `--iq-accent` instead of `--iq-tint` for focus rings. These are all Phase E migration work items now documented in `docs/plans/2026-04-12-cross-app-drift-report.md`.

- **Discovery: The canonical Figma product accent map contains 5 reserved colors for future products that were not documented anywhere in code, memory, or docs.**
  - **Impact:** Menu (red #EF4444), Family Finance (green #22C55E), Ledger (purple #A855F7), Next? (lime #84CC16), and Blue Variant / Tight Corridor (blue #0284C7) are all pre-assigned. Without this map, each new product would face the same "what color?" decision process that iQ Publish went through (violet → coral → indigo iteration). The full map now lives in three redundant places: `iq-brand-system.md` §1, `guidelines.md` v3.0 §3, and Claude Flow memory (`product-accent-color-map` key). An agent in any project can query for "reserved product colors" and get the answer.

- **Discovery: `KGiQ-LLC/branding/guidelines.md` v2.0 had fundamentally different surface color values than the locker.**
  - **Impact:** The guidelines doc used Tailwind Slate (canvas `#0f172a`, primary `#1e293b`) while the locker uses Apple-style neutral blacks (canvas `#000000`, primary `#1C1C1E`). This isn't a typo — it's two different design philosophies that were never reconciled. The guidelines' token namespace was also different (`--brand-*`, `--surface-*`, `--bg-*`) compared to the locker's `--iq-*`. Any agent reading guidelines.md for implementation guidance would produce visibly different UIs from what the locker's CSS would render. Root cause: guidelines.md was written in the KGiQ parent brand era and then partially updated during the v2.0 rebrand but never fully reconciled with the iQ product system tokens. v3.0 resolves this by scoping guidelines.md to identity only and deferring all implementation details to the locker.

- **Discovery: iQ Publish and iQ Booking both define `.iq-display`, `.iq-title-1`, etc. utility classes in `iq-typography.css` but NEITHER app actually uses them.**
  - **Impact:** Both apps apply typography via inline `style` props referencing tokens directly (`style={{ font: 'var(--iq-font-heading)', fontSize: '22px' }}`). The utility classes are dead code in both implementations. `iq-brand-system.md` §4 documents this and recommends new apps adopt the utility classes. Doesn't block anything but is a consistency opportunity for Phase E or future new-app bootstrapping.

- **Discovery: iQ Publish's base wordmark SVGs had the indigo dot as `#6366F2`, off by one digit from the canonical `#6366F1`.**
  - **Impact:** Minor cosmetic drift between the logo files and the token system / accent CSS / brand guidelines. The user corrected both SVGs in Figma during this session after I flagged it. The new product marks and favicon I created use the canonical `#6366F1`. The panel doc `docs/history/2026-03-29-brand-cohesion-expert-panel.md` has the same `#6366F2` typo (flagged in LINEAGE §5 D3) but can be footnoted on next panel-doc edit without any runtime impact.

- **Discovery: All three product base logos use byte-identical iQ letterform path coordinates — only the dot color differs.**
  - **Impact:** Mark and favicon variants for Booking, Publish, and Circle can be generated from any one base logo by viewBox cropping and dot-color substitution, with zero risk of geometric drift. This technique kept the pattern clean across all 9 generated variants (3 products × 3 variants each). If a future product (from the reserved 5 — Menu, Family Finance, Ledger, Next?, or the Blue Variant) needs the same logo set, it'll be trivial to generate from whatever base wordmark the designer creates.

- **Discovery: The locker's `parent-layer/kgiq-brand-guidelines.md` was a byte-for-byte verbatim copy of `KGiQ-LLC/branding/guidelines.md` (both 21,882 bytes, matching MD5).**
  - **Impact:** When I updated `branding/guidelines.md` to v3.0, I also had to sync the locker's copy to avoid drift between the two locations. This suggests the locker could eventually adopt a lighter pattern (e.g., a symlink or a pointer note) instead of a verbatim copy, but that's a framework-level consideration for another session. For now, the sync is manual; future updates to guidelines.md need to remember to sync the locker copy.

- **Discovery: The LINEAGE.md file was accurate for Wave 1-4 provenance but had no discovery path for "migration planning" or "drift investigation" readers.**
  - **Impact:** An agent doing Phase E migration wouldn't know LINEAGE §3 contains pre-written consumer delta reports. An agent investigating drift wouldn't know §2 has the reconciliation log. The MANIFEST's references to LINEAGE were generic ("see for audit history") without pointing at specific sections. Added §6 "When to consult LINEAGE.md" to the MANIFEST with a routing table mapping situations to LINEAGE sub-sections. Also added section-specific LINEAGE pointers to the `product-accents` and `kgiq-parent-brand` SKILLs. Discoverability improvement; doesn't change LINEAGE content itself.

## Assumptions Validated/Invalidated

Cross-reference with `docs/brief.md` Assumptions Registry:

- **[A1]** Subagents don't auto-inherit parent-session skill registrations. → **unchanged** — the cross-app audits confirmed this again via the MANIFEST-read protocol working as expected during the locker build.
- **[A3]** Skill locker pattern generalizes from vendored-upstream to self-authored content. → **further reinforced**. The locker successfully absorbed a major consolidation (new canonical doc, archived legacy docs, reorganized directory structure) without any breakage. The three-layer content model (core + accents + parent-layer) held up cleanly under expansion to four products.
- **[A9]** Native Claude Code SKILL.md format is the correct content format. → **validated again**. All 7 active SKILLs were updated cleanly in-place with section-specific routing to the new `iq-brand-system.md` and LINEAGE references. The frontmatter-plus-body format handles these updates naturally.
- **[A11]** Locker lives at `KGiQ-LLC/iq-design-locker/` and IS canonical. → **reinforced**. Registration of iQ Circle worked exactly as the framework anticipated — new accent file, MANIFEST update, SKILL update, LINEAGE entry, then vendor into the new consumer project.
- **[A12]** `LINEAGE.md` backwards-looking provenance audit is a structural quality gate. → **validated with caveat**. LINEAGE worked as a write target (every maintenance action recorded a new entry), but had a discoverability gap for read use cases. Resolved by adding the MANIFEST §6 routing table and SKILL-level section pointers. Worth codifying as a framework pattern: "every locker's MANIFEST should have a 'When to consult LINEAGE' section with use-case → section mappings."
- **[A-new]** Structural validation is a sufficient substitute for `npm test` in content/docs locker repos. → **further reinforced**. No bugs escaped this session despite the large surface area (12+ files modified, 30+ files added/moved, 8 archived, 22 logo files written, 3 markdown files rewritten from scratch). Running `vendor-into.sh --accent circle` end-to-end was the integration test; it worked on the first try.

## Problems & Solutions

| Problem | Resolution |
|---------|------------|
| Initial search for a reserved iQ Circle accent color turned up nothing in Claude Flow memory, Neon, brand docs, or existing CSS files | User provided the canonical Figma product accent map via screenshot. All 9 colors now documented in three locations (locker doc, guidelines v3.0, CF memory). |
| `KGiQ-LLC/branding/guidelines.md` v2.0 had ~60% technical drift against the locker (wrong token namespace, wrong surface values, stale CSS code blocks) | Archived v2.0 and wrote v3.0 fresh, narrowed to brand identity only. Clean scope separation with the locker's implementation reference. |
| Cross-app audit of Booking and Publish revealed significant drift that wasn't visible from the spec | Documented in standalone `docs/plans/2026-04-12-cross-app-drift-report.md` — all findings catalogued per-app with specific file:line references for Phase E migration work. |
| Legacy branding docs were mixed with current assets in `KGiQ-LLC/branding/` | Created `branding/archive/` with 8 archived files + `INDEX.md` timeline. Parent-layer copy of guidelines synced to v3.0. |
| The locker's `parent-layer/logos/` had inconsistent structure vs. what a full product family needs | Reorganized into `logos/kgiq/`, `logos/products/`, `logos/icons/` with kebab-case naming. Removed `parent-layer/logos/` entirely. Updated MANIFEST, README, and the `kgiq-parent-brand` SKILL. |
| iQ Publish base wordmarks had `#6366F2` instead of canonical `#6366F1` | User corrected both SVGs in Figma mid-session; synced to locker copies; new marks/favicons use canonical value. |
| The locker's LINEAGE.md had stale paths after the logo reorg — entries in §1 pointed at `parent-layer/logos/*` files that no longer exist | Added §7 as a first-class reorganization event with §7.9 path resolution table. §1-6 preserved as historical record. |
| LINEAGE.md was not reliably discoverable for read use cases (migration planning, drift investigation) | Added §6 "When to consult LINEAGE" to MANIFEST with situation → section routing table. Added section-specific LINEAGE pointers to `product-accents` and `kgiq-parent-brand` SKILLs. |
| iQ Circle project had stale sibling reference to "KGIQ CRM" in its `open-mind.yaml` | Updated to reference `iq-locker`. Also fixed a stale `--iq-accent (teal, immutable)` reference in `docs/plans/2026-04-11-crm-evolution-concept.md` to reflect rose for iQ Circle. |
| iQ Circle logo coverage was complete (5 variants) but Booking and Publish only had 2 (wordmarks) | Back-filled 6 files using the same viewBox-crop technique on the existing base wordmarks. All three products now have wordmark × {dk, lt} + mark × {dk, lt} + favicon parity. |
| Social directory had render HTML source files mixed with final assets | Moved render HTMLs to `social/sources/` subdirectory. |
| Guidelines v2.0 archive snapshot needed before the overwrite | Copied to `archive/guidelines-v2.0.md` before running the v3.0 write. |

## Open Questions

1. **Waves 5+6 are still pending.** Tasks 17 (wire `KGiQ-LLC/CLAUDE.md` Lockers entry), 18 (nested locker `CLAUDE.md`), 19 (spawn discoverability test). The detour was high-value but expensive in session time; next session should execute these without further scope expansion unless absolutely necessary.
2. **Wave 6 Task 19 (spawn discoverability test) will be stronger because of this session's work.** The new `iq-brand-system.md` is a cleaner target than the previous spec + brief + components guide trio. But that also means the success criteria might need a minor update — the test currently says "subagent reads `iq-design-locker/MANIFEST.md` and reads one or more relevant SKILL.md files." It should probably also verify the subagent finds `iq-brand-system.md` via the SKILL routing. Minor tweak, non-blocking.
3. **Cross-app drift from the 2026-04-12 report is real Phase E work.** The biggest items: iQ Publish must fix its hardcoded `--iq-color-info` and add Plus Jakarta Sans + JetBrains Mono to font loading. iQ Booking must fix the calendar page badge and evaluate the L1 glass card policy violation. These are separate sessions per consumer, per the session-separation convention. Order recommendation per LINEAGE §3.4 still stands: iq-publish (S) → booking-module (M) → portfolio-front-end (L).
4. **Should future new iQ products (Menu, Family Finance, Ledger, Next?, Blue Variant) get their accent CSS files created proactively, or wait until an actual app needs them?** The colors are documented in `iq-brand-system.md` §1 but no CSS files exist. Leaning toward "wait" — don't speculate on files that aren't used. But the Figma map means the decision is deterministic when the time comes.
5. **The `parent-layer/kgiq-brand-guidelines.md` / `branding/guidelines.md` sync problem.** Currently these are byte-for-byte identical and must be manually synced on every guidelines update. Options for the future: (a) make one a symlink, (b) add a sync test to the locker framework, (c) make the locker's copy a pointer doc that references the canonical source. Not urgent; sync worked fine this session. Worth noting for iq-locker framework evolution.
6. **Should the iq-locker framework codify "every MANIFEST should have a 'When to consult LINEAGE' section" as a pattern?** The discoverability improvement from §6 was meaningful. If other locker instances are built in the future, they'll benefit from the same routing table convention. Worth extracting to the framework-level `SKILL-LOCKER-PATTERN.md` eventually.
7. **The `guidelines-v1.1-proposed.md` in the archive was never formally resolved against v2.0.** It's in `branding/archive/` now and INDEX.md notes it was superseded, but some of its proposed changes may or may not have made it into v2.0. Archaeological question; non-blocking.

## Tracked Files Changed

Files in the tracked_files list from `docs/open-mind.yaml` that were created or updated this session:

- `docs/sessions/2026-04-12-design-system-consolidation.md` (this file, new)
- `docs/captures.md` (appended with full synthesized source-of-truth reference)
- `docs/discovery-log.md` (will be appended in Step 5)
- `docs/lockers-index.md` (iQ Circle added as known consumer; "Known consumer projects" updated)
- `docs/plans/2026-04-12-cross-app-drift-report.md` (new)

**NOT changed in this session (by design):**
- `docs/brief.md` — Phase 2 is still in progress (Waves 5+6 pending). No phase boundary. Will be updated by the next session's wrap if Wave 6 spawn test passes.
- `docs/plans/iq-design-system-build-plan.md` — historical plan; references archived paths (the build plan was written at 2026-04-10). Not touching. LINEAGE §7.9 provides path resolution for any reader.
- `docs/plans/iq-design-system-brand-audit.md` — historical audit. Same rationale.

**Changed outside this repo (expected, not tracked by iq-locker):**
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/` — full consolidation (see "What Was Built/Changed" above for all files)
- `/Users/kmgdev/KGiQ-LLC/branding/` — guidelines.md v3.0 rewrite, archive/ directory, logo reorganization, social/ cleanup
- `/Users/kmgdev/dev_projects/iq-circle/` — locker vendored, CLAUDE.md updated, open-mind.yaml fixed, concept doc fixed

## Next Steps

1. **Wrap and commit this session.** (In progress.)
2. **Next session — Execute Waves 5+6.** `/om-go` → "Continue last session's work" → run Tasks 17 (`KGiQ-LLC/CLAUDE.md` Lockers entry), 18 (nested locker `CLAUDE.md`), 19 (spawn discoverability test). Should take ~30 min across all three tasks.
3. **When Wave 6 passes:** flip `docs/lockers-index.md` status from `scoping` to `validated` and mark Phase 2 complete in `docs/brief.md` Phase Tracker.
4. **Phase E consumer migrations** — separate sessions, one per consumer. Recommended order: iq-publish (S, fix `--iq-color-info` + fonts + `no-membership/page.tsx`) → booking-module (M, calendar badge + glass policy review + focus ring) → portfolio-front-end (L, the visible UI shift on the embedded widget). Full details in `docs/plans/2026-04-12-cross-app-drift-report.md`.
5. **Brief updates** — the stale data points D7/D8 in the (now-archived) brief are resolved by the archive. But the parallel stale data in iq-publish's own `docs/context/iq-design-system-brief.md` still exists and needs handling during the iq-publish migration session.

## Continuation Prompt

> Continue Phase 2: IQ Design System Locker build — Waves 5+6 only — in iq-locker.
>
> **Last session recap:** The session started aiming for Waves 5+6 but was redirected by the user into a major consolidation pass. Outcomes: created `docs/iq-brand-system.md` (508 lines, single source of truth for implementation), registered iQ Circle with rose accent `#EC4899` and vendored the locker into the iq-circle project, rewrote `KGiQ-LLC/branding/guidelines.md` from v2.0 (624 lines, mixed content) to v3.0 (386 lines, brand identity only with clean scope split from the locker), archived 8 stale branding docs with an INDEX timeline, reorganized logos in the locker from `parent-layer/logos/` to `logos/{kgiq,products,icons}/` with kebab-case naming, back-filled iQ Booking and iQ Publish with marks and favicons so all four products have full logo parity, added `LINEAGE.md` §7 documenting the reorganization as a first-class provenance event, rerouted all 7 SKILLs from legacy docs to `iq-brand-system.md` with section-specific LINEAGE pointers, and captured cross-app drift in `docs/plans/2026-04-12-cross-app-drift-report.md`. Everything embedded to Neon + Claude Flow.
>
> **Priority:** Run `/om-exec --wave 5` (or equivalent) on `docs/plans/iq-design-system-build-plan.md` to execute Tasks 17, 18, 19. The build plan still references the old path conventions (`parent-layer/logos/`, `docs/iq-design-system-spec.md`), but that's fine for Tasks 17/18/19 because those tasks don't touch those files. `LINEAGE.md` §7.9 has the path resolution table if anything in the tasks confuses the agent.
>
> **Key context:**
> - Locker at `KGiQ-LLC/iq-design-locker/` now has full content parity for the iQ family: 4 products (kgiq/booking/publish/circle), each with wordmarks + marks + favicon in `logos/products/` and `logos/kgiq/`. iQ Circle is registered and vendored.
> - **`docs/iq-brand-system.md` is the single source of truth** for implementation. Task 19's spawn discoverability test should probably verify the subagent finds this file via MANIFEST/SKILL routing, not just the SKILL.md files themselves. Minor update to the Task 19 success criteria worth considering.
> - **The Wave 6 test may be stronger than originally planned** because the locker is cleaner now. If the spawn test passes on the first try, flip `docs/lockers-index.md` status from `scoping` to `validated` and mark Phase 2 complete in `docs/brief.md`. If it fails, iterate on the `KGiQ-LLC/CLAUDE.md` Lockers entry copy with more trigger keywords and log iterations in `LINEAGE.md` §6 (Quality-gate history).
> - **Task 17 uses master-index convention** from `docs/lockers-index.md`: single lightweight section in `KGiQ-LLC/CLAUDE.md` pointing to `iq-design-locker/MANIFEST.md`. Front-load trigger keywords (styling, brand, colors, typography, components, voice).
> - **Known Phase E migrations** — separate sessions per consumer, in order: iq-publish → booking-module → portfolio-front-end. Full details in `docs/plans/2026-04-12-cross-app-drift-report.md`. Do NOT start these until Phase 2 is formally complete.
> - **Quality-gate policy still applies:** fix all review suggestions and re-review until clean. Don't defer minor findings.
>
> **Brief:** `docs/brief.md` (iq-locker framework philosophy; Phase 2 currently shows "in progress").
> **Build plan:** `docs/plans/iq-design-system-build-plan.md` (Tasks 17-19 at lines ~305+).
> **Source of truth map:** Claude Flow memory key `iq-design-system-source-of-truth` and Neon embeddings of `iq-brand-system.md` and `guidelines.md` v3.0.
