# IQ Design System Locker — Build Plan

**Target:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/`
**Origin session:** 2026-04-10-4d94
**Full rationale:** `docs/plans/iq-design-system-brand-audit.md` (read first for history, drift findings, canonical-source framing, and build philosophy)
**Scope:** Build the first-instance iq-locker locker — the IQ Design System — as the canonical source of truth for the IQ brand, consolidating three drifted copies scattered across booking-module / iq-publish / portfolio-front-end. Phases A–D in scope (extract, audit, wrap in skills, wire + test). Phase E (consumer-project reconciliation) is listed for completeness at the bottom but out of scope for this build.

---

> ## AS-BUILT AMENDMENT — READ BEFORE ACTING ON THIS PLAN (2026-04-12)
>
> This plan was authored 2026-04-10 and executed across 2026-04-11 (Waves 1-4, 18 tasks) and 2026-04-12 (a consolidation detour). **Five specific assumptions in this plan are now obsolete.** Do not use the plan text alone to make design/architectural decisions — reconcile with the deltas below first, then consult `KGiQ-LLC/iq-design-locker/LINEAGE.md` §7 for authoritative current state.
>
> **1. The base for `core/iq-tokens.css` was reversed from iq-publish to booking-module.** Pre-decision #4 and Task 4 as written are obsolete. At Wave 2 runtime the user explicitly overrode the pre-decision because iq-publish had hardcoded `--iq-color-info: #3B82F6`, severing the semantic-info ↔ accent linkage. The canonical base is **booking-module**. See LINEAGE §2.2.
>
> **2. Four active accents, not three.** `accents/iq-accent-circle.css` (rose `#EC4899`) was added 2026-04-12 for iQ Circle. Task 5 lists only kgiq/publish/booking; the current active set is **kgiq/publish/booking/circle**. The canonical Figma product accent map (9 colors total — 4 active, 5 reserved for Menu/Family Finance/Ledger/Next?/Blue Variant) is documented in `iq-brand-system.md` §1 and LINEAGE §7.2.
>
> **3. `docs/iq-brand-system.md` is the single source of truth for implementation — not the spec/brief/components-guide.** The 508-line `iq-brand-system.md` was synthesized 2026-04-12 from cross-app audits of iQ Booking + iQ Publish. Tasks 7 and 13 route SKILLs to the original spec/brief/components-guide; those three were archived to `docs/history/` on 2026-04-12 and **superseded**. All 7 active SKILL.md files now route to `iq-brand-system.md` sections. See LINEAGE §7.2 and §7.5.
>
> **4. Logos were reorganized and renamed.** Task 6 describes `parent-layer/logos/KGiQ-*.svg` (PascalCase). Current structure:
> - `logos/kgiq/kgiq-*.svg` (13 files + proof sheet, kebab-case) — KGiQ parent brand
> - `logos/products/iq-{booking,publish,circle}-{dk-bg,lt-bg,mark-dk,mark-lt,favicon}.svg` (15 files) — all four products have wordmark + mark + favicon parity
> - `logos/icons/` (4 files) — iQ mark and app icons
> - `parent-layer/logos/` — **removed**
> - Full old→new path table: LINEAGE §7.9
>
> **5. `KGiQ-LLC/branding/guidelines.md` is v3.0** (386 lines, brand identity only — clean scope split from the locker's implementation reference). Task 6 describes copying v2.0 verbatim; the locker's `parent-layer/kgiq-brand-guidelines.md` was synced to v3.0 on 2026-04-12. v2.0 is archived at `branding/archive/guidelines-v2.0.md`. See LINEAGE §7.6.
>
> **Authoritative references for current locker state:**
> - `KGiQ-LLC/iq-design-locker/LINEAGE.md` §7 (especially §7.9 path resolution table)
> - `KGiQ-LLC/iq-design-locker/docs/iq-brand-system.md` (implementation reference)
> - `KGiQ-LLC/iq-design-locker/MANIFEST.md` (active skill curation)
>
> **Sibling doc also affected:** `docs/plans/iq-design-system-brand-audit.md` (pre-build rationale) carries the same pre-consolidation framing. A pointer at the top of that file redirects readers here and to LINEAGE §7.

---

## Pre-decided canonicalization decisions

Locking in decisions that would otherwise require mid-execution judgment. This makes the plan deterministic and safe for automation.

1. **Neutral default accent in `core/iq-tokens.css`:** the `--iq-accent` variable stays in `core/iq-tokens.css` with a **KGiQ parent gold (`#FFD166`) default**. Accent override files redefine it via import order. Gold is the sensible fallback if a consumer imports only `core/`. The misleading `/* === Accent (iQ Publish product override) === */` comment is replaced with a neutral header explaining the three-layer tint model.

2. **Hover + dark-mode gold derivations:** hover = `#E6BC5C` (10% darker), dark-mode = `#FFDB8F` (lighter for dark backgrounds), dark-hover = `#FFE4A8`. Eyeballed from `#FFD166`; flag for human review during spec verification (Task 12).

3. **Product accent override file shape:**
   ```css
   /* iq-accent-<product>.css — product accent override for <Product Name>
      Import AFTER core/iq-tokens.css to override the default. */
   :root {
     --iq-accent: #HHHHHH;
     --iq-accent-hover: #HHHHHH;
     --iq-accent-dark: #HHHHHH;
     --iq-accent-dark-hover: #HHHHHH;
   }
   [data-theme="dark"] {
     --iq-accent: #HHHHHH;
     --iq-accent-hover: #HHHHHH;
   }
   ```

4. **Source preference order for core files:** iq-publish's version is the most recently updated production copy; use it as the base unless a diff surfaces substantive drift that requires judgment. In that case, flag in `LINEAGE.md` and fall back to human review.

5. **`parent-layer/iq-site-tokens.css` extraction:** extract verbatim from `portfolio-front-end/src/styles/iq-site-tokens.css`, then remove the `@import` lines (consumer projects import core/ themselves) and move `--iq-product-accent` to `accents/iq-accent-kgiq.css`. The remaining file handles dark-first surfaces, text, shadows, borders — the KGiQ parent visual layer.

## Extract and canonicalize

### Task 1: Create locker directory structure

- **Wave:** 1
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** none
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/ (inferred from description — directory tree)

Create `/Users/kmgdev/KGiQ-LLC/iq-design-locker/` with subdirectories: `core/`, `accents/`, `parent-layer/`, `parent-layer/logos/`, `docs/`, `docs/history/`, `skills/`, `scripts/`. Foundational — blocks all subsequent work.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/` (directory tree)

### Task 2: Diff core CSS files across all source copies

- **Wave:** 1
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** none
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-tokens.css, /Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-typography.css, /Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-motion-tokens.css, /Users/kmgdev/dev_projects/iq-publish/vendored/iq-tokens.css, /Users/kmgdev/dev_projects/iq-publish/vendored/iq-typography.css, /Users/kmgdev/dev_projects/iq-publish/vendored/iq-motion-tokens.css, /Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-tokens.css, /Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-typography.css, /Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-motion-tokens.css

Run byte-diffs for `iq-tokens.css`, `iq-typography.css`, and `iq-motion-tokens.css` across the three source locations:
- `/Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/`
- `/Users/kmgdev/dev_projects/iq-publish/vendored/`
- `/Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/`

Expected: `iq-typography.css` and `iq-motion-tokens.css` are byte-identical across all three. `iq-tokens.css` has known drift (teal / indigo / indigo accent blocks). Record exact diff output for Task 9 (drift documentation). This task is investigation-only, no writes to the locker.

**Files:** `/Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-tokens.css`, `/Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-typography.css`, `/Users/kmgdev/dev_projects/booking-module/packages/shared-ui/src/styles/iq-motion-tokens.css`, `/Users/kmgdev/dev_projects/iq-publish/vendored/iq-tokens.css`, `/Users/kmgdev/dev_projects/iq-publish/vendored/iq-typography.css`, `/Users/kmgdev/dev_projects/iq-publish/vendored/iq-motion-tokens.css`, `/Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-tokens.css`, `/Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-typography.css`, `/Users/kmgdev/dev_projects/portfolio-front-end/src/styles/vendored/iq-motion-tokens.css`

### Task 3: Copy iq-typography.css and iq-motion-tokens.css to locker core/

- **Wave:** 2
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** Task 1, Task 2
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-typography.css, /Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-motion-tokens.css

Using iq-publish's version as the canonical base (unless Task 2 surfaces a reason to choose differently), copy both files to the locker. No edits unless Task 2 flagged drift.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-typography.css`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-motion-tokens.css`

### Task 4: Extract and canonicalize core/iq-tokens.css

- **Wave:** 2
- **Agent:** coder
- **Complexity:** high
- **Model:** opus
- **Depends on:** Task 1, Task 2
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-tokens.css

Start from `iq-publish/vendored/iq-tokens.css`. Remove the `--iq-accent` block (both the light and dark-mode variants) and the misleading "iQ Publish product override" comment. Replace with a new neutral-default accent block using KGiQ parent gold per pre-decision #1 and #2. Update the file header to reference the three-layer tint model and the `accents/` override convention. This is the highest-judgment task in the build — if the diff from Task 2 surfaces anything unexpected, pause for human review.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-tokens.css`

### Task 5: Create three product accent override files

- **Wave:** 2
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 1
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-kgiq.css, /Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-publish.css, /Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-booking.css

Create `accents/iq-accent-kgiq.css` (gold), `accents/iq-accent-publish.css` (indigo, from the block removed in Task 4), and `accents/iq-accent-booking.css` (teal, extracted from booking-module's copy). Each follows the shape in pre-decision #3, including dark-mode variants. Verify each product's hex values against its source of origin.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-kgiq.css`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-publish.css`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/accents/iq-accent-booking.css`

### Task 6: Extract parent-layer files from portfolio-front-end and KGiQ-LLC

- **Wave:** 2
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 1
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/iq-site-tokens.css, /Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/kgiq-brand-guidelines.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/logos/

Extract `parent-layer/iq-site-tokens.css` from portfolio-front-end per pre-decision #5 (remove imports, remove `--iq-product-accent`). Copy `KGiQ-LLC/branding/guidelines.md` to `parent-layer/kgiq-brand-guidelines.md` verbatim. Copy the KGiQ logo SVG files from `KGiQ-LLC/branding/logos/iQ Brand-Logos/` to `parent-layer/logos/` using the `KGiQ-dk-bg` / `KGiQ-lt-bg` naming convention per the 5-expert panel recommendation. Include the 12-variant proof sheet.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/iq-site-tokens.css`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/kgiq-brand-guidelines.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/logos/`

### Task 7: Copy authoring spec, operational brief, components guide, and panel history to docs

- **Wave:** 2
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** Task 1
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-design-system-spec.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-design-system-brief.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-components-guide.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/history/2026-03-29-brand-cohesion-expert-panel.md

Copy four documents from their source locations into the locker's `docs/` tree:
- `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md` → `docs/iq-design-system-spec.md` (THE authoring source)
- `iq-publish/docs/context/iq-design-system-brief.md` → `docs/iq-design-system-brief.md` (operational contract)
- `iq-publish/docs/context/iq-components-guide.md` → `docs/iq-components-guide.md`
- `KGiQ-LLC/docs/plans/2026-03-29-brand-cohesion-expert-panel.md` → `docs/history/2026-03-29-brand-cohesion-expert-panel.md`

No edits — these are vendored verbatim to preserve authoring history.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-design-system-spec.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-design-system-brief.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-components-guide.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/history/2026-03-29-brand-cohesion-expert-panel.md`

## Backwards-looking provenance audit

### Task 8: Write LINEAGE.md skeleton with per-file provenance table

- **Wave:** 3
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 3, Task 4, Task 5, Task 6, Task 7
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md

Create `LINEAGE.md` at the locker root with sections for: per-file provenance table, drift reconciliation log, consumer-project delta report, completeness check, spec verification. Fill in the per-file provenance table with entries for every file created by Tasks 3, 4, 5, 6, 7 — recording source project, source path, extraction date (2026-04-10), and any modifications made. Reference the relevant task numbers from this plan.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`

### Task 9: Byte-compare locker files against sources and document drift reconciliation

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 2, Task 8
- **Parallel:** no
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md

Using the diff output from Task 2, append a drift reconciliation section to `LINEAGE.md`. Document exactly what was changed during canonicalization (accent block removal, comment replacement, dark-mode block adjustment). Include the removed content as an appendix for historical traceability. Flag any unexpected drift the diff surfaced.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`

### Task 10: Generate consumer-project delta report

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 8
- **Parallel:** no
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md

Append a consumer-project delta report to `LINEAGE.md`. For each consumer project (booking-module, iq-publish, portfolio-front-end), list which files would need to change to adopt the locker as canonical. Include file paths, line numbers where relevant, and the nature of each change. This becomes the Phase E reconciliation backlog.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`

### Task 11: Completeness grep across all surveyed projects

- **Wave:** 4
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** Task 8
- **Parallel:** no
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md

Run `grep -r` (via the Grep tool) for `iq-tokens`, `iq-typography`, `iq-motion-tokens`, `iq-site-tokens`, `iq-design-system-spec`, `iq-design-system-brief` across booking-module, iq-publish, portfolio-front-end, upwork, and KGiQ-LLC. Append a completeness check section to `LINEAGE.md` listing any hit not already accounted for in the locker. Each uncovered hit becomes a follow-up item.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`

### Task 12: Verify authoring spec against extracted token values

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 3, Task 4, Task 5, Task 8
- **Parallel:** no
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md

Read `docs/iq-design-system-spec.md` end-to-end. Compare its prescribed token values against what's in `core/iq-tokens.css`, `core/iq-typography.css`, `core/iq-motion-tokens.css`, and the three accent files. Append a spec verification section to `LINEAGE.md` noting any disagreements — these are candidate drift findings that may need to be resolved in either the spec or the implementation. Also verify the hover/dark-mode gold derivations from pre-decision #2 are reasonable.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`

## Wrap in Claude Code skills

### Task 13: Write SKILL.md wrappers for active and dormant skills

- **Wave:** 3
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 7
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-tokens-and-theme/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-typography/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-components/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-motion/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/kgiq-parent-brand/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-voice-and-tone/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/product-accents/SKILL.md, /Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/tech-nerd-aesthetic/SKILL.md

Create 8 SKILL.md files using native Claude Code frontmatter format (name, description, optional triggers/allowed-tools/model). Each is thin — a few paragraphs explaining when to invoke, plus pointers into the locker's `core/`, `accents/`, `parent-layer/`, or `docs/` directories. Active skills:
- `skills/iq-tokens-and-theme/SKILL.md` — tokens, surfaces, tint cascade
- `skills/iq-typography/SKILL.md` — three-font stack, type scale
- `skills/iq-components/SKILL.md` — canonical component set, glassmorphism marked dormant-for-customer-facing
- `skills/iq-motion/SKILL.md` — motion tokens, easing, reduced-motion
- `skills/kgiq-parent-brand/SKILL.md` — parent palette, logos, tagline, voice pillars
- `skills/iq-voice-and-tone/SKILL.md` — product-layer voice, do/don't microcopy
- `skills/product-accents/SKILL.md` — dot-as-signature system, accent map

Dormant skill:
- `skills/tech-nerd-aesthetic/SKILL.md` — marked dormant; promotion trigger: marketing/landing work

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-tokens-and-theme/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-typography/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-components/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-motion/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/kgiq-parent-brand/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/iq-voice-and-tone/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/product-accents/SKILL.md`, `/Users/kmgdev/KGiQ-LLC/iq-design-locker/skills/tech-nerd-aesthetic/SKILL.md`

### Task 14: Write MANIFEST.md

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 13
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/MANIFEST.md

Create the locker's routing index. Active/dormant split with 7 active skills (Task 13) and 1 dormant (tech-nerd-aesthetic) with promotion trigger documented. Trust boundaries: first-party, no upstream. Pointer to `LINEAGE.md` for provenance. Pointer to `docs/` for authoritative prose. Curation cadence note.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/MANIFEST.md`

### Task 15: Write README.md with consumer-project vendoring instructions

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 13
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/README.md

Human-facing doc. Explain: what the locker is, the three-layer token model (core → accents → parent-layer), how to vendor the locker into a consumer project, curation cadence, how to propose a change. Include a "quickstart for a new IQ-branded project" snippet.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/README.md`

### Task 16: Write scripts/vendor-into.sh

- **Wave:** 4
- **Agent:** coder
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 13
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/scripts/vendor-into.sh

Shell helper for consumer projects. Takes a target project path; copies `core/` + chosen accent file + (optionally) `parent-layer/` + applicable `skills/` into the target project's vendored location. Records the locker commit hash in a `VENDOR-PIN` file in the target. Idempotent, safe to rerun.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/scripts/vendor-into.sh`

## Wire and discoverability test

### Task 17: Add Lockers master-index entry to KGiQ-LLC/CLAUDE.md

- **Wave:** 5
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** Task 14
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/CLAUDE.md

Add a "Lockers" section to `KGiQ-LLC/CLAUDE.md` (create the file if it doesn't exist yet) with a pointer to `iq-design-locker/MANIFEST.md`. Follow the master-index convention from `iq-locker/docs/lockers-index.md`. Entry should front-load trigger keywords (styling, brand, colors, typography, components, voice).

**Files:** `/Users/kmgdev/KGiQ-LLC/CLAUDE.md`

### Task 18: Create optional nested CLAUDE.md in iq-design-locker

- **Wave:** 5
- **Agent:** coder
- **Complexity:** low
- **Model:** haiku
- **Depends on:** Task 14
- **Parallel:** yes
- **Quality gate:** tests + spec-review + code-review
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/CLAUDE.md

Create `KGiQ-LLC/iq-design-locker/CLAUDE.md` as an optional nested entry-point that auto-loads when agents read files in the locker subtree. Short file — mostly a pointer back to MANIFEST and a reminder of the invocation protocol. The master index in `KGiQ-LLC/CLAUDE.md` remains the primary discovery mechanism.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/CLAUDE.md`

### Task 19: Run empirical spawn discoverability test

- **Wave:** 6
- **Agent:** tester
- **Complexity:** medium
- **Model:** sonnet
- **Depends on:** Task 14, Task 17
- **Parallel:** yes
- **Quality gate:** tests
- **Files:** /Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md, /Users/kmgdev/dev_projects/iq-locker/docs/lockers-index.md

Spawn a general-purpose subagent in KGiQ-LLC with a trivial design task: *"I'm adding a new secondary button variant for the KGiQ parent site. Suggest colors, hover states, and padding consistent with the IQ design system. Explain what you consulted to decide."*

> **As-built note (2026-04-12):** `docs/iq-brand-system.md` now exists in the locker as the single source of truth for implementation (added 2026-04-12, not in the original plan). The success criteria below were written before this doc existed. The test bar is now stronger: a well-routed subagent should find `iq-brand-system.md` via MANIFEST or SKILL routing, not only SKILL.md files. Add a success criterion accepting `docs/iq-brand-system.md` as discoverable evidence alongside SKILL.md.

Success criteria:
- Subagent reads `KGiQ-LLC/CLAUDE.md` and finds the Lockers section
- Subagent reads `iq-design-locker/MANIFEST.md`
- Subagent reads one or more relevant SKILL.md files **and/or `docs/iq-brand-system.md`** (per as-built note above)
- Answer references `core/iq-tokens.css`, the skills, or `iq-brand-system.md` sections — not fabricated values
- Color choice is KGiQ parent gold or steel blue, NOT iQ Publish indigo, iQ Booking teal, or iQ Circle rose (stray product accents would indicate the locker didn't surface correctly)

If the test fails, iterate on the `CLAUDE.md` master-index entry copy (more descriptive trigger words) and re-run. Record iterations in `LINEAGE.md`. Update `iq-locker/docs/lockers-index.md` to flip status from `scoping` to `validated` once the test passes.

**Files:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md`, `/Users/kmgdev/dev_projects/iq-locker/docs/lockers-index.md`

---

## Success criteria

> **As-built note (2026-04-12):** The list below reflects the original 18-of-19 build. Items marked ✅ are complete as of 2026-04-12; items marked ⏳ are the Wave 5+6 remainder. Several items updated inline to reflect post-build reality (4 accents, `iq-brand-system.md`, logos reorg) — see the As-Built Amendment at the top of this file for full context.

- [x] `KGiQ-LLC/iq-design-locker/` directory structure exists ✅
- [x] `core/iq-tokens.css` has no misleading comments, uses gold as the neutral default (base = booking-module per as-built delta #1) ✅
- [x] ~~Three~~ **Four** `accents/iq-accent-*.css` files exist with correct values (kgiq/publish/booking **+ circle** per as-built delta #2) ✅
- [x] `parent-layer/iq-site-tokens.css` is clean (no imports, no product accent) ✅
- [x] ~~`docs/` has authoring spec, operational brief, components guide, panel history~~ **`docs/iq-brand-system.md` exists as single source of truth for implementation; spec/brief/components-guide archived to `docs/history/`; panel history retained as binding decision record** (per as-built delta #3) ✅
- [x] `logos/{kgiq,products,icons}/` directory structure with kebab-case naming; all 4 products have wordmark + mark + favicon parity (per as-built delta #4) ✅
- [x] `LINEAGE.md` documents full provenance, drift reconciliation, consumer delta, completeness check, spec verification, and (§7) the 2026-04-12 reorganization record ✅
- [x] 7 active + 1 dormant SKILL.md files with valid Claude Code native frontmatter; all 7 active SKILLs routed to `iq-brand-system.md` sections ✅
- [x] `MANIFEST.md`, `README.md`, `scripts/vendor-into.sh` all exist and are correct (vendor script accepts `--accent circle`; MANIFEST §6 routes readers to LINEAGE sections) ✅
- [ ] `KGiQ-LLC/CLAUDE.md` has a Lockers section entry ⏳ **(Wave 5, Task 17)**
- [ ] `KGiQ-LLC/iq-design-locker/CLAUDE.md` nested stub exists ⏳ **(Wave 5, Task 18)**
- [ ] One successful empirical spawn discoverability test (per updated Task 19 criteria including `iq-brand-system.md`) ⏳ **(Wave 6, Task 19)**
- [ ] `iq-locker/docs/lockers-index.md` status flipped to `validated` ⏳ **(post Wave 6)**

## Time estimate

~3–3.5 hours across all 19 tasks. Extraction (Tasks 1–7) is the most time-consuming at ~1 hour. Audit (Tasks 8–12) ~45 min. Skills wrap (Tasks 13–16) ~1 hour. Wire + test (Tasks 17–19) ~30 min.

## Phase E (out of scope — listed for completeness)

Reconcile consumer projects (booking-module, iq-publish, portfolio-front-end) to vendor from the locker instead of using their stray copies. Each consumer = separate session. Not part of this plan's execution.
