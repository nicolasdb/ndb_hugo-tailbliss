# Story 1.1: Remove TailBliss Defaults & Sample Content

Status: ready-for-dev

## Story

As a **portfolio owner**,
I want all generic TailBliss sample content and branding removed,
So that I have a clean slate to build my own visual identity.

## Acceptance Criteria

1. **Given** the repository contains TailBliss sample content **When** the cleanup is completed **Then** all 14 sample posts are removed (`blog-post-{1-7}.md`, `news-post-{1-7}.md`)
2. **And** unused partials are removed (`newsletter.html`, `posts-template.html`, `post-tile.html`)
3. **And** TailBliss branding images are removed (5 `tailbliss-*.png/svg/webp` files, `sample-logo.svg`)
4. **And** TailBliss sample images are removed (`tailbliss-rocket-indigo.png`, `hacktoberfest.jpg`)
5. **And** unused platform configs are removed (`Dockerfile`, `nginx.conf`, `nixpacks.toml`, `cloudflare.md`, `theme.toml`)
6. **And** `content/prose.md` is reviewed and removed if not needed
7. **And** `assets/css/style.css` is audited — useful content merged into `main.css` or file removed entirely
8. **And** `pnpm run test` passes with the cleaned codebase (Hugo builds successfully)

## Tasks / Subtasks

