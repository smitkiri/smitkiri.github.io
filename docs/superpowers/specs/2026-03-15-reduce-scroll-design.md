# Design: Reduce Scroll — Two-Zone Layout

**Date:** 2026-03-15
**Status:** Approved

## Problem

The website is a single long-scroll page. Visitors (recruiters, ML peers, conference organizers) won't scroll through everything. Key metrics and impact are buried mid-paragraph, and there's no way to jump directly to the section you care about.

## Goals

- Visitors immediately understand expertise and impact without scrolling
- Enough context above the fold for recruiters/collaborators to decide whether to reach out
- Deep readers can still access the full narrative

## Approach: Two-Zone Layout

### Zone 1 — Hero (above the fold)

Keep all existing hero content exactly as-is:
- Name, role, profile photo
- Full bio paragraph (unchanged)

Add a **2×2 stat highlights grid** immediately after the `.hero-bio` paragraph.

The stat grid adds ~120px of height inside `.hero`. To preserve the "above the fold" goal, reduce `.hero` bottom padding from `96px` to `64px` (mobile: from `72px` to `48px`).

**HTML structure:**

```html
<div class="stats-grid">
  <div class="stat-tile">
    <span class="stat-value">350–450</span>
    <span class="stat-label">ML jobs per day on DART Jobs</span>
  </div>
  <div class="stat-tile">
    <span class="stat-value">7×</span>
    <span class="stat-label">increase in modeling runs (DART V2)</span>
  </div>
  <div class="stat-tile">
    <span class="stat-value">Months → Days</span>
    <span class="stat-label">model deployment via DART Online</span>
  </div>
  <div class="stat-tile">
    <span class="stat-value">40+</span>
    <span class="stat-label">ML engineers on the platform</span>
  </div>
</div>
```

**CSS — new rules:**

```css
.stats-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.75rem;
  margin-top: 2rem;
}

.stat-tile {
  background: color-mix(in srgb, var(--border) 30%, var(--bg));
  border: 1px solid var(--border);
  border-radius: 6px;
  padding: 0.75rem 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.stat-value {
  font-family: var(--font-display);
  font-size: 1.3rem;
  font-weight: 500;
  color: var(--accent);
  letter-spacing: -0.02em;
  line-height: 1.2;
}

.stat-label {
  font-family: var(--font-body);
  font-size: 0.75rem;
  color: var(--muted);
  line-height: 1.4;
}

/* Mobile: single column */
@media (max-width: 580px) {
  .stats-grid {
    grid-template-columns: 1fr;
  }
}
```

Note: `color-mix()` is already used elsewhere in this codebase (`.writing-featured`) with no fallback — same pattern here.

**CSS — modify existing `.hero` rule:**

Change the existing `.hero` padding:
- Desktop: `padding: 132px 0 96px` → `padding: 132px 0 64px`
- Mobile (`@media (max-width: 580px)`): `padding: 96px 0 72px` → `padding: 96px 0 48px`

### Zone 2 — Below the fold

All three existing sections stay **completely unchanged** in content and styling. Add `id` attributes to each `<section>` element (not the inner `<p class="section-eyebrow">`):

```html
<section class="section" id="work-on"> ... </section>
<section class="section" id="selected-work"> ... </section>
<section class="section" id="writing"> ... </section>
```

The `id="work-on"` slug is an intentional shortening of "What I work on" for URL cleanliness. The hero `<section class="hero">` intentionally gets no `id` — the existing `<a href="#" class="nav-name">` scroll-to-top behavior is unchanged.

**Scroll offset:** Append `scroll-margin-top: 64px` as a third property to the existing `.section` rule (do not create a separate rule):

```css
/* existing rule — append scroll-margin-top */
.section {
  padding: 72px 0;
  border-top: 1px solid var(--border);
  scroll-margin-top: 64px; /* clear the 56px fixed nav + 8px breathing room */
}
```

### Nav changes

Add three anchor links to `.nav-right` before the existing external links, followed by a separator span. The anchor links reuse the existing `.nav-ext` class — no new class needed.

Nav label shorthands are intentional display truncations:
- "Work on" = "What I work on"
- "Selected work" = "Selected work" (exact match)
- "Writing" = "Writing & talks"

**Hide the anchor group below `max-width: 680px`** — at narrower viewports, the 7-item nav (3 anchors + separator + 3 external links) at `gap: 1.5rem` becomes too crowded. The 680px threshold gives comfortable breathing room before the `720px` max-width container edge. Mobile visitors scroll naturally and don't need the anchor shortcuts. Use `display: none` via a media query on `.nav-anchor-group`.

**Before:**
```html
<div class="nav-right">
  <a href="https://github.com/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">GitHub</a>
  <a href="https://linkedin.com/in/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">LinkedIn</a>
  <a href="mailto:smit.kiri@gmail.com" class="nav-ext">Email</a>
</div>
```

**After:**
```html
<div class="nav-right">
  <div class="nav-anchor-group">
    <a href="#work-on" class="nav-ext">Work on</a>
    <a href="#selected-work" class="nav-ext">Selected work</a>
    <a href="#writing" class="nav-ext">Writing</a>
    <span class="nav-sep">·</span>
  </div>
  <a href="https://github.com/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">GitHub</a>
  <a href="https://linkedin.com/in/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">LinkedIn</a>
  <a href="mailto:smit.kiri@gmail.com" class="nav-ext">Email</a>
</div>
```

**CSS additions:**

```css
.nav-anchor-group {
  display: contents; /* renders children inline in the flex row */
}

.nav-sep {
  font-size: 0.75rem;
  color: var(--border);
  user-select: none;
}

@media (max-width: 680px) {
  .nav-anchor-group {
    display: none;
  }
}
```

`display: contents` makes `.nav-anchor-group` invisible as a box — its children participate directly in the `.nav-right` flex layout, so the existing `gap: 1.5rem` applies uniformly. On mobile, `display: none` hides the entire group (links + separator) without touching the external link layout.

## What Does Not Change

- All existing nav styles (`.nav-ext`, `.nav-inner`, `#nav`)
- Bio text (verbatim)
- All section content and styling (except `id` attributes added)
- Typography, colors, spacing, fonts (except `.hero` bottom padding reduction)

## Files to Modify

`src/pages/index.astro` only:

1. Add `id` attributes to the three `<section class="section">` elements
2. Add stat grid HTML after `.hero-bio`
3. Add new CSS rules: `.stats-grid`, `.stat-tile`, `.stat-value`, `.stat-label`, mobile override
4. Modify existing `.hero` padding (desktop + mobile)
5. Append `scroll-margin-top: 64px` to existing `.section` rule
6. Add `.nav-anchor-group` wrapping div + `.nav-sep` span to `.nav-right`
7. Add `.nav-anchor-group`, `.nav-sep`, and mobile `.nav-anchor-group` CSS rules
