# IQ Design System — Brand Audit

**Date:** 2026-04-10
**Session:** 2026-04-10-4d94
**Purpose:** Consolidate the canonical IQ design system knowledge scattered across five existing projects into a single reference, so we can design the first-instance iq-locker instance on empirical ground truth rather than guesses.
**Method:** Five parallel Explore subagents surveyed `/Users/kmgdev/KGiQ-LLC`, `portfolio-front-end`, `booking-module`, `iq-publish`, and `upwork`. Each reported design tokens, components, brand docs, voice signals, code patterns, and the canonical-vs-project-specific split. Supplemented by a Claude Flow memory search (empty — fresh project namespace).

## Headline finding

**The IQ design system already has a canonical source, and it is mature and well-documented.** The work for the iq-locker first-instance locker is not *authoring* a design system — it's *curating, wrapping, and teaching agents where the canonical content lives*.

> ⚠️ **REVISION (post-verification, 2026-04-10, CONFIRMED):** The canonical-source framing in this audit went through two corrections. Final position:
>
> 1. **There is no single canonical source of the IQ design system right now.** It exists as three drifted copies of `iq-tokens.css` / `iq-typography.css` / `iq-motion-tokens.css` scattered across booking-module, iq-publish, and portfolio-front-end, each with a different product accent baked in and some embedded comments that don't match their current host project.
> 2. **booking-module holds the original authoring spec** at `docs/superpowers/specs/2026-03-24-iq-design-system-spec.md`. This is where the iQ design system was first formalized, during the iQ Booking rebrand on 2026-03-24.
> 3. **portfolio-front-end is the canonical parent *runtime expression*** — its `src/styles/iq-site-tokens.css` is the authoritative "parent brand layer" (dark-first surfaces, steel-blue tint base, gold product accent default). But the vendored core files it imports are actually iq-publish's copies with an iQ Publish accent hardcoded in them — the override layer corrects for this at runtime.
> 4. **`KGiQ-LLC/branding/` contains stale token exports** (`design-tokens.css` is Feb 4 and pre-migration) but the Mar 30 updated `guidelines.md` v2.0 carries the parent brand layer prose (logo system, voice, tagline).
> 5. **The 2026-03-29 5-expert brand cohesion panel** identified a gap between parent and products and recommended alignment. The alignment work has **partially happened** (portfolio-front-end migrated on Mar 30; guidelines.md updated Mar 30) but the distributed-copies problem was never solved, and new drift risk persists.
>
> **The resolution: the locker becomes the single canonical source of truth.** It lives in `KGiQ-LLC/iq-design-locker/` (decided). It is not vendoring from an upstream — it IS the upstream. Consumer projects (iq-publish, booking-module, portfolio-front-end, future projects) will vendor FROM the locker going forward. The locker's first job includes fixing the drift: splitting product accents into separate files, removing misleading comments, and providing a neutral core.
>
> The empirical data below (tokens / components / voice / project signals / panel decisions) is all still accurate. Only the "where is canonical" framing has been corrected. The panel review content below is useful history but should be read as diagnosis-of-the-problem, not current state.

The parent source `/Users/kmgdev/KGiQ-LLC/branding/` contains:

- `guidelines.md` (v2.0, March 2026) — full brand identity guide: logo suite, colors, typography, themes, effects, component patterns, voice/tone, tech-nerd aesthetic, implementation checklist
- `design-tokens.css` (v1.0, Feb 2026) — portable token export with 130+ design tokens and 50+ component classes, ready to copy into new projects
- `components-guide.md` (v1.0, Feb 2026) — structural component specs (spacing, radius, buttons, forms, cards, badges, alerts, progress)
- `USAGE-GUIDE.md` — copy-paste setup checklist for new and existing projects, including a Tailwind config snippet and Claude Code prompt templates
- `component-consistency-matrix.md` — drift tracker across deployed projects
- `style-guide.html` — interactive visual reference
- `guidelines-v1.1-proposed.md` — tracked evolution doc

**This changes the shape of the first-instance locker work substantially.** Instead of building a design system from nothing, we're doing a fabric-toolkit-style vendor-and-curate operation against a source we already control. That's a known pattern (same shape as the T3 Hearst reference implementation), and it de-risks Phase 1 significantly.

## The canonical brand system (consolidated)

Cross-referencing all five projects, the canonical IQ design system consists of the following stable, reusable primitives. These appear in at least two projects and are endorsed by the authoritative `KGiQ-LLC/branding/` docs.

### Brand palette (parent KGiQ)

| Token | Hex | Role |
|-------|-----|------|
| Steel Blue | `#5E7F9B` | Primary brand color (KG element); secondary buttons; focus states |
| Sage Green | `#8DA377` | Secondary / legacy; supporting role |
| Gold | `#FFD166` | Primary CTA; dot-motif signature; parent brand accent |
| Neutral Gray | `#B6BBB6` | Separators; muted text; quiet UI |

### Product accent system ("dot-color atom")

Each iQ product gets its own accent color that overrides `--iq-tint-base` for its instance while keeping the parent KGiQ colors intact:

| Product | Accent | Observed in |
|---------|--------|-------------|
| KGiQ (parent) | Gold `#FFD166` | KGiQ-LLC, portfolio-front-end |
| iQ Booking | Teal `#0D9488` | booking-module |
| iQ Publish | Indigo `#6366F1` (light) / `#818CF8` (dark) | iq-publish |
| iQ Upwork Tools | *(unclear — not extracted; may not use the pattern)* | upwork |

This is a canonical brand extension mechanism and should be treated as a first-class concept in the locker.

### Three-layer token model (observed in portfolio-front-end, iq-publish, booking-module)