- [ ] Task 1: Remove 14 sample posts (AC: #1)
  - [ ] Delete `content/posts/blog-post-{1-7}.md` (7 files)
  - [ ] Delete `content/posts/news-post-{1-7}.md` (7 files)
- [ ] Task 2: Remove unused partials (AC: #2)
  - [ ] Delete `layouts/partials/newsletter.html`
  - [ ] Delete `layouts/partials/posts-template.html`
  - [ ] Delete `layouts/partials/post-tile.html`
  - [ ] Remove `{{ partial "newsletter.html" . }}` call from `layouts/index.html:210`
  - [ ] Remove `{{ partial "post-tile.html" . }}` call from `layouts/index.html:201`
  - [ ] Remove `{{ partial "posts-template.html" . }}` call from `layouts/_default/list.html:16`
- [ ] Task 3: Remove TailBliss branding images (AC: #3, #4)
  - [ ] Delete `assets/images/pages/tailbliss-rocket-indigo.png`
  - [ ] Delete `assets/images/pages/hacktoberfest.jpg`
  - [ ] Delete `assets/images/global/sample-logo.svg`
  - [ ] Delete `static/images/sample-logo.svg`
  - [ ] Delete `static/images/tailbliss-cover.png`
  - [ ] Delete `static/images/tailbliss-full-blue.png`
  - [ ] Delete `static/images/tailbliss-full-blue.svg`
  - [ ] Delete `static/images/tailbliss-rocket-indigo.webp`
  - [ ] Delete `static/images/tailbliss-white.svg`
  - [ ] Delete `images/logo-tailbliss-round.svg` (root `images/` dir)
  - [ ] Delete `images/tailbliss-lighthouse-11-03-22.png` (root `images/` dir)
  - [ ] Remove hero image references from `layouts/index.html:20` and `layouts/partials/head.html:23`
  - [ ] Remove sample-logo fallback from `layouts/index.html:154` (sponsors section)
- [ ] Task 4: Remove unused platform configs (AC: #5)
  - [ ] Delete `Dockerfile`
  - [ ] Delete `nginx.conf`
  - [ ] Delete `nixpacks.toml`
  - [ ] Delete `cloudflare.md`
  - [ ] Delete `theme.toml`
- [ ] Task 5: Review and remove `content/prose.md` (AC: #6)
  - [ ] Confirm prose.md is TailBliss typography demo (375+ lines of lorem ipsum) — remove it
  - [ ] Remove `assets/images/featured/featured-img-placeholder.png` referenced by prose.md
- [ ] Task 6: Audit `assets/css/style.css` (AC: #7)
  - [ ] Read style.css (large file ~50k tokens) — identify anything NOT covered by TailwindCSS 4.1 + main.css
  - [ ] If useful styles exist: merge into `assets/css/main.css` under custom utilities section
  - [ ] If no useful content: delete entirely
  - [ ] Verify no templates reference style.css directly (check `<link>` tags, `@import` statements)
- [ ] Task 7: Clean up template references to removed files (AC: #8)
  - [ ] Update `layouts/index.html` — remove/stub sections that referenced deleted content (hero image, blog section with post-tile, newsletter, sponsors with sample-logo)
  - [ ] Update `layouts/_default/list.html` — remove posts-template partial call
  - [ ] Update `layouts/partials/head.html` — remove hero image preload
  - [ ] Grep codebase for "tailbliss", "sample-logo", "hacktoberfest", "newsletter" — fix any remaining references
- [ ] Task 8: Verify Hugo build passes (AC: #8)
  - [ ] Run `pnpm run test` — must pass
  - [ ] Run `pnpm run dev` — site loads without errors in browser
  - [ ] Confirm no broken image references or missing partial errors in console

## Dev Notes

### Critical: What NOT to Do

- **Do NOT delete `netlify.toml`** — this is the active deployment config, not a TailBliss artifact
- **Do NOT delete `tailwind.config.js`** — active build config
- **Do NOT delete `vite.config.mjs`** — active CSS build pipeline
- **Do NOT delete `postcss.config.js`** — active build config
- **Do NOT delete `layouts/partials/pagination.html`** — this is reused in future stories
- **Do NOT delete `layouts/shortcodes/imgc.html`** — active image shortcode, used in future stories
- **Do NOT delete `assets/js/darkmode.js`** — modified in Story 1.3, not removed here
- **Do NOT modify `assets/css/main.css`** beyond style.css audit merge — design tokens are Story 1.2
- **Do NOT create new content** — this story is deletion only. New content comes in later epics
- **Do NOT restructure layouts** — only remove references to deleted files. Full layout redesign is Epic 2

### Template Cleanup Strategy

When removing partial calls from templates, use **minimal stubs** rather than deleting entire template sections. The homepage (`layouts/index.html`) currently has sections for hero, blog posts, sponsors, newsletter — after cleanup:

- **Hero section**: Remove the TailBliss hero image (`tailbliss-rocket-indigo.png`) reference. Leave the section structure with a placeholder comment `{{/* Hero section — redesigned in Story 2.3 */}}`
- **Blog section**: Remove the `{{ range (.Paginator 3).Pages }}` + `post-tile.html` block. Leave a comment `{{/* Posts section — rebuilt in Epic 3 */}}`
- **Sponsors section**: Remove entirely (TailBliss feature, not in DESIGN-SPEC)
- **Newsletter section**: Remove entirely (TailBliss feature, not in DESIGN-SPEC)

For `layouts/_default/list.html`: Replace the `posts-template.html` call with a simple range loop or comment stub. This page will be rebuilt in Story 3.4.

### style.css Audit Guide

The file is large. Focus on:
1. **Custom prose styles** — main.css already has custom prose styles. If style.css has different prose rules, compare and keep the better version in main.css
2. **Component styles** — anything that's pure Tailwind utility composition can be removed (Tailwind 4.1 handles this)
3. **Reset/normalize** — Tailwind includes its own reset. Remove duplicates
4. **Animation/transition** — DESIGN-SPEC says "no animation". Remove all transition/animation rules
5. **Dark mode** — check if style.css has dark mode rules that conflict with or supplement main.css

**Decision framework**: If in doubt, **delete it**. TailwindCSS 4.1 + main.css is the source of truth. Anything in style.css that's genuinely needed can be re-added later with proper design token integration (Story 1.2).

### Build Pipeline

```
Vite (assets/css/main.css) → TailwindCSS 4.1 → static/css/
Hugo reads static/css/ → compiles templates → public/
```

- CSS pipeline: Vite, NOT Hugo Pipes. Never use `resources.PostCSS` or Hugo Pipes for CSS
- Build: `pnpm run build` (production), `pnpm run dev:watch` (development)
- Test: `pnpm run test` (Hugo build validation)

### Branch Strategy

Per architecture D3.1: Create branch `phase0/1-1-remove-tailbliss-defaults` from `main`. Merge to `main` when complete.

### Project Structure Notes

- All files to remove are in the main repo (not submodules or theme directories)
- The `images/` directory at project root contains TailBliss logos — this is separate from `assets/images/` and `static/images/`
- After cleanup, `content/posts/` directory will be empty (no posts). This is expected — posts are created in Epic 3
- `static/images/` will still contain `site-logo.svg` and `og-images/` — these are replaced in Story 5.1, not removed here

### References

- [Source: _bmad-output/planning-artifacts/architecture.md § File Removal List]
- [Source: _bmad-output/planning-artifacts/architecture.md § Build Pipeline Clarification]
- [Source: _bmad-output/planning-artifacts/architecture.md § Implementation Patterns]
- [Source: _bmad-output/planning-artifacts/epics.md § Story 1.1]
- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md § 10 Design Principles]

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
