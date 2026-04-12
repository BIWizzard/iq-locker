# iQ Design System — Cross-App Drift Report

**Date:** 2026-04-12
**Session:** 2026-04-12-c7f3
**Scope:** Full implementation audit of iQ Booking and iQ Publish against the canonical locker

---

## Critical Drift (apps disagree with each other or the spec)

### 1. `--iq-color-info` hardcoded in iQ Publish

**Booking:** `--iq-color-info: var(--iq-accent)` — chains correctly
**Publish:** `--iq-color-info: #3B82F6` — hardcoded blue
**Canonical:** `var(--iq-accent)` — Booking is correct

This is the exact issue that drove the Wave 1 base-selection reversal during the locker build (2026-04-11). The hardcode in Publish's vendored `iq-tokens.css` means info elements render as blue regardless of what accent is active. After locker migration, Publish must fix this — either by re-vendoring from the locker (which has the correct `var(--iq-accent)`) or by adding a local one-line override.

### 2. Font loading gap in iQ Publish

**Booking admin:** Loads all 3 iQ fonts (Plus Jakarta Sans, Inter, JetBrains Mono) via `next/font/google`
**Publish:** Only loads Inter. Plus Jakarta Sans and JetBrains Mono are MISSING.
**Impact:** All headings using `.iq-title-*` or `--iq-font-heading` silently fall back to system UI. Data displays using `--iq-font-mono` fall back to monospace system font.

### 3. No reusable component library in iQ Publish

**Booking:** Has `apps/admin/src/components/iq/` with Button, Modal, Toggle, SegmentedControl, SectionGroup, Skeleton, EmptyState, Toast
**Publish:** No shared components. Card/button patterns repeated via inline style props.
**Impact:** No consistency enforcement. Each new surface re-implements the same patterns.

### 4. Glassmorphism on L1 cards in iQ Booking

**Booking:** `glassCard` pattern applies `backdrop-filter: blur(12px)` on L1 cards (SectionGroup level)
**Publish:** No glassmorphism on any surface
**Panel decision (2026-03-29):** No glassmorphism on customer-facing surfaces. Brief says admin L3+ only.
**Impact:** Booking's L1 glass cards may violate the panel decision. The blur is cosmetically inert in light mode but is applied regardless.

### 5. Focus ring inconsistency on Booking page

**Booking admin:** `var(--iq-tint)` — correct (tenant-aware)
**Booking page:** `var(--iq-accent)` — should be `--iq-tint`
**Publish:** `var(--iq-tint)` — correct
**Impact:** Tenant-branded booking pages show the product accent for focus rings instead of the tenant's brand color.

---

## Moderate Drift (different approaches, both partially correct)

### Dark mode persistence
- Booking: localStorage key `iq-theme`
- Publish: Cookie `iq-publish-theme` (365-day expiry)
- Recommendation: Standardize on one approach for new apps. Cookies are better for SSR.

### Token import mechanism
- Booking: Monorepo package `@booking-module/shared-ui/styles`
- Publish: Vendored copies at `vendored/iq-tokens.css`
- New apps (iQ Circle): Will use `iq-design-locker-vendor/` via `vendor-into.sh`

### Motion token adoption
- Both apps partially adopt tokens
- Simple transitions default to Tailwind's 150ms ease, bypassing `--iq-duration-*`
- Complex animations use Framer Motion with shared-ui spring presets (Booking) or hardcoded values (Publish)
- `prefers-reduced-motion` handling: CSS token collapse works for token-referenced durations but NOT for Tailwind utility or hardcoded transitions

### Typography utility classes
- Both define `.iq-title-1`, `.iq-display`, etc. in the token file
- Neither app uses them — both apply typography via inline style props
- Recommendation: New apps should adopt the utility classes

### Font variable chain
- Booking loads fonts via `next/font/google` into CSS variables (`--font-inter`, `--font-plus-jakarta`, `--font-jetbrains`)
- But `--iq-font-body` references `"Inter"` by string name, not `var(--font-inter)`
- This works because `next/font` injects `@font-face` declarations, but it's not using the optimized subset references

---

## Aligned (both apps correct)

- Surface hierarchy (Canvas → L1 → L2 → L3): Consistent 4-level model
- `[data-theme="dark"]` CSS mechanism: Same selector, no Tailwind `dark:` variants
- Semantic color values: Identical hex values for success/warning/error
- Spacing scale: Identical 4px grid
- Border radius scale: Identical
- Shadow system: Identical resting/raised/overlay
- Type scale values: Identical (when fonts load)

---

## Hardcoded Color Hotspots

### iQ Booking
- `apps/admin/src/app/calendar/page.tsx:297` — badge with hardcoded Slate-800 colors (does not adapt to light mode)
- `apps/admin/src/app/calendar/page.tsx:58-65` — provider colors (acceptable — third-party brand colors)
- `apps/admin/src/components/CalendarWriteQueueStatus.tsx:96-137` — badge RGBA hardcoded (should use `color-mix()`)
- `apps/admin/src/components/CalendarConnectionCard.tsx:22-38` — status badge colors (match token values but not referenced)

### iQ Publish
- `src/app/no-membership/page.tsx` — entire page outside token system (hardcoded dark palette, `#6366F1`)
- `src/app/(auth)/join/page.tsx`, `complete-profile/page.tsx` — `color: "#FFFFFF"` instead of `var(--iq-tint-text)`
- `src/api/v1/tenant/branding/route.ts:7` — powered-by badge HTML hardcodes `#6366F1` (acceptable — external HTML string)

---

## Recommendations for Phase E Consumer Migration

1. **iQ Publish migration (S→M after font fix):** Re-vendor from locker (fixes `--iq-color-info`), add Plus Jakarta Sans + JetBrains Mono to font loading, tokenize `no-membership/page.tsx`, fix auth page `#FFFFFF` to `var(--iq-tint-text)`
2. **iQ Booking migration (M):** Re-vendor from locker, fix calendar badge to use surface tokens, fix booking page focus ring to `--iq-tint`, fix booking page dark mode script to set `data-theme`, evaluate L1 glass card pattern against panel decision
3. **iQ Circle (new):** Vendored from locker at inception. Must load all 3 fonts from day one. Should adopt utility classes and build reusable component library.
4. **portfolio-front-end (L):** The `--iq-color-info` visible-UI-shift finding (gold replaces blue in booking widget info chips) remains. Needs visual regression baseline before migration.
