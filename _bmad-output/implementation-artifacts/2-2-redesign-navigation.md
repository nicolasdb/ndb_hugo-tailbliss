# Story 2.2: Redesign Navigation

Status: ready-for-dev

## Story

As a **portfolio visitor**,
I want a clean navigation bar that matches the portfolio's visual identity,
So that I can navigate between sections easily on any device.

## Acceptance Criteria

1. **Given** the nav uses the default TailBliss design
   **When** the nav is redesigned
   **Then** `layouts/partials/nav.html` matches DESIGN-SPEC §6.2

2. **And** the nav has backdrop blur on scroll (Alpine.js `x-data` for scroll detection)

3. **And** there is no dark mode toggle button in the nav

4. **And** nav links include: Home, Posts, Patterns, Timeline, About — in Hanken Grotesk 14px

5. **And** the nav is responsive (mobile hamburger menu or equivalent at `sm` breakpoint)

6. **And** the nav uses design token colors (not hardcoded values)

7. **And** the nav logo shows "ndb" in Literata 18px weight 500

8. **And** `hugo.yaml` menu config is updated with correct nav items

9. **And** `pnpm run test` passes

## Tasks / Subtasks

- [ ] Task 1: Update hugo.yaml menu configuration (AC: #8)
  - [ ] 1.1 Replace current `menu.main` entries with: Home (`/`), Posts (`/posts/`), Patterns (`/patterns/`), Timeline (`/timeline/`), About (`/about/`)
  - [ ] 1.2 Remove `categories` and `dropdown` menu sections (TailBliss leftovers)
  - [ ] 1.3 Update `menu.footer` entries to match new site structure

- [ ] Task 2: Rewrite nav.html partial (AC: #1, #2, #3, #4, #6, #7)
  - [ ] 2.1 Read current `layouts/partials/nav.html` completely
  - [ ] 2.2 Rewrite to DESIGN-SPEC §6.2 specifications:
    - Sticky top, 54px height
    - Left: "ndb" logo text — `font-heading text-[18px] font-medium`
    - Right: nav links — `font-body text-[14px]`
    - Active link: text-primary, weight 600. Others: text-secondary, weight 400
    - Max-width: 920px centered
  - [ ] 2.3 Add Alpine.js scroll detection for backdrop blur:
    - `x-data="{ scrolled: false }"` on nav element
    - `@scroll.window="scrolled = window.scrollY > 10"`
    - Transparent at top, `backdrop-blur-[10px]` + semi-transparent bg + border on scroll
  - [ ] 2.4 Ensure NO dark mode toggle button exists
  - [ ] 2.5 Use design token colors via Tailwind utilities (not hardcoded hex/oklch)

- [ ] Task 3: Add responsive mobile menu (AC: #5)
  - [ ] 3.1 Add Alpine.js `x-data="{ open: false }"` for mobile menu toggle
  - [ ] 3.2 Add hamburger button visible only below `sm` breakpoint
  - [ ] 3.3 Add mobile menu panel (full-width dropdown or slide-in)
  - [ ] 3.4 Use `x-cloak` on mobile menu to prevent flash
  - [ ] 3.5 Ensure desktop nav links hidden on mobile, shown on `sm:` and above

- [ ] Task 4: Verify build and rendering (AC: #9)
  - [ ] 4.1 Run `pnpm run build` — must succeed
  - [ ] 4.2 Run `pnpm run test` — Hugo build validation must pass
  - [ ] 4.3 Verify nav renders correctly at desktop and mobile widths

## Dev Notes

### DESIGN-SPEC §6.2 — Navigation Component

- Sticky top, 54px height
- Background: transparent at top, blurs (`backdrop-filter: blur(10px)`) + semi-transparent + border on scroll
- Left: "ndb" in Literata 18px weight 500
- Right: Posts, Patterns, Timeline, About — Hanken Grotesk 14px
- Active link: text-primary, weight 600. Others: text-secondary, weight 400
- Max-width: 920px centered

### Alpine.js Scroll Detection Pattern

```html
<nav x-data="{ scrolled: false, open: false }"
     @scroll.window="scrolled = window.scrollY > 10"
     :class="scrolled ? 'bg-[var(--surface-elevated)]/80 backdrop-blur-[10px] border-b border-[var(--border)]' : 'bg-transparent'"
     class="fixed top-0 w-full z-50 transition-none">
```

Per DESIGN-SPEC §9: No CSS transitions. The blur/background change should be instant (no `transition-*` classes). Use `transition-none` explicitly.

### Current Nav State

The existing `nav.html` is a TailBliss default with:
- Complex dropdown menus (categories, complex dropdown)
- Dark mode toggle button (should already be removed from Story 1.3/1.5)
- TailBliss logo/branding references
- Alpine.js `x-data` for mobile menu (can be adapted)

**Rewrite entirely** — the TailBliss nav structure is too different from DESIGN-SPEC to patch.

### Hugo Menu Template Pattern

```go-html-template
{{/* Nav links from hugo.yaml menu.main */}}
{{ range .Site.Menus.main }}
  <a href="{{ .URL }}"
     class="font-body text-[14px] {{ if $.IsMenuCurrent "main" . }}text-[var(--text-primary)] font-semibold{{ else }}text-[var(--text-secondary)] font-normal hover:text-[var(--text-primary)]{{ end }}">
    {{ .Name }}
  </a>
{{ end }}
```

### Previous Story Dependencies

- **Story 2.1** adds Google Fonts and `font-heading`/`font-body`/`font-mono` Tailwind utilities
- This story DEPENDS on 2.1 being complete for font classes to work
- If 2.1 is not done, font classes will fall back to system fonts (graceful degradation)

### Design Token Color References

Use CSS custom property references via Tailwind arbitrary values:
- `text-[var(--text-primary)]` — primary text
- `text-[var(--text-secondary)]` — secondary text (nav links)
- `bg-[var(--surface-elevated)]` — elevated surface (blurred nav bg)
- `border-[var(--border)]` — border color

Alternatively, if tokens are mapped in `@theme`, use the Tailwind utility names directly.

### What NOT to Do

- ❌ Do NOT keep TailBliss dropdown menus — replace entirely
- ❌ Do NOT add CSS transitions/animations — DESIGN-SPEC §9 forbids them
- ❌ Do NOT use hardcoded color values — use design token references
- ❌ Do NOT add a dark mode toggle
- ❌ Do NOT create a separate JS file for nav — use inline Alpine.js `x-data`
- ❌ Do NOT use `partials/components/nav/` subdirectory — flat partial naming

### Scope Boundary

This story redesigns `nav.html` and updates `hugo.yaml` menus. It does NOT:
- Create hero partial (Story 2.3)
- Redesign footer (Story 2.4)
- Create any new content sections or pages
- Build the homepage layout

### Project Structure Notes

**Files to modify:**
- `layouts/partials/nav.html` — Complete rewrite to DESIGN-SPEC
- `hugo.yaml` — Update menu.main and menu.footer entries

**Files NOT to modify:**
- `layouts/_default/baseof.html` — Already calls nav partial
- `assets/css/main.css` — No CSS changes needed (Tailwind utilities only)

### References

- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#Component: Navigation]
- [Source: _bmad-output/planning-artifacts/architecture.md#D2.3: Alpine.js Scope]
- [Source: _bmad-output/planning-artifacts/architecture.md#Naming Patterns]
- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.2]

### Branch Strategy

Per architecture D3.1: Create branch `phase0/2-2-redesign-navigation` from `main`.

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
