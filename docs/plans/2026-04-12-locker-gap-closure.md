# IQ Design System Locker — Gap Closure Plan

**Date surfaced:** 2026-04-12
**Source:** Task 19 spawn discoverability test (see `LINEAGE.md` §8)
**Locker:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/`
**Status:** Open — waiting for a dedicated closure session
**Recommended ordering:** Close these before starting Phase E consumer migrations so `iq-publish`, `booking-module`, and `portfolio-front-end` can reconcile against a gap-free locker rather than inheriting ambiguity.

## Why this doc exists

Task 19's spawn discoverability test passed on the first try — a blank-slate subagent in `KGiQ-LLC` found the canonical source of truth in three reads via `KGiQ-LLC/CLAUDE.md` → `iq-design-locker/MANIFEST.md` → `docs/iq-brand-system.md`. The routing chain is proven.

But while producing its button-variant answer, the subagent surfaced **three content gaps** in the locker. None of these broke the test — the subagent found everything the docs actually had to offer — but they are places where the docs either (a) don't say what a working consumer needs them to say, (b) contradict themselves across canonical vs legacy files, or (c) leave an unresolved implementation question that a reader cannot answer from the docs alone.

These are dead zones in the locker. The user directive is to remove any gray from the locker: fill these gaps before they confuse a real consumer migration. This doc tracks them so nothing gets lost.

---

## Gap 1 — Parent layer does not override fill tokens

**Files involved:**
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/iq-site-tokens.css`
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-tokens.css` (lines 53-54 light fills; lines 145-146 dark fills)
- (consumer evidence) `/Users/kmgdev/KGiQ-LLC/website/` — how the KGiQ parent site actually initializes theme

**Evidence from the spawn test:**

The subagent needed `--iq-color-fill-secondary` to resolve a button background value for the KGiQ parent site. It found that `parent-layer/iq-site-tokens.css` overrides `--iq-color-surface-*` (canvas, primary, secondary, overlay) and `--iq-color-text-*` but does NOT override `--iq-color-fill-secondary` or `--iq-color-fill-tertiary`. Those tokens inherit from `core/iq-tokens.css`, where the light-mode values are:

```css
--iq-color-fill-secondary: #F5F5F7;  /* Apple Light — Fill Secondary */
--iq-color-fill-tertiary: #EBEBED;   /* Apple Light — Fill Tertiary */
```

In the parent layer's dark-first context (`canvas #0B1220`, `surface-primary #0F172A`, `surface-secondary #334155`), a light `#F5F5F7` fill would glare unacceptably on a slate-900 canvas. That is not a plausible design intent.

**Two possible explanations:**

1. **The parent layer is missing the fill overrides** — a real omission/bug. It should include `--iq-color-fill-secondary: #334155` (or similar) and `--iq-color-fill-tertiary: rgba(255,255,255,0.06)` or the equivalent dark values directly in `:root`.
2. **The parent layer assumes `data-theme="dark"` is set at runtime on the HTML element** and relies on `core/iq-tokens.css` lines 145-146 (`rgba(255,255,255,0.06)` / `rgba(255,255,255,0.10)`) to resolve via the `[data-theme="dark"]` cascade. Under this model the parent layer is additive on top of a dark-theme base, and the fill values are correct at runtime.

**Which is it?** The docs don't say. The subagent could not resolve this from the locker alone.

**Proposed fix:**

