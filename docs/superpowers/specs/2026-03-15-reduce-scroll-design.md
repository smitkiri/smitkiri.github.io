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

Add a **2×2 stat highlights grid** below the bio:

| Stat | Label |
|------|-------|
| 350–450 | ML jobs per day on DART Jobs |
| 7× | increase in modeling runs (DART V2) |
| Months → Days | model deployment via DART Online |
| 40+ | ML engineers on the platform |

Each tile: teal accent number/value, muted label beneath, light background (`#F8FAFC`), subtle border.

### Zone 2 — Below the fold

All three existing sections stay **completely unchanged** in content and styling. Each section gets an HTML `id` anchor:

- `id="work-on"` → "What I work on"
- `id="projects"` → "Selected work"
- `id="writing"` → "Writing & talks"

### Nav changes

The sticky nav gains anchor links on the right side, grouped with a subtle separator from the external links:

**Before:** `Smit Kiri` · GitHub · LinkedIn · Email

**After:** `Smit Kiri` · Work on · Projects · Writing `·` GitHub · LinkedIn · Email

- Anchor links styled the same as current external links (monospace, muted color)
- A faint dot separator (`·`) between the two groups
- Smooth scroll behavior already enabled via `html { scroll-behavior: smooth }`

## What Does Not Change

- Bio text (verbatim)
- All section content and styling
- Typography, colors, spacing, fonts
- Mobile layout (stat grid collapses to 2×2 on mobile too, already fits)

## Files to Modify

- `src/pages/index.astro` — only file that needs changes:
  1. Add stat grid HTML+CSS after `.hero-bio`
  2. Add `id` attributes to the three `<section>` elements
  3. Add anchor links to the nav
