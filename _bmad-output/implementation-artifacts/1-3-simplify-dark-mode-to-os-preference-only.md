# Story 1.3: Simplify Dark Mode to OS Preference Only

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **portfolio visitor**,
I want the site to respect my OS dark/light preference automatically,
So that I get a comfortable viewing experience without needing to find a toggle.

## Acceptance Criteria

1. **Given** the portfolio currently has a visible dark mode toggle button
   **When** the dark mode simplification is completed
   **Then** the toggle button is removed from the nav

2. **And** `assets/js/darkmode.js` is simplified to only detect `prefers-color-scheme: dark` (or replaced with pure CSS media query)
   > **Decision (Story 1.3):** Chose minimal JS approach (Option A). Pure CSS not viable because `@custom-variant dark` requires `.dark` class selector — switching to `@media` would require rewriting all `.dark .prose` selectors.

3. **And** localStorage persistence for dark mode preference is removed

4. **And** visitors with OS dark preference see the dark theme automatically

5. **And** visitors with OS light preference (or no preference) see the light theme

6. **And** existing `dark:` Tailwind utility classes continue to function correctly

7. **And** `pnpm run test` passes

## Tasks / Subtasks

- [x] Task 1: Replace JS-based dark mode with CSS-only `prefers-color-scheme` (AC: #2, #3, #4, #5)
  - [x] 1.1 Update `layouts/partials/head.html`: replaced inline script with OS-preference-only detection + change listener
  - [x] 1.2 Delete `assets/js/darkmode.js` entirely (localStorage toggle logic no longer needed)
  - [x] 1.3 Update `layouts/partials/footer.html`: removed the darkmode.js script block

- [x] Task 2: Remove toggle button from nav (AC: #1)
  - [x] 2.1 Update `layouts/partials/nav.html`: removed the toggle button block with SVG icons

- [x] Task 3: Update CSS dark mode variant (AC: #6)
  - [x] 3.1 No CSS change needed — `@custom-variant dark` stays as-is since `.dark` class is still applied by head script
  - [x] 3.2 Verified `.dark .prose` styles in main.css still apply correctly (unchanged selectors)

- [x] Task 4: Clean up configuration (AC: #1)
  - [x] 4.1 Updated `hugo.yaml`: removed `darkmode_js` config, set `disable_theme_toggle: true`

- [x] Task 5: Verify build and functionality (AC: #7)
  - [x] 5.1 Run `pnpm run build` — succeeded
  - [x] 5.2 Run `pnpm run test` — Hugo build passed
  - [x] 5.3 Verified `pnpm run dev` — server starts, no JS errors in console

## Dev Notes

### Critical Decision: CSS-Only vs Minimal JS

**Two approaches exist:**

**Option A: Pure CSS (RECOMMENDED)**
Use `@media (prefers-color-scheme: dark)` in the Tailwind `@custom-variant` directive. No JS needed at all. The `.dark` class is applied via CSS media query.

**Implementation:**
```css
/* In main.css — replace the existing @custom-variant line */
@custom-variant dark (&:where(.dark, .dark *));
```

And in `head.html`, replace the inline script with:
```html
<script>
  // Apply dark class based on OS preference — no localStorage, no toggle
  if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
    document.documentElement.classList.add('dark');
  }
  // Listen for OS preference changes
  window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', e => {
    document.documentElement.classList.toggle('dark', e.matches);
  });
</script>
```

**Why minimal JS over pure CSS?** The `@custom-variant dark` in Tailwind uses `.dark` class selector (not media query). Changing this to media query would require also changing all `.dark .prose` styles in main.css. The simplest, least-risky approach: keep the `.dark` class mechanism, just control it via OS preference detection instead of localStorage/toggle.

**Option B: Change @custom-variant to media query**
Would require rewriting all `.dark .prose` selectors to use `@media (prefers-color-scheme: dark)` instead. More invasive, higher risk, no benefit.

**Decision: Option A** — minimal JS that applies `.dark` class from OS preference. Zero risk to existing `dark:` utility classes.

### Current Dark Mode Architecture (What Exists)

**1. Inline script in `head.html` (lines 37-44):**
```js
if (localStorage.getItem('color-theme') === 'dark' ||
    (!('color-theme' in localStorage) &&
     window.matchMedia('(prefers-color-scheme: dark)').matches)) {
  document.documentElement.classList.add('dark');
} else {
  document.documentElement.classList.remove('dark')
}
```
- Checks localStorage first, falls back to OS preference
- Runs in `<head>` to prevent FOUC (flash of unstyled content)
- **KEEP the `<head>` location** — just simplify the logic

**2. `assets/js/darkmode.js` (41 lines):**
- IIFE with toggle button click handler
- Reads `#theme-toggle`, `#theme-toggle-dark-icon`, `#theme-toggle-light-icon` from DOM
- Toggles `.dark` class on `<html>` + persists to localStorage
- **DELETE this file entirely**

**3. Toggle button in `nav.html` (lines 84-94):**
- Conditional on `{{ if not .Site.Params.Disable_theme_toggle }}`
- Contains sun/moon SVG icons
- **DELETE this block entirely**

**4. Script tag in `footer.html` (lines 80-83):**
- Loads darkmode.js via Hugo's `resources.Get "js/darkmode.js" | js.Build`
- Conditional on `{{ if not .Site.Params.Disable_theme_toggle }}`
- **DELETE this block entirely**

**5. `@custom-variant dark` in `main.css` (line 7):**
- `@custom-variant dark (&:where(.dark, .dark *));`
- Makes Tailwind `dark:` utilities match `.dark` class
- **KEEP this line unchanged** — it still works, just `.dark` comes from OS now

**6. `.dark .prose` styles in `main.css` (lines 426-493):**
- All dark mode prose overrides use `.dark` selector
- **KEEP all these unchanged** — `.dark` class still applied by head script

**7. `hugo.yaml` config:**
- `darkmode_js:` list and `disable_theme_toggle: false`
- **Update or remove** since darkmode.js is deleted

### What NOT to Touch

- `assets/css/design-tokens.css` — has `.light {}` and `.dark {}` scoped tokens; these are independent
- `assets/css/main.css` prose/theme section — `.dark .prose` styles stay as-is
- `@custom-variant dark` line — stays as-is
- `@theme` block — no changes needed
- Any template `dark:` utility classes — they all work via `.dark` class on `<html>`

### File Changes Summary

**Delete:**
- `assets/js/darkmode.js`

**Modify:**
- `layouts/partials/head.html` — simplify inline script (remove localStorage, keep OS detection)
- `layouts/partials/nav.html` — remove toggle button block (lines 84-94)
- `layouts/partials/footer.html` — remove darkmode.js script block (lines 80-83)
- `hugo.yaml` — remove `darkmode_js` config, set `disable_theme_toggle: true`

**No changes:**
- `assets/css/main.css` — CSS stays identical
- `assets/css/design-tokens.css` — tokens stay identical

### Project Structure Notes

- All changes align with architecture decision: "light gallery (portfolio) vs dark workshop (backoffice)"
- `dark:` CSS variants kept at zero cost — they're just CSS selectors
- No conflict with design-tokens.css `.light`/`.dark` scoping (those use different selectors)
- Party Mode Decision confirms: "Portfolio uses OS `prefers-color-scheme` only, no visible toggle button, no localStorage persistence"

### References

- [Source: _bmad-output/planning-artifacts/epics.md § Story 1.3: Simplify Dark Mode to OS Preference Only]
- [Source: _bmad-output/planning-artifacts/epics.md § Party Mode Decisions — Dark mode toggle removed]
- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md § Two-app visual identity]
- [Source: layouts/partials/head.html lines 37-44 — current inline dark mode script]
- [Source: assets/js/darkmode.js — current toggle implementation (41 lines)]
- [Source: layouts/partials/nav.html lines 84-94 — toggle button]
- [Source: layouts/partials/footer.html lines 80-83 — darkmode.js script tag]
- [Source: assets/css/main.css line 7 — @custom-variant dark definition]
- [Source: hugo.yaml lines 11-12 — darkmode_js config]

## Previous Story Intelligence

### Learnings from Story 1.2 (Implement Design Tokens & CSS Pipeline)

**What was done:**
- Created `assets/css/design-tokens.css` (402 lines, copy of spec)
- Added `@import "./design-tokens.css";` as first line of `main.css`
- Added semantic token mappings to `@theme` block
- Kept TailBliss legacy colors for backward compatibility

**Key insight for Story 1.3:**
- `main.css` import chain is: tokens → TailwindCSS → `@custom-variant dark` → `@utility` → styles
- The `@custom-variant dark` line at line 7 uses `.dark` class selector — our simplified script must still add `.dark` to `<html>` for this to work
- Design tokens have `.light {}` and `.dark {}` blocks that use class selectors — these are independent from the Tailwind dark variant
- Don't modify the CSS pipeline at all — this story only touches JS and HTML templates

**Testing approach that worked:**
1. `pnpm run build` after changes
2. `pnpm run test` for Hugo validation
3. Browser check for console errors
4. Incremental: test after each file change

**Code patterns established:**
- Comment stubs: `{{/* Section — redesigned in Story X.Y */}}`
- Build validation: `pnpm run test` before marking done

### Learnings from Story 1.1 (Remove TailBliss Defaults)

- Straightforward deletion story — no issues
- Build passed immediately after cleanup
- Grep codebase for references to deleted files after deletion

## Git Intelligence Summary

**Last 5 commits:**
1. `32a92e0` — "story1.2 completed"
2. `b73bb3e` — "story1.2 created"
3. `ab3f0fb` — "story1.1 completed"
4. `3131ec8` — "story 1.1 created"
5. `847fe5c` — "epics created"

**Commit message style:** lowercase, no conventional prefixes. Expected: `"story1.3 completed"`

**Files changed in story 1.2:** `assets/css/design-tokens.css` (new), `assets/css/main.css` (modified). No overlap with story 1.3 file changes.

**Branch strategy (architecture D3.1):** Create `phase0/1-3-simplify-dark-mode` from `main`.

## Testing Requirements

### Build Validation

```bash
pnpm run build    # Vite + Hugo must succeed
pnpm run test     # Hugo build validation must pass
pnpm run dev:watch  # Dev server must start without errors
```

### Functional Verification

1. **OS dark preference works:** Set OS to dark mode → site shows dark theme → `.dark` class on `<html>`
2. **OS light preference works:** Set OS to light mode → site shows light theme → no `.dark` class on `<html>`
3. **Live OS switch:** Toggle OS dark mode → site updates without page reload (matchMedia listener)
4. **No toggle button:** Nav should not contain any sun/moon icon button
5. **No localStorage:** `localStorage.getItem('color-theme')` should return `null`
6. **No console errors:** DevTools console clean — no "Cannot find element" errors from removed button
7. **dark: utilities work:** Any template using `dark:bg-gray-900` etc. still functions correctly
8. **Prose dark mode works:** If you view a page with `.prose` content in dark mode, headings/text should be light-colored

### What NOT to Test

- CSS token values — unchanged from story 1.2
- Typography — deferred to Epic 2
- Responsive layout — no layout changes in this story
- Build hash changes — expected (JS bundle removed)

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

None — clean implementation, no issues encountered.

### Completion Notes List

- Replaced localStorage + toggle dark mode with OS-preference-only detection (Option A from Dev Notes)
- Head script simplified: detects `prefers-color-scheme: dark` and adds `.dark` class, listens for OS changes
- Deleted `assets/js/darkmode.js` (41 lines of toggle logic)
- Removed toggle button from nav (sun/moon SVG icons)
- Removed darkmode.js script loading from footer
- Removed `darkmode_js` config from hugo.yaml, set `disable_theme_toggle: true`
- CSS unchanged — `@custom-variant dark` and `.dark .prose` styles work as before
- No stale references to darkmode.js in source code (only in docs/planning files)
- `pnpm run build` and `pnpm run test` both pass
- `pnpm run dev` verified — server starts cleanly, no JS errors
- Date: 2026-02-17

#### Code Review Fixes (2026-02-17)
- Added `window.matchMedia &&` defensive guard for older browser compatibility
- Added architectural comment explaining why JS is used instead of pure CSS `@media`
- Clarified AC#2 decision in story (minimal JS chosen over pure CSS)
- Verified and documented `pnpm run dev` test result (was previously undocumented)

### Change Log

- 2026-02-17: Simplified dark mode to OS preference only — removed toggle, localStorage, darkmode.js
- 2026-02-17: Code review fixes — matchMedia guard, architectural comments, AC#2 clarification

### File List

- **Deleted:** `assets/js/darkmode.js`
- **Modified:** `layouts/partials/head.html` (simplified inline script)
- **Modified:** `layouts/partials/nav.html` (removed toggle button block)
- **Modified:** `layouts/partials/footer.html` (removed darkmode.js script block)
- **Modified:** `hugo.yaml` (removed darkmode_js, set disable_theme_toggle: true)