1. **Investigate first.** Read `/Users/kmgdev/KGiQ-LLC/website/` (or the KGiQ parent site's actual source, wherever it lives — check `portfolio-front-end/public/vendor/` for the vendored parent layer) to see if `data-theme="dark"` is initialized on the root element, in CSS, or via JavaScript at runtime. Five-minute investigation.
2. **If explanation 2 is correct:** Add a header comment to `parent-layer/iq-site-tokens.css` explicitly stating *"This layer assumes `data-theme='dark'` is set on the document root. Without that, fill and hover tokens will resolve to their light-mode defaults from `core/iq-tokens.css` and display incorrectly."* Also add this note to `docs/iq-brand-system.md` §2 token routing rules so future consumers know. No CSS changes.
3. **If explanation 1 is correct:** Add explicit fill overrides to `parent-layer/iq-site-tokens.css`:
   ```css
   :root {
     /* existing overrides ... */
     --iq-color-fill-secondary: #334155;       /* slate-700, matches surface-secondary */
     --iq-color-fill-tertiary: rgba(255,255,255,0.08);
   }
   ```
   Document in LINEAGE.md §2 (drift reconciliation) as a discovery-driven fix.

**Effort:** ~15 minutes investigation + 5 minutes fix/doc.
**Dependencies:** Read the actual KGiQ parent-site initialization code. If that code is outside the dev_projects tree, might need guidance on where to look.

---

## Gap 2 — `iq-brand-system.md` §6 is thin on concrete button specifics

**Files involved:**
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-brand-system.md` §6 (Components → Button hierarchy)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/history/iq-components-guide.md` (legacy; explicitly superseded)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-tokens.css` (spacing tokens `--iq-space-2` through `--iq-space-6`)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/core/iq-typography.css` (body emphasis type scale)

**Evidence from the spawn test:**

The subagent could resolve `--iq-color-fill-secondary`, `--iq-radius-md`, focus-ring formula, and press-transform from §6. But §6 does NOT specify:

- **Button padding** (horizontal/vertical)
- **Button label font-size**
- **Button label font-weight**
- **Hover background** for any variant
- **Hover text color** for any variant

The subagent had to fall back to `docs/history/iq-components-guide.md` (which is archived and marked legacy) to get concrete values: `padding: 8px 16px`, `font-size: 14px`, `font-weight: 500`. It reported these with explicit legacy-provenance caveats rather than presenting them as canonical.

The legacy values map cleanly to canonical tokens:
- `padding: 8px 16px` → `var(--iq-space-2) var(--iq-space-4)`
- `font-weight: 500` → `var(--iq-type-body-emphasis-weight)` (exists in `core/iq-typography.css` line 40)
- `font-size: 14px` → NOT aligned with canonical (`--iq-type-body-emphasis-size: 15px` per `core/iq-typography.css` line 38). Either the legacy is stale or §4 needs a dedicated button size.

**Why this matters:** Any new consumer writing a button has to either (a) fall back to the same legacy doc the subagent did, which should not be a canonical reference path, or (b) guess. Both are drift sources.

**Proposed fix:**

Rewrite §6 button hierarchy with complete token mappings. Minimal viable template:

```markdown
### Button hierarchy (§6.x)

All five button variants share the following base specs unless overridden:

| Property | Token | Value |
|---|---|---|
| Padding | `var(--iq-space-2) var(--iq-space-4)` | `8px 16px` |
| Border radius | `var(--iq-radius-md)` | `8px` |
| Font family | `var(--iq-font-body)` | Inter |
| Font size | `var(--iq-type-body-emphasis-size)` | `15px` |
| Font weight | `var(--iq-type-body-emphasis-weight)` | `500` |
| Line height | `var(--iq-type-body-emphasis-lh)` | `22px` |
| Transition | `background var(--iq-duration-fast) var(--iq-ease-default), color var(--iq-duration-fast) var(--iq-ease-default)` | 200ms |
| Press transform | `scale(0.97)` | — |
| Focus ring | `0 0 0 3px color-mix(in srgb, var(--iq-tint) 40%, transparent)` | — |

Per-variant background / text / hover-background / hover-text:

| Variant | Background | Text | Hover bg | Hover text |
|---|---|---|---|---|
| Primary | `var(--iq-tint)` | `var(--iq-color-text-on-tint)` | `color-mix(var(--iq-tint), black 8%)` | (unchanged) |
| Secondary | `var(--iq-color-fill-secondary)` | `var(--iq-color-text-primary)` | `var(--iq-color-fill-tertiary)` | (unchanged) |
| Tertiary | `transparent` | `var(--iq-color-text-primary)` | `var(--iq-color-fill-secondary)` | (unchanged) |
| Destructive | `var(--iq-color-danger)` | `var(--iq-color-text-on-danger)` | `color-mix(var(--iq-color-danger), black 8%)` | (unchanged) |
| Link | `transparent` | `var(--iq-tint)` | `transparent` | `color-mix(var(--iq-tint), white 12%)` |
```

This is a proposal, not a final spec — the hover rules in particular need design owner review. The point is: concrete canonical values for all five variants, all routed through existing tokens, in the canonical doc. No fallback to the legacy guide.

**Effort:** ~30-45 minutes to draft, review against existing consumer usage in iq-publish / booking-module / iq-circle, and commit. Should be a spec review cycle, not a solo rewrite.
**Dependencies:** Design owner sign-off on hover rules (especially whether Primary hover uses `color-mix` or a new `--iq-tint-hover` token); may cross-reference the 2026-03-29 panel doc for any button decisions it made.

---

## Gap 3 — Possible "Secondary = Steel Blue" regression from the legacy guide

**Files involved:**
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-brand-system.md` §6 Secondary button variant
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/history/iq-components-guide.md` line 100 (legacy)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/parent-layer/iq-site-tokens.css` (`--iq-tint-base: #5E7F9B`)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/docs/iq-brand-system.md` §2 token routing rules (tint vs accent)

**Evidence from the spawn test:**

The legacy components guide line 100 documents:

```
| **Secondary** | Steel Blue (#5E7F9B) | White | Lighten 10% | Important secondary |
```

This steel-blue-filled Secondary pattern is not carried into the canonical `iq-brand-system.md` §6, which routes Secondary to `--iq-color-fill-secondary` (a neutral fill like `#F5F5F7` or dark-mode `rgba(255,255,255,0.06)`).

Two plausible interpretations:

1. **The §6 rewrite dropped it intentionally.** §2 "Token Routing Rules" explicitly says *"Interactive elements (buttons, focus rings, links, toggles) use `--iq-tint`, NOT `--iq-accent`"* — which arguably pushes Secondary toward using `--iq-tint` for emphasis. But §6 Secondary doesn't use `--iq-tint` either; it uses a neutral fill. Either §6 is consistent with §2 (neutral fills are the intended Secondary treatment across all products) OR §6 has drifted from the legacy intent that Secondary should be a "tinted subordinate" (steel blue on the KGiQ parent site, teal on Booking, indigo on Publish, rose on Circle).
2. **The §6 rewrite accidentally dropped it.** The synthesized `iq-brand-system.md` was written 2026-04-12 from cross-app audits. If the audit samples didn't hit a "tinted Secondary" usage in any of Booking / Publish, the pattern could have been omitted by accident.

**Why this matters:** If the intent is tinted Secondary, the canonical doc is currently wrong and any new consumer building from §6 will produce neutral-filled buttons that don't match the brand identity. If the intent is neutral Secondary, the legacy guide is stale (fine — it's archived) but `iq-brand-system.md` should have an explicit rationale note so future readers don't wonder why the "obviously steel-blue" pattern was dropped.