```
┌──────────────────────────────────────────────────────────┐
│  Project-specific tokens (design-tokens.css)             │
│  Site/app particulars, content tokens, overrides         │
├──────────────────────────────────────────────────────────┤
│  Site/product overrides (iq-site-tokens.css)             │
│  Theme mode (dark-first), product accent, surface model  │
├──────────────────────────────────────────────────────────┤
│  iQ core tokens (vendored, iq-tokens.css +               │
│  iq-typography.css + iq-motion-tokens.css)               │
│  Colors, type scale, motion, spacing, z-index, a11y      │
└──────────────────────────────────────────────────────────┘
```

**Tint cascade:** `--iq-accent` (fixed per product) → `--iq-tint-base` (tenant-overridable) → `--iq-tint` (interactive elements) → `--iq-tint-hover` (oklch color-mix 85% black/white).

### Surfaces (4-level hierarchy)

`canvas` → `primary` → `secondary` → `overlay` — with dark-first defaults (`#0f172a` canvas → `#1e293b` primary → `#334155` secondary → `#475569` hover).

### Typography system

| Face | Font | Usage |
|------|------|-------|
| Sans / body | Inter (400, 500) | Default body text |
| Display / heading | Plus Jakarta Sans (600 only) | Headings — semibold-only is canonical |
| Mono / accent | JetBrains Mono | Tech-nerd accents, code, terminal, data |

**Type scale** (8 levels, observed in portfolio-front-end and iq-publish): Display (28/34/600) · Title 1 (22/28/600) · Title 2 (17/22/600) · Title 3 (15/-/600) · Body (15/22/400) · Callout (13/18/400) · Caption 1 (12/16/400) · Caption 2 (11/14/500) · Data (14, mono).

Utility classes: `.iq-display`, `.iq-title-1`, `.iq-body`, `.iq-body-emphasis`, `.iq-callout`, `.iq-caption-2`, `.iq-data`.

### Spacing (8-point grid)

`4 · 8 · 12 · 16 · 20 · 24 · 32 · 40 · 48 · 64` — named tokens `--iq-space-1` through `--iq-space-16`.

### Border radius scale

`sm` 4–6px · `md` 8px · `lg` 10–12px · `xl` 12–16px · `2xl` 24px · `full` 9999px.

### Motion

Durations (canonical): `press 60ms` · `micro 120ms` · `fast 200ms` · `standard 300ms` · `measured 400ms` · `slow 550ms`.
Easing: `default cubic-bezier(0.22, 1, 0.36, 1)` plus `enter` / `exit` / `bounce` / `spring` variants.
Respects `prefers-reduced-motion: reduce` (collapses all to 0.01ms).

### Components (canonical base set)

Observed across at least two projects with the same or nearly-same shape:
- `Button` — variants `primary` (gold), `secondary` (steel blue), `tertiary`, `ghost`, `danger`; sizes `sm` / `md` / `lg`
- `Badge` — variants `info` / `success` / `warning` / `error` / `neutral` with 20% alpha backgrounds
- `Card` / `SurfaceCard` — semantic surfaces, elevation via shadow + border
- `GlassCard` — glassmorphism pattern (backdrop blur, subtle border, overlay shadow)
- `Input` / `TextField` with `.label` / `.label-required` / focus ring
- `Alert` with semantic variants
- `SectionHeader` with optional mono mode and gold terminal prompt (`>`) accent
- Form primitives: checkbox, radio, toggle, segmented control
- Navigation: `nav-item` with active state using steel blue background

### Focus state convention

**3px ring, steel blue at 20% opacity** (`rgba(94, 127, 155, 0.2)`) — never default browser outline. This is consistent across all projects with IQ branding.

### Voice and tone

Two framings appear across the projects, with mild drift:

- **KGiQ-LLC `guidelines.md` v2.0:** "Expert · Direct · Human · Product-Confident" with explicit writing rules (be concise, be specific, be active, be human, no qualifiers, no puffery).
- **iq-publish `iq-design-system-brief.md`:** "Capable · Clear · Human" with do/don't pairs ("Finding available services…" not "Loading…").

