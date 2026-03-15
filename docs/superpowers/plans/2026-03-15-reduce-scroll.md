# Reduce Scroll — Two-Zone Layout Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a stat highlights grid to the hero and anchor links to the nav so visitors get immediate impact without scrolling.

**Architecture:** All changes are in a single file (`src/pages/index.astro`). No new files, no new components. Three isolated edits: section anchors, stat grid, and nav anchor links.

**Tech Stack:** Astro 5, vanilla CSS (CSS custom properties), HTML

---

## Chunk 1: Section Anchors + Scroll Offset

**Files:**
- Modify: `src/pages/index.astro`

### Task 1: Add `id` attributes to sections and `scroll-margin-top` to `.section`

- [ ] **Step 1: Add `id` to the three `<section class="section">` elements**

  Open `src/pages/index.astro`. Find the line:
  ```html
  <section class="section">
    <p class="section-eyebrow">What I work on</p>
  ```
  Change it to:
  ```html
  <section class="section" id="work-on">
    <p class="section-eyebrow">What I work on</p>
  ```

  Find:
  ```html
  <section class="section">
    <p class="section-eyebrow">Selected work</p>
  ```
  Change it to:
  ```html
  <section class="section" id="selected-work">
    <p class="section-eyebrow">Selected work</p>
  ```

  Find:
  ```html
  <section class="section">
    <p class="section-eyebrow">Writing & talks</p>
  ```
  Change it to:
  ```html
  <section class="section" id="writing">
    <p class="section-eyebrow">Writing & talks</p>
  ```

- [ ] **Step 2: Add `scroll-margin-top` to the existing `.section` CSS rule**

  Find the existing `.section` rule in the `<style>` block:
  ```css
  .section {
    padding: 72px 0;
    border-top: 1px solid var(--border);
  }
  ```
  Change it to:
  ```css
  .section {
    padding: 72px 0;
    border-top: 1px solid var(--border);
    scroll-margin-top: 64px; /* clear the 56px fixed nav + 8px breathing room */
  }
  ```

- [ ] **Step 3: Start the dev server and verify**

  Run:
  ```bash
  npm run dev
  ```
  Open `http://localhost:4321` in a browser. Verify:
  - Page looks identical to before (no visible change yet)
  - In browser devtools, confirm the three `<section>` elements have `id="work-on"`, `id="selected-work"`, and `id="writing"`
  - Navigate to `http://localhost:4321#work-on` — the page should scroll so "What I work on" is visible below the nav bar (not hidden behind it)

- [ ] **Step 4: Commit**

  ```bash
  git add src/pages/index.astro
  git commit -m "feat: add section anchor ids and scroll-margin-top"
  ```

---

## Chunk 2: Stat Highlights Grid

**Files:**
- Modify: `src/pages/index.astro`

### Task 2: Add stat grid HTML after `.hero-bio`

- [ ] **Step 1: Add the stat grid HTML**

  Find the `.hero-bio` paragraph in `src/pages/index.astro`:
  ```html
      <p class="hero-bio">
        I build and lead the ML infrastructure that powers Klaviyo's machine
        learning — Ray clusters running distributed compute across hundreds of
        nodes, serving platforms that compress deployment timelines from months to
        days, and the technical roadmaps that define how the platform evolves. My
        focus is on foundations that compound: infrastructure and strategy that
        multiply what ~40 ML engineers and data scientists can accomplish.
      </p>
    </section>
  ```

  Insert the stat grid between the closing `</p>` and the closing `</section>`:
  ```html
      <p class="hero-bio">
        I build and lead the ML infrastructure that powers Klaviyo's machine
        learning — Ray clusters running distributed compute across hundreds of
        nodes, serving platforms that compress deployment timelines from months to
        days, and the technical roadmaps that define how the platform evolves. My
        focus is on foundations that compound: infrastructure and strategy that
        multiply what ~40 ML engineers and data scientists can accomplish.
      </p>

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
    </section>
  ```

### Task 3: Add stat grid CSS and adjust `.hero` padding

- [ ] **Step 2: Add stat grid CSS rules**

  In the `<style>` block, add these rules after the `.hero-bio` rule:
  ```css
  /* ── Stat grid ── */
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
  ```

- [ ] **Step 3: Reduce `.hero` bottom padding (desktop)**

  Find:
  ```css
  .hero {
    padding: 132px 0 96px;
  }
  ```
  Change to:
  ```css
  .hero {
    padding: 132px 0 64px;
  }
  ```

- [ ] **Step 4: Reduce `.hero` bottom padding (mobile)**

  Find the mobile override inside `@media (max-width: 580px)`:
  ```css
  .hero { padding: 96px 0 72px; }
  ```
  Change to:
  ```css
  .hero { padding: 96px 0 48px; }
  ```

- [ ] **Step 5: Add mobile single-column override for stat grid**

  Inside the existing `@media (max-width: 580px)` block, add:
  ```css
  .stats-grid { grid-template-columns: 1fr; }
  ```

- [ ] **Step 6: Verify in browser**

  With `npm run dev` running, open `http://localhost:4321`. Verify:
  - A 2×2 grid of stat tiles appears below the bio paragraph
  - Each tile has a teal accent number/value and a muted label beneath
  - Tile backgrounds are a light off-white (barely visible difference from page bg)
  - The hero section doesn't push the first section too far down — the stat tiles should be visible without scrolling on a standard laptop screen (1280×800 or larger)
  - Resize to mobile width (<580px) — grid collapses to a single column, tiles stack vertically

- [ ] **Step 7: Commit**

  ```bash
  git add src/pages/index.astro
  git commit -m "feat: add stat highlights grid to hero"
  ```

---

## Chunk 3: Nav Anchor Links

> **Requires Chunk 1 first** — anchor ids (`#work-on`, `#selected-work`, `#writing`) must exist on the section elements before these nav links will work.

**Files:**
- Modify: `src/pages/index.astro`

### Task 4: Add anchor links to the nav

- [ ] **Step 1: Update the nav HTML**

  Find the `.nav-right` div in the nav:
  ```html
      <div class="nav-right">
        <a href="https://github.com/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">GitHub</a>
        <a href="https://linkedin.com/in/smitkiri" target="_blank" rel="noopener noreferrer" class="nav-ext">LinkedIn</a>
        <a href="mailto:smit.kiri@gmail.com" class="nav-ext">Email</a>
      </div>
  ```
  Replace it with:
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

- [ ] **Step 2: Add `.nav-anchor-group` and `.nav-sep` CSS**

  In the `<style>` block, add these rules after the `.nav-ext` rule:
  ```css
  .nav-anchor-group {
    display: contents; /* children participate in .nav-right flex layout directly */
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

- [ ] **Step 3: Verify in browser**

  With `npm run dev` running, open `http://localhost:4321`. Verify:
  - Nav shows: `Smit Kiri` on left · `Work on · Selected work · Writing · GitHub · LinkedIn · Email` on right
  - All nav items are the same muted monospace style, separated evenly
  - A faint dot `·` separates the anchor group from the external links
  - Hovering any nav link (anchor or external) turns it dark — same hover behavior
  - Clicking "Work on" smoothly scrolls to the "What I work on" section, landing below the nav bar
  - Clicking "Selected work" scrolls to "Selected work" section
  - Clicking "Writing" scrolls to "Writing & talks" section
  - Resize to ≤680px width — the three anchor links and separator dot disappear; only GitHub · LinkedIn · Email remain
  - Resize back to >680px — anchor links reappear

- [ ] **Step 4: Commit**

  ```bash
  git add src/pages/index.astro
  git commit -m "feat: add anchor nav links for section navigation"
  ```