**Proposed fix:**

1. **Confirm intent with the design owner.** One question: *"For the KGiQ parent site, should a Secondary button be (a) steel-blue-filled with white text (legacy, `--iq-tint-base` approach), or (b) neutral-filled with slate text (canonical §6 current approach)?"* This is a 2-minute decision.
2. **If (a) tinted Secondary is intended:** Rewrite §6 Secondary to route through `--iq-tint` (not `--iq-color-fill-secondary`). Add an explicit rationale note: *"Secondary uses `--iq-tint` so each product's parent-site 'subordinate action' reflects that product's identity (steel blue for KGiQ, teal for Booking, indigo for Publish, rose for Circle). See §2 token routing rules."* Probably also update §2 to flag Secondary as an exception to the strict "buttons use fill tokens" convention.
3. **If (b) neutral Secondary is intended:** Add a rationale note to §6 Secondary explaining why: *"Secondary uses a neutral fill (not `--iq-tint`) so that Primary + Secondary have clear visual hierarchy even on product-accent-heavy surfaces. Tinted Secondary would compete with Primary for attention."* Also add a note saying this is a deliberate change from the pre-2026-04-12 legacy guide.
4. **Either way:** Update LINEAGE.md §8.5 with the resolution and mark Gap 3 closed.

**Effort:** ~5 minutes decision + ~15 minutes implementation + LINEAGE update.
**Dependencies:** Design owner answer on the Primary/Secondary color hierarchy question. May also need to check how iq-circle's current front-end (actively being built by the user) intends to render Secondary.

---

## Closure checklist

- [ ] Gap 1 investigated: read KGiQ parent-site `data-theme` initialization code
- [ ] Gap 1 fixed: either parent-layer override added OR header comment documenting the dark-theme-at-runtime assumption
- [ ] Gap 2 drafted: complete button hierarchy spec with per-variant tokens
- [ ] Gap 2 reviewed: design owner sign-off on hover rules
- [ ] Gap 2 committed: `iq-brand-system.md` §6 rewritten with concrete canonical values
- [ ] Gap 3 decided: tinted vs neutral Secondary
- [ ] Gap 3 committed: §6 Secondary updated with rationale note
- [ ] LINEAGE.md §8.5 updated: each gap marked closed with the resolution summary
- [ ] `lockers-index.md` updated: status flipped `validated` → `live` after first Phase E consumer migration

## Recommended closure sequence

1. **Single focused session** (estimated 1-1.5 hours): address all three gaps in order (Gap 1 investigation is fast; Gap 2 is the longest; Gap 3 is short decision + short edit).
2. **Quality gate**: run a second spawn discoverability test after the gaps are closed, using the same button-variant prompt — verify the subagent no longer has to fall back to `docs/history/iq-components-guide.md` for any value.
3. **After closure**: proceed to Phase E consumer migrations starting with `iq-publish`. See `docs/plans/2026-04-12-cross-app-drift-report.md`.

## Out of scope for this plan

- **Token content decisions** (should the button corner radius be 8px or 6px?) — those belong in a separate design review, not a gap-closure session. This plan is about making the locker internally consistent and complete against what already exists.
- **New features** (e.g., "add a Ghost button variant") — those are additive design work. This plan only addresses what the canonical doc should say about the five existing variants.
- **Phase E consumer migrations** — covered by `2026-04-12-cross-app-drift-report.md`. Sequentially after gap closure.