These are compatible but not identical. The KGiQ-LLC version is more developed (explicit rules and anti-patterns); the iQ Publish version is more action-oriented (do/don't microcopy examples). **Worth harmonizing into a single canonical voice skill.**

**Tagline:** "Clarity, Action, Results" — appears in KGiQ-LLC and upwork projects.

### Tech-nerd aesthetic (load-bearing)

A recurring personality signature across all IQ-branded projects:
- Terminal prompts (`>`, `$`) as visual accents, especially in hero sections and section headers
- Monospace (JetBrains Mono) accents in strategic places — data, code, accent callouts, section numbering
- Dark-mode-first as the default, with light mode as a secondary variant
- IDE / command-line references in copy and visuals
- "Gold dot" first-class brand signature element (not decoration)

This is not optional flavor — it's a canonical brand element and should be explicit in the locker.

## Canonical source of truth — verified (2026-04-10)

Empirical verification of the distributed-copies problem. This is the load-bearing finding for the locker build.

### The actual history

| Date | Event | Artifact |
|------|-------|----------|
| 2026-03-24 | iQ design system first authored during iQ Booking rebrand in booking-module | `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md` (THE original authoring source) |
| 2026-03-24 | First `iq-tokens.css` / `iq-typography.css` / `iq-motion-tokens.css` created in booking-module | `booking-module/packages/shared-ui/src/styles/` with teal (`#0D9488`) accent |
| 2026-03-28 | iq-publish creates its own copy, rewrites `--iq-accent` to indigo (`#6366F1`), adds misleading "iQ Publish product override" comment | `iq-publish/vendored/` |
| 2026-03-29 | 5-expert brand cohesion panel diagnoses parent/product gap, recommends alignment | `KGiQ-LLC/docs/plans/2026-03-29-brand-cohesion-expert-panel.md` |
| 2026-03-30 | portfolio-front-end copies iq-publish's version into `src/styles/vendored/`, adds `iq-site-tokens.css` as the parent override layer. `KGiQ-LLC/branding/guidelines.md` updated to reflect panel decisions | `portfolio-front-end/src/styles/vendored/` + `iq-site-tokens.css` |
| 2026-04-10 | **Current state:** three drifted copies of the core files, no single source of truth, `KGiQ-LLC/branding/design-tokens.css` stale at Feb 4 | — |

### The three drifted copies

| Project | Path | `iq-tokens.css` bytes | `--iq-accent` value | Notes |
|---------|------|----------------------|---------------------|-------|
| booking-module | `packages/shared-ui/src/styles/` | 5026 | `#0D9488` (teal) | Oldest copy. Lives alongside other shared-ui assets, not in a `vendored/` subdir. |
| iq-publish | `vendored/` | 5017 | `#6366F1` (indigo) | Has misleading "iQ Publish product override" comment that made it into downstream copies. |
| portfolio-front-end | `src/styles/vendored/` | 5017 | `#6366F1` (indigo) | Byte-identical to iq-publish's version. **The comment is misleading in this location** because portfolio-front-end is the parent site, not iQ Publish. `iq-site-tokens.css` overrides `--iq-tint-base` to steel blue (`#5E7F9B`) and sets `--iq-product-accent` to gold (`#FFD166`) so the site renders correctly despite the stray core file. |

### The canonical parent runtime expression

`portfolio-front-end/src/styles/iq-site-tokens.css` is 40 lines and clean. This IS the canonical parent brand layer — it imports the three vendored core files and layers on KGiQ-specific surfaces, text colors, tint base, shadows, borders, and the default product accent (gold for parent). The locker's `parent-layer/iq-site-tokens.css` should be a copy of this file (cleaned up if any drift is found).

### What the locker does about it

1. **Becomes the single canonical home** for the iQ design system, living in `KGiQ-LLC/iq-design-locker/`.
2. **Ships a neutral `core/iq-tokens.css`** that does not bake in any product accent. The "iQ Publish product override" comment is gone. A clear header explains the three-layer model.
3. **Splits product accents into separate files** (`accents/iq-accent-kgiq.css`, `accents/iq-accent-publish.css`, `accents/iq-accent-booking.css`) so each consumer project imports the accent it actually wants, rather than editing a copy of the core file.
4. **Owns the parent brand layer** as a clean, canonical artifact (`parent-layer/iq-site-tokens.css` extracted from portfolio-front-end, plus parent-brand prose from `KGiQ-LLC/branding/guidelines.md`).
5. **Records the sourcing decisions and drift history** in a `LINEAGE.md` (or equivalent) so future readers know where each file came from and why.

### Backwards-looking audit (required build step)

After the first extraction, run a backwards-looking provenance audit to verify:

1. **Per-file lineage.** For each file in the locker, record its source project and source path. Produce a manifest documenting "this file was extracted from X on 2026-04-10."
2. **Byte-level comparison with sources.** Diff the locker version against each source copy. For files where multiple sources exist (like `iq-typography.css` which should be byte-identical across booking-module / iq-publish / portfolio-front-end), confirm the claim and flag any surprises.
3. **Drift reconciliation log.** For files where drift is found, document the drift explicitly and record which version the locker chose as canonical. Example: "`iq-tokens.css` — chose iq-publish's version (Mar 28) as base, stripped `--iq-accent` block and the misleading comment, replaced with neutral default + header."
4. **Consumer-project delta report.** Optional but recommended: generate a report showing which files in each consumer project would need updating to align with the locker's canonical version. This tells us how much "backwash" work is needed to get booking-module / iq-publish / portfolio-front-end pointing at the locker instead of their stray copies.
5. **Completeness check.** Make sure we didn't miss any files. Grep across all surveyed projects for `iq-tokens`, `iq-typography`, `iq-motion`, `iq-site-tokens`, `iq-design-system` — compare against what's in the locker. Any hits we didn't account for become follow-ups.
6. **Spec verification.** Read the booking-module spec file and confirm it's consistent with the token values we extracted. If the spec and the tokens disagree somewhere, that's a drift flag worth noting.

This audit should live as a doc at `KGiQ-LLC/iq-design-locker/LINEAGE.md` (or similar). It's both a quality gate for the first build and the reference for future updates.

---

## Neon addendum: the 5-expert brand cohesion panel (2026-03-29)

Querying Open Mind's Neon database surfaced a document I missed in the filesystem survey: `/Users/kmgdev/KGiQ-LLC/docs/plans/2026-03-29-brand-cohesion-expert-panel.md`. It's a 5-expert panel review (Web Design, Brand Identity, UI/UX, Apple PM, "Steve Jobs channel") conducted 12 days ago. **It is the most current canonical thinking on the IQ design system, more current than any of the guideline docs on disk.** It also materially reframes what "canonical" means.

### Canonical source hierarchy (corrected)

The panel rated the three properties:

| Property | Location | Maturity | Role |
|----------|----------|----------|------|
| **iQ Publish** | `iq-publish/` with vendored `iq-*.css` | **9/10 — production-grade** | Canonical design system authority |
| **iQ Booking** | `booking-module/` with same vendored `iq-*.css` | **9/10 — production-grade** | Canonical design system authority (duplicate source) |
| **kgiq.dev / KGiQ-LLC parent** | `portfolio-front-end/` + `KGiQ-LLC/branding/` | **6/10 — hobbyist-polished** | Parent brand layer, being aligned UP to the products |

**Implication for iq-locker:** vendor from the iQ product system as the primary source of tokens, typography, motion, and surfaces. Vendor from `KGiQ-LLC/branding/` for parent-brand-layer content (logos, parent color palette, voice pillars, tagline, tech-nerd aesthetic). These are two sources at two different layers, not one-or-the-other.

### Unanimous panel findings (5/5 agree)

Five findings on which all five experts aligned. These are *decisions already made* and should be treated as canonical in the locker:

1. **The iQ product design system is the stronger standard.** Three-layer token architecture, light/dark theme, `prefers-reduced-motion` + `prefers-contrast: more` + `forced-colors` accessibility support, Apple HIG-inspired typography roles (Display 28 / Title 1 22 / Body 15 / Caption 11), 5-curve motion system with spring/bounce and stagger tokens, print stylesheets, explicit design system brief as operational contract. The parent site has none of these; it has dark-mode-only, no motion system, one card doing all the work (GlassCard), Inter-for-everything typography.

2. **Plus Jakarta Sans for headings is the single highest-impact harmonization.** Priority #1. Already used by both products; already present in the proposed KGiQ logo for the "iQ" letterforms. Parent site currently uses Inter for everything — Inter is a UI body font, not a display font. ~2 hours of work.

3. **White "iQ" in the proposed logo is correct.** Sage Green (`#8DA377`) is being **reclassified from "primary brand green" to "legacy/secondary palette."** This is a material palette change. White positions the parent as the neutral authority — "I am the platform, not a product." Gold dot becomes the sole parent accent.

4. **A light background logo variant is blocking for GTM.** Spec exists in the panel doc. Rename convention: `KGiQ-dk-bg` / `KGiQ-lt-bg` to match product convention.

5. **Dark canvas tones clash.** Parent `#0f172a` (Slate 900 — blue-tinted, warm-dark) vs products `#000000` / `#1C1C1E` (Apple neutral cold-dark). Visible temperature difference during navigation transitions. Fix: neutralize parent to products.

### Dot-as-signature system — validated

| Brand | Dot Color | Hex | Semantic |
|-------|-----------|-----|----------|
| **KGiQ** (parent) | Gold | `#FFD166` | Warm, premium, authority |
| **iQ Publish** | Indigo | `#6366F2` | Cool, creative, social |
| **iQ Booking** | Teal | `#0D9488` | Calm, professional, trust |

The three colors occupy different color wheel quadrants. Panel notes the system scales to 6–8 products before running out of perceptually distinct hue slots. The `--iq-accent` token is the architectural hook.

*(Note: my initial audit recorded the Publish dot as `#6366F1`. The panel doc has `#6366F2`. Probably a typo somewhere; worth resolving in the locker.)*

### Token-level drift (parent vs products)

The panel published a token-level comparison table. Excerpting the load-bearing rows:

| Attribute | kgiq.dev parent | iQ Products |
|-----------|-----------------|-------------|
| Heading font | Inter | **Plus Jakarta Sans** |
| Body base size | 16px | **15px** |
| Dark canvas | `#0f172a` (Slate blue) | **`#000000`** (true black) |
| Dark primary surface | `#1e293b` | **`#1C1C1E`** (Apple dark) |
| Text primary dark | `#f8fafc` | **`#F5F5F7`** (Apple gray) |
| CTA color | Gold | **Product accent** via `--iq-tint` |
| Grid | 8pt (rem) | **4pt (px)** |
| Motion durations | 3 (150/200/300ms) | **5 (60/120/200/350/550ms) + press (100ms)** |
| Motion easings | `ease` only | **5 named curves (spring, bounce, enter, exit, default)** |
| Elevation model | Glassmorphism (blur + semi-transparent) | **Apple Settings** (4-level surface hierarchy + shadow) |
| Glassmorphism policy | Everywhere | **"NO glassmorphism on customer-facing surfaces"** (explicit product brief rule) |

**Bold values are the canonical side per the panel.** The parent aligns to these.

### Panel's action plan (consensus)

Five **blocking** items for GTM:

1. Plus Jakarta Sans on kgiq.dev for headings (~2 hrs)
2. Create light-background logo variant (~1 hr)
3. Build iQ Publish landing page on kgiq.dev (~1 session)
4. Fix /booking landing page CTAs — gold → teal (~30 min)
5. Remove internal-only products from /products (~15 min)

Six "should-do soon after launch" items including neutralize dark backgrounds, drop glassmorphism from nav/footer, add light mode to kgiq.dev, update brand guidelines for three-font stack + dot system, rename logo files, add "by KGiQ" mark to product sidebars.

Four "can wait" items including motion token adoption on parent and spacing grid alignment.

### What the panel decisions change about the locker

- **Vendor from two sources, not one.** Primary content source: iQ product system (the vendored `iq-tokens.css`, `iq-typography.css`, `iq-motion-tokens.css` files in iq-publish/booking-module). Secondary: `KGiQ-LLC/branding/` for logo system, parent palette, voice pillars, tagline, tech-nerd aesthetic.
- **Version pinning matters more than I thought.** The iQ product system is actively evolving — the panel's recommendations will drive updates. The locker needs to pin to a specific revision and explicitly trigger re-sync when the canonical source updates, rather than chasing live content.
- **Sage Green reclassification is a MANIFEST entry.** The locker's `tokens-and-theme` skill should note that `#8DA377` is legacy/secondary and no longer primary. This is exactly the kind of curation the locker is meant to carry.
- **Glassmorphism becomes a dormant-with-trigger item.** Panel explicitly says *"NO glassmorphism on customer-facing surfaces."* It's only appropriate for internal admin UI. The locker's components skill should mark `GlassCard` patterns dormant-for-customer-facing-work.
- **"Harmonize voice pillars" is resolved.** Not empirically resolved in the panel doc, but the "Capable / Clear / Human" framing from iq-publish is the product-layer voice; the "Expert / Direct / Human / Product-Confident" from KGiQ-LLC is the parent-layer voice. They're two layers, not drift. The locker can document both with their respective scopes.
- **Logo file naming convention:** `KGiQ-dk-bg` / `KGiQ-lt-bg`. Panel recommendation.
- **The iQ design system brief functions as an "operational contract."** There's a doc at `iq-publish/docs/context/iq-design-system-brief.md` that the panel explicitly called out as a model of how a design system should operate. Worth reading before writing the locker's own README/MANIFEST.

### Other relevant files found in Neon (not yet surveyed)

- `/Users/kmgdev/KGiQ-LLC/docs/plans/2026-03-29-logo-review-panels.md` — logo review results, 5/5 light variant approval, tagline warmed to `#C49A1A`, KG typeface 3–2 split Bold vs Semibold
- `/Users/kmgdev/KGiQ-LLC/docs/plans/2026-03-30-site-redesign-implementation.md` — 6-phase site redesign implementation plan (Foundation → Homepage → Product Pages → iQ Assistant Rebrand → Blog → Polish/Launch)
- `/Users/kmgdev/KGiQ-LLC/branding/logos/iQ Brand-Logos/KGiQ-proof-sheet.html` — 12 logo SVG variants using Plus Jakarta Sans Semibold
- `/Users/kmgdev/dev_projects/booking-module/docs/sessions/2026-03-24-iq-design-system-complete-migration.md` — booking-module's migration TO the iQ design system (this was when iQ Booking adopted the canonical tokens)
- `/Users/kmgdev/dev_projects/Remotion/docs/sessions/2026-03-31-brand-promo-animation.md` — brand promo animation work in a Remotion project (not surveyed)

These are all pre-existing design system work. The locker can reference them, but doesn't need to vendor them.

---

## Project-by-project signal summary

| Project | IQ-branded? | Signal strength | Canonical role |
|---------|-------------|-----------------|----------------|
| `KGiQ-LLC` | **Yes — authoritative** | Highest | **The canonical source.** Owns `branding/` with complete docs, tokens, usage guide, drift matrix. Everything else derives from here. |
| `portfolio-front-end` | Yes — fully | High | High-fidelity production reference implementation using vendored iq-*.css. Best example of the three-layer token model in a Next.js 16 + Tailwind 4 stack. |
| `booking-module` | Yes — fully | High | Reference for cross-runtime IQ branding (React admin + Lit Web Component booking widget). Uses teal product accent. CLAUDE.md explicitly states *"The iQ Design System governs all visual output."* |
| `iq-publish` | Yes — fully | High | Reference for indigo product accent. Has its own `docs/context/iq-design-system-brief.md` with "Capable, Clear, Human" voice framing and do/don't microcopy examples. |
| `upwork` | Yes — fully | Medium | KGiQ Upwork Proposal Generator. Uses its own `branding/guidelines.md` v2.0 (parallel to KGiQ-LLC). **Interesting: this project has brand docs that look very similar to KGiQ-LLC — likely forked or shared.** Uses React 19 + Vite + Tailwind 3.4. |

## Observed drift (worth noting for the locker)

Four categories of drift across the projects:

1. **Styling stack drift.** KGiQ-LLC uses pure CSS (no Tailwind), portfolio-front-end uses Tailwind 4 with `@theme`, upwork uses Tailwind 3.4 + CSS vars, booking-module uses CSS vars + Lit templates + Tailwind in admin, iq-publish uses Tailwind 4 + inline token `var()` references. **The tokens themselves are consistent; the consumption idiom varies.** The locker should teach tokens as the canonical layer and *document* the accepted consumption patterns rather than prescribing one.

2. **Voice framing drift.** KGiQ-LLC ("Expert / Direct / Human / Product-Confident") vs iq-publish ("Capable / Clear / Human"). Not a contradiction — they're compatible — but worth harmonizing.

3. **Brand docs duplication.** KGiQ-LLC has `branding/guidelines.md` v2.0. `upwork` has `branding/guidelines.md` that looks very similar (both 520-line-ish, both v2.0-ish). These may be forks, copies, or independent instantiations. **This is exactly the drift problem the locker is meant to solve.**

4. **Product accent color documentation.** Booking's teal and Publish's indigo are clearly canonical, but there's no single place where the product accent map lives. It's spread across each product's own token files. The locker should own this map as a first-class concept.

## What the drift tells us

The existence of drift is itself the argument for the locker. Before iq-locker, each project "knows" the IQ design system through whatever state of KGiQ-LLC/branding/ existed when it was last copied. After iq-locker, each project consumes the canonical source through a curated, versioned, promotion-tracked MANIFEST — and drift becomes detectable and correctable.

## Proposed locker structure (for discussion)

Based on the audit, here's a proposal for how to structure the IQ design system locker. This is not yet a decision — it's input for the design conversation.

### Shape (final — locker IS canonical)

The locker lives at `/Users/kmgdev/KGiQ-LLC/iq-design-locker/`. It is **not vendoring from an upstream — it is the upstream**, so the `upstream/` directory naming from the fabric-toolkit pattern doesn't apply. Structure:

```
KGiQ-LLC/iq-design-locker/
├── MANIFEST.md                    ← curation index: active/dormant, promotion
│                                    triggers, trust boundaries
├── README.md                      ← human setup + consumer-project vendoring
│                                    instructions + curation cadence
├── LINEAGE.md                     ← backwards-looking provenance audit; per-file
│                                    source mapping; drift reconciliation log
├── CLAUDE.md                      ← locker-local entry-point (optional per Q10;
│                                    top-level master index is primary)
├── core/                          ← canonical CSS — neutral, no product accent
│   │                                baked in, no stray comments
│   ├── iq-tokens.css              ← cleaned version of the core tokens file
│   ├── iq-typography.css          ← three-font stack + named type roles
│   └── iq-motion-tokens.css       ← 5-duration + 5-curve + reduced-motion
│
├── accents/                       ← per-product accent files (NEW — fixes drift)
│   ├── iq-accent-kgiq.css         ← gold (#FFD166) — parent
│   ├── iq-accent-publish.css      ← indigo (#6366F1) — iQ Publish
│   └── iq-accent-booking.css      ← teal (#0D9488) — iQ Booking
│
├── parent-layer/                  ← canonical KGiQ parent brand layer
│   ├── iq-site-tokens.css         ← extracted + cleaned from
│   │                                portfolio-front-end/src/styles/iq-site-tokens.css
│   ├── kgiq-brand-guidelines.md   ← from KGiQ-LLC/branding/guidelines.md (Mar 30)
│   └── logos/                     ← KGiQ-dk-bg.svg, KGiQ-lt-bg.svg, proof-sheet
│
├── docs/                          ← prose documentation (not skills)
│   ├── iq-design-system-spec.md   ← from booking-module — THE authoring source
│   ├── iq-design-system-brief.md  ← from iq-publish — operational contract
│   ├── iq-components-guide.md     ← from iq-publish
│   └── history/
│       └── 2026-03-29-brand-cohesion-expert-panel.md  ← preserved for context
│
├── skills/                        ← Claude Code native SKILL.md wrappers
│   ├── iq-tokens-and-theme/       ← tokens + three-layer tint + surfaces
│   │   └── SKILL.md
│   ├── iq-typography/             ← three-font stack + type scale + utilities
│   │   └── SKILL.md
│   ├── iq-components/             ← canonical component set; glassmorphism
│   │   └── SKILL.md                 dormant-for-customer-facing
│   ├── iq-motion/                 ← motion tokens + easing + reduced-motion
│   │   └── SKILL.md
│   ├── kgiq-parent-brand/         ← parent palette (sage green legacy), logo
│   │   └── SKILL.md                 system, tagline, voice pillars
│   ├── iq-voice-and-tone/         ← product-layer voice "Capable/Clear/Human"
│   │   └── SKILL.md
│   ├── tech-nerd-aesthetic/       ← DORMANT by default; promote for
│   │   └── SKILL.md                 marketing/landing work
│   └── product-accents/           ← dot-as-signature system; accent map
│       └── SKILL.md
│
└── scripts/
    ├── vendor-into.sh             ← helper that consumer projects run to copy
    │                                files from here into their own tree with
    │                                a commit-hash pin recorded locally
    └── wire.sh                    ← (for consuming projects) symlinks active
                                     skills into the project's .claude/skills/
```

### Active vs dormant (first cut, post-panel)

**Active (wired into `.claude/skills/`):**
- `iq-tokens-and-theme` — product system tokens, three-layer tint cascade, four-level surface hierarchy, dark/light
- `iq-typography` — three-font stack (PJS/Inter/JBM), type scale with named roles (Display / Title 1 / Title 2 / Body / Callout / Caption), utility classes
- `iq-components` — canonical component set (Button, Badge, Card, SurfaceCard, Input, Alert, SectionHeader) with glassmorphism explicitly marked dormant-for-customer-facing
- `iq-motion` — 5-duration system (press/micro/fast/standard/measured/slow) + 5 named curves + stagger + reduced-motion
- `kgiq-parent-brand` — parent palette (Steel Blue / Gold / Neutral Gray — **sage green reclassified as legacy**), logo system with dk-bg/lt-bg convention, tagline "Clarity, Action, Results"
- `iq-voice-and-tone` — product-layer voice "Capable, Clear, Human" with do/don't microcopy examples
- `product-accents` — dot-as-signature system: KGiQ Gold, iQ Publish Indigo, iQ Booking Teal, with `--iq-accent` architectural hook

**Dormant (tracked but not wired):**
- `tech-nerd-aesthetic` — promote when marketing/landing work is in scope (terminal prompts, monospace accents, "gold dot signature")
- `glassmorphism-patterns` — **promote only for internal admin UI**; explicitly forbidden on customer-facing surfaces per the panel
- `kgiq-parent-voice-pillars` — the "Expert / Direct / Human / Product-Confident" framing from KGiQ-LLC/branding/guidelines.md. Promote when writing parent-brand-layer marketing copy (distinct from product voice)

### SKILL.md wrapper approach

Each SKILL.md is thin — frontmatter + a short body that references the upstream resource files:

```markdown
---
name: iq-tokens-and-theme
description: Canonical IQ design system tokens — colors, surfaces, spacing, radius, shadows, tint cascade. Use when styling any IQ-branded UI element, picking colors, or setting up a new project's theme.
version: 1.0.0
---

# IQ Tokens and Theme

The canonical IQ design system tokens live in `../upstream/design-tokens.css`.
Read that file for the full list of CSS custom properties.

## Core rules

- Never hardcode colors. Always use `var(--iq-*)` or `var(--brand-*)` tokens.
- Use the three-layer model: core iq tokens → site overrides → project-specific.
- [...]

## Reference

- `../upstream/design-tokens.css` — full token export
- `../upstream/guidelines.md` — narrative style guide with rationale
- `../upstream/USAGE-GUIDE.md` — setup checklist for new projects
```

This keeps the rich content in its canonical prose form and uses SKILL.md as the agent-facing routing surface.

### Sync and drift discipline

- `scripts/iq-design-locker-sync.sh` pulls from `KGiQ-LLC/branding/` with a pinned commit hash stored in `MANIFEST.md`. Manual review before merge.
- `upstream/component-consistency-matrix.md` gets promoted from prose to an actionable drift tracker — compare against consumer projects and flag diffs.
- MANIFEST.md records "last curated" date + upstream commit pin per review cadence.

### CLAUDE.md entry-point snippet (project-level)

Goes in the iq-locker project's own top-level CLAUDE.md (and in any project that adopts the locker):

```markdown
## Lockers

- **IQ Design System** (`docs/iq-design-locker/MANIFEST.md`) — Frontend styling,
  brand colors, typography, component patterns, voice/tone, tech-nerd aesthetic.
  Use for any UI work, marketing copy review, or theme setup in an IQ-branded
  project.
```

## Open questions before we build

1. **Vendor or submodule or symlink?** The fabric-toolkit precedent is a vendored copy with a sync script. But KGiQ-LLC/branding/ is the developer's own source, not a third-party upstream. **A git submodule might be cleaner** — pins a commit, updates on demand, no manual sync script. Or a symlink for solo dev use (fragile for CI / teammates). The tradeoffs differ from fabric-toolkit because the upstream is first-party here.

2. **Should upwork's `branding/guidelines.md` be reconciled with KGiQ-LLC's before we start?** If they're forks, we should decide which is canonical and merge the other's changes upstream first. This is pre-locker work — the locker can't curate a source that's ambiguous.

3. **Voice pillars harmonization.** Pick one of "Expert / Direct / Human / Product-Confident" (KGiQ-LLC) and "Capable / Clear / Human" (iq-publish), or merge into a single framing. The locker's `voice-and-tone` skill should have one canonical answer.

4. **Tech-nerd aesthetic — active or dormant in the first cut?** It's load-bearing for marketing/landing work but can over-apply if loaded indiscriminately for backend work. Dormant with a clear promotion trigger feels right, but worth deciding explicitly.

5. **Motion system — where does it live?** Canonical tokens exist in vendored `iq-motion-tokens.css`. Separate skill, or rolled into the tokens skill? Separate probably scales better but splits thin content.

6. **Is the iq-locker framework the right home, or should the locker live inside KGiQ-LLC?** Arguments for KGiQ-LLC: that's where the canonical content already is; consumer projects could vendor from there directly. Arguments for iq-locker: this is the reusable framework, and the locker pattern is its first-instance proof. **Probably: the framework + convention lives in iq-locker, and the first concrete locker lives as a vendored instance in any project that needs it — including eventually KGiQ-LLC itself.**

## Impact on iq-locker brief

- **First-instance selection confirmed:** IQ design system is the right pick. It's the safest candidate (no Claude Flow overlap), the canonical source already exists, and the scope is a vendor-and-curate operation rather than greenfield authoring.
- **A3 (vendored → self-authored generalization) gets a nuanced test.** The original assumption was "pattern generalizes from vendored-upstream (Microsoft Fabric) to self-authored (IQ brand)." Turns out the IQ brand is *also* effectively vendored — from a first-party upstream (KGiQ-LLC) rather than third-party. That's a middle case. A future second-instance locker (e.g. agentic foundation skills) would be the true greenfield test.
- **Scope for Phase 2 (build first-instance):** vendor KGiQ-LLC/branding/ into `docs/iq-design-locker/upstream/`, write SKILL.md wrappers for 5–6 active skills, write MANIFEST.md, write README.md and sync/wire scripts, add master-index entry to the project's top-level CLAUDE.md.

## Next-step recommendation (revised post-Neon)

The Neon findings resolve several drift questions the original audit was going to flag. The panel has already decided:

- ✅ Voice pillars layering (product = "Capable, Clear, Human"; parent = "Expert / Direct / Human / Product-Confident"; both live in the locker with scope labels)
- ✅ KGiQ-LLC/branding/guidelines.md v2.0 vs upwork/branding/guidelines.md duplication — the upwork copy is older and not canonical; discard from consideration
- ✅ Sage green as legacy, not primary
- ✅ Plus Jakarta Sans as the heading font
- ✅ Logo naming: `KGiQ-dk-bg` / `KGiQ-lt-bg`
- ✅ Three-font stack as the standard
- ✅ Dot-as-signature system as canonical
- ✅ No glassmorphism on customer-facing surfaces

What remains to decide before building the locker:

1. **Vendor mechanism.** Git submodule (pins commit, clean update semantics) vs copy-and-sync script (fabric-toolkit pattern, more explicit diff on updates) vs symlink (fragile, solo-dev only). **Lean: copy-and-sync**, matching the fabric-toolkit precedent. Different from fabric-toolkit only in that both upstreams are first-party, which means sync is cheaper but drift is also more likely.

2. **Pinning strategy.** The iQ product system is actively evolving (the panel's action items will drive updates). Pin to a specific commit in each upstream and record that pin in MANIFEST.md with a "last reviewed" date. Re-sync deliberately, not automatically.

3. **Where the locker lives.** Three options:
   - **(a) In iq-locker project itself** as a reference implementation, then copied into consumer projects. Matches the "iq-locker is a reusable framework" philosophy.
   - **(b) In KGiQ-LLC as the canonical host**, with consumer projects symlinking or submoduling from there. Matches "canonical source of truth" philosophy.
   - **(c) Standalone repo** (`iq-design-locker` or similar) that both iq-locker and consumer projects vendor from. Clean separation but adds a repo.

   Lean: **(a) for the first instance**. Build it inside iq-locker to prove the pattern. Once it works, decide whether to promote to (b) or (c) based on how the second locker's needs inform the answer.

4. **Scope of the first build.** Minimal viable locker = active skills only, MANIFEST + README + sync script, no dormant content yet. Prove the wiring and the subagent discoverability end-to-end, then add dormant content in a second pass.

## Proposed immediate next step (build plan)

Move from audit into locker construction. The locker lives at `/Users/kmgdev/KGiQ-LLC/iq-design-locker/`. Proposed build sequence:

### Phase A — Extract + canonicalize (the locker's first job)

1. **Create the directory structure** at `KGiQ-LLC/iq-design-locker/` using the shape above.
2. **Extract the core files with drift-fix.** Take iq-publish's version of `iq-tokens.css` (the most recently updated) as the working base, then:
   - Remove the `/* === Accent (iQ Publish product override) === */` block and the hardcoded `--iq-accent: #6366F1` values
   - Replace with a clear header explaining the three-layer model and a neutral default accent (or use a CSS variable placeholder that must be overridden)
   - Save as `core/iq-tokens.css`
   - Diff against booking-module's and portfolio-front-end's copies; note any other drift to reconcile
3. **Copy `core/iq-typography.css` and `core/iq-motion-tokens.css`** from iq-publish (or whichever project has the most current version — verify with a diff across all three projects first; these should be byte-identical but confirm).
4. **Create the accent files.** Extract the three accent blocks into separate files:
   - `accents/iq-accent-kgiq.css` — gold `#FFD166` (extract from portfolio-front-end's `iq-site-tokens.css` `--iq-product-accent` block)
   - `accents/iq-accent-publish.css` — indigo `#6366F1` (from the block removed in step 2)
   - `accents/iq-accent-booking.css` — teal `#0D9488` (from booking-module's `iq-tokens.css`)
5. **Extract the parent layer.** Copy portfolio-front-end's `src/styles/iq-site-tokens.css` (minus the accent lines, which move to `accents/iq-accent-kgiq.css`) to `parent-layer/iq-site-tokens.css`. Copy relevant files from `KGiQ-LLC/branding/` into `parent-layer/` (guidelines.md, logos/, USAGE-GUIDE.md if relevant).
6. **Copy docs.** `booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md` → `docs/iq-design-system-spec.md` (THE authoring source). iq-publish's `docs/context/iq-design-system-brief.md` and `iq-components-guide.md` → `docs/`. Copy the brand cohesion panel review to `docs/history/`.

### Phase B — Backwards-looking audit (quality gate)

7. **Write `LINEAGE.md`.** For each file in the locker, record source project + source path + extraction date + any drift reconciliation notes. Include diff results from step 2 and step 3.
8. **Byte-compare against sources.** Diff each locker file against its source. Flag any surprises (e.g., "I thought iq-typography was identical across projects — it isn't, here's the diff").
9. **Consumer-project delta report.** Document what each consumer project would need to change to point at the locker canonical. This is the backlog for reconciling existing projects once the locker is live.
10. **Completeness grep.** `grep -r` for `iq-tokens`, `iq-typography`, `iq-motion`, `iq-design-system` across all surveyed projects. Anything we didn't account for goes into a follow-up list.
11. **Spec verification.** Read the authoring spec in `docs/iq-design-system-spec.md` end-to-end and compare against the tokens extracted. Any disagreements get flagged.

### Phase C — Wrap in Claude Code skills

12. **Write the 7 active SKILL.md files.** Each is thin — frontmatter (name, description) + a few paragraphs explaining when to invoke + pointers into `core/` / `accents/` / `parent-layer/` / `docs/`. Use native Claude Code SKILL.md format.
13. **Write MANIFEST.md.** Active/dormant split, promotion triggers, trust boundaries, pointer to LINEAGE.md for provenance.
14. **Write README.md.** Human-facing: what this locker is, how to vendor it into a new project, what gets updated and how often, curation cadence.
15. **Write `scripts/vendor-into.sh`.** Consumer-project helper that copies files from here with a commit hash pin.

### Phase D — Discover + wire

16. **Add the locker index entry** to the project's top-level `CLAUDE.md` (master-index convention). For iq-locker's own CLAUDE.md (which doesn't yet exist as a user-authored file), this is the first user-authored content it'll have. For KGiQ-LLC, add the entry to `KGiQ-LLC/CLAUDE.md`.
17. **Empirically test spawn discoverability.** Spawn a subagent with a trivial design task (e.g. "suggest colors for a new Button variant in an IQ-branded app") and verify it reads MANIFEST → SKILL.md → `core/` content before proposing. If it doesn't, iterate on the master-index entry copy until it does.

### Phase E — Reconcile consumer projects (optional, separate sessions)

Once the locker is validated, reconcile each consumer project (booking-module, iq-publish, portfolio-front-end) in separate sessions. Each consumer project replaces its stray copies with locker-vendored files, using `scripts/vendor-into.sh` and recording a commit-hash pin. This is not blocking for the locker to exist; it's the downstream cleanup work the locker enables.

### Time estimate

- Phase A: ~1 hour (extraction + clean-up)
- Phase B: ~30–60 min (audit + LINEAGE.md)
- Phase C: ~1 hour (SKILL.md wrappers + MANIFEST + README)
- Phase D: ~30 min (wire + test spawn)
- Phase E: ~30 min per consumer project, whenever

Total for locker-exists-and-is-validated: **~3–3.5 hours** across A–D.

---

*Five parallel Explore subagents produced this audit. Empirical signals from actual file contents. Rerun or supplement with a Neon memory query if additional brand/design discoveries surface in Open Mind memory.*
