# Story 2.1: Update Base Layout & Typography

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **portfolio visitor**,
I want the site to use the correct fonts and semantic HTML structure,
So that the portfolio feels polished and professional from any page.

## Acceptance Criteria

1. **Given** the base layout uses default TailBliss structure
   **When** the base layout is updated
   **Then** `layouts/_default/baseof.html` loads Google Fonts per DESIGN-SPEC §2 (Literata, Hanken Grotesk, Commit Mono)

2. **And** the HTML structure uses semantic elements (`nav`, `main`, `article`, `section`, `footer`)

3. **And** the `<html>` element has class `light` applied (via head.html script, already done in Story 1.5)

4. **And** `layouts/partials/head.html` includes Google Fonts preload/preconnect links for performance

5. **And** the page renders correctly with the new font stack (Literata for headings, Hanken Grotesk for body, Commit Mono for metadata)

6. **And** design token font custom properties are wired in `assets/css/main.css` `@theme` block

7. **And** `pnpm run test` passes

## Tasks / Subtasks

- [ ] Task 1: Add Google Fonts to head.html (AC: #1, #4)
  - [ ] 1.1 Add `preconnect` links for `fonts.googleapis.com` and `fonts.gstatic.com`
  - [ ] 1.2 Add `<link>` for Google Fonts stylesheet loading all three families:
    - `Literata:opsz,wght@7..72,400;7..72,500;7..72,700`
    - `Hanken+Grotesk:wght@400;500;600;700`
    - `Commit+Mono:wght@400;700`
  - [ ] 1.3 Use `display=swap` parameter for font-display optimization

- [ ] Task 2: Wire font tokens in CSS @theme block (AC: #6)
  - [ ] 2.1 Read `assets/css/main.css` completely
  - [ ] 2.2 In the `@theme` block, add font-family mappings:
    - `--font-heading: 'Literata', Georgia, 'Times New Roman', serif;`
    - `--font-body: 'Hanken Grotesk', system-ui, sans-serif;`
    - `--font-mono: 'Commit Mono', 'JetBrains Mono', 'Fira Code', monospace;`
  - [ ] 2.3 Verify these match DESIGN-SPEC §2 font stack and fallbacks

- [ ] Task 3: Update baseof.html semantic structure (AC: #1, #2)
  - [ ] 3.1 Read `layouts/_default/baseof.html` completely
  - [ ] 3.2 Ensure `<body>` uses design token background color instead of hardcoded `bg-gray-50`
  - [ ] 3.3 Verify `<body>` contains semantic structure: `nav` (via partial), `<main>` (via block), `footer` (via partial)
  - [ ] 3.4 Apply `font-body` as the default body font class
  - [ ] 3.5 Ensure the `<html>` element supports `.light` class (already in head.html script from 1.5)

- [ ] Task 4: Update existing templates with font utilities (AC: #5)
  - [ ] 4.1 In `layouts/index.html`, replace `text-4xl font-extrabold` hero heading with `font-heading` class + appropriate Tailwind size
  - [ ] 4.2 Ensure body text areas use `font-body` (may be inherited from body element)
  - [ ] 4.3 Ensure any mono/metadata text uses `font-mono` where applicable
  - [ ] 4.4 Do NOT redesign the homepage layout — only apply font utilities to existing elements

- [ ] Task 5: Verify build and rendering (AC: #7)
  - [ ] 5.1 Run `pnpm run build` — must succeed
  - [ ] 5.2 Run `pnpm run test` — Hugo build validation must pass
  - [ ] 5.3 Start `pnpm run dev:watch` and verify fonts load correctly in browser

## Dev Notes

### Architecture Context

**DESIGN-SPEC §2 — Typography "The Archivist":**

| Role | Font | Weights | Fallback |
|------|------|---------|----------|
| Heading | Literata | 400, 500, 700 | Georgia, Times New Roman, serif |
| Body | Hanken Grotesk | 400, 500, 600, 700 | system-ui, sans-serif |
| Mono | Commit Mono | 400, 700 | JetBrains Mono, Fira Code, monospace |

**Portfolio type scale (DESIGN-SPEC §2):**
- Display: 40px (hero name)
- H1: 28px, H2: 22px, H3: 20px, H4: 17px
- Body: 15px (line-height 1.7)
- Small: 13px, Meta: 12px, Micro: 11px, Nano: 10px

**Font role mapping relevant to this story:**
- Name/hero title: Heading 700
- Body paragraphs: Body 400
- Section labels: Mono 400 uppercase
- Nav logo "ndb": Heading 500
- Nav links: Body 400-600

### Current State (from Epic 1)

**baseof.html** (14 lines): Minimal — `<html>`, `<head>`, `<body class="bg-gray-50">`, nav partial, main block, footer partial. No Google Fonts, no font classes.

**head.html** (60 lines): Has favicon links, Alpine.js preload, x-cloak style, light class script, CSS loading logic. No Google Fonts preconnect/preload.

**main.css**: Already has `@import "./design-tokens.css"` and `@import "tailwindcss"` with `@theme` block. Need to add font-family custom properties in `@theme`.

**design-tokens.css**: Contains `--font-heading`, `--font-body`, `--font-mono` token definitions. These need to be referenced in main.css `@theme` block to make Tailwind utility classes work (e.g., `font-heading`, `font-body`, `font-mono`).

### What Changed in Epic 1 (Previous Story Intelligence)

**Story 1.5 (most recent):**
- Removed all dark mode CSS and detection code
- `head.html` now has simple `document.documentElement.classList.add('light')`
- Removed `@custom-variant dark`, `.dark .prose` styles from main.css
- Removed `dark:` utility classes from 7 template files
- **Clean light-theme-only foundation** is ready

**Key learnings from Epic 1:**
- Vite pipeline works: `assets/css/main.css` → Vite → `static/css/` → Hugo reads
- Never use Hugo Pipes for CSS
- `pnpm run build` and `pnpm run test` are the validation gates

### CSS Integration — How Font Tokens Work

**design-tokens.css** defines the raw values:
```css
.light {
  --font-heading: 'Literata', Georgia, 'Times New Roman', serif;
  --font-body: 'Hanken Grotesk', system-ui, sans-serif;
  --font-mono: 'Commit Mono', 'JetBrains Mono', 'Fira Code', monospace;
}
```

**main.css @theme block** must map these to Tailwind:
```css
@theme {
  --font-heading: var(--font-heading);
  --font-body: var(--font-body);
  --font-mono: var(--font-mono);
}
```

This enables Tailwind utility classes: `font-heading`, `font-body`, `font-mono`.

**IMPORTANT:** Check if `@theme` already has font overrides. If `--font-sans` or `--font-serif` are set, decide whether to override them or add new ones. The convention is to use our semantic names (`font-heading`, `font-body`, `font-mono`) not Tailwind defaults.

### Google Fonts URL

Single combined URL for all three families:
```
https://fonts.googleapis.com/css2?family=Commit+Mono:wght@400;700&family=Hanken+Grotesk:wght@400;500;600;700&family=Literata:opsz,wght@7..72,400;7..72,500;7..72,700&display=swap
```

### What NOT to Do

- ❌ Do NOT redesign the nav, footer, or homepage layout — that's Stories 2.2, 2.3, 2.4
- ❌ Do NOT use Hugo Pipes for CSS — Vite handles the CSS pipeline
- ❌ Do NOT add `@font-face` declarations — Google Fonts handles this via `<link>`
- ❌ Do NOT remove existing `bg-gray-50` from body unless replacing with token equivalent
- ❌ Do NOT touch `assets/css/design-tokens.css` — it's the shared spec (read-only)
- ❌ Do NOT add dark-mode font overrides — portfolio is light-only

### Scope Boundary

This story establishes the font infrastructure. It does NOT:
- Redesign any components (nav, footer, hero — those are 2.2, 2.3, 2.4)
- Create new partials
- Create new content files
- Change the homepage layout

It DOES:
- Load Google Fonts in `<head>`
- Wire font tokens into Tailwind via `@theme`
- Apply `font-body` as default body font
- Apply `font-heading` to existing heading elements
- Ensure semantic HTML structure in baseof.html

### Project Structure Notes

**Files to modify:**
- `layouts/partials/head.html` — Add Google Fonts preconnect + stylesheet link
- `assets/css/main.css` — Add font-family mappings in `@theme` block
- `layouts/_default/baseof.html` — Add body font class, verify semantic structure

**Files to verify (no changes):**
- `assets/css/design-tokens.css` — Should already have font token definitions
- `layouts/index.html` — Optionally apply font utilities to existing elements

### References

- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#2. Typography]
- [Source: _bmad-output/planning-artifacts/architecture.md#D2.1: CSS Integration]
- [Source: _bmad-output/planning-artifacts/architecture.md#Naming Patterns]
- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.1]
- [Source: _bmad-output/implementation-artifacts/1-5-finalize-portfolio-light-theme-only.md]

### Branch Strategy

Per architecture D3.1: Create branch `phase0/2-1-update-base-layout-typography` from `main`.

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
