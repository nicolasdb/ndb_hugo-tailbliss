# Story 2.3: Build Homepage Hero & Content Structure

Status: ready-for-dev

## Story

As a **portfolio visitor**,
I want to see Nicolas's identity and tagline when landing on the site,
So that I immediately understand what this portfolio is about.

## Acceptance Criteria

1. **Given** the homepage shows generic TailBliss content
   **When** the hero and homepage are rebuilt
   **Then** `content/_index.md` exists with hero content in frontmatter (tagline, description)

2. **And** `layouts/partials/hero.html` renders hero content from `.Params.hero` (no hardcoded strings)

3. **And** `layouts/index.html` uses the hero partial and reads from `content/_index.md`

4. **And** `layouts/partials/section-heading.html` exists (mono uppercase section label, reusable)

5. **And** the homepage has empty placeholder sections for "Latest Posts", "Patterns", and "Timeline" (wired in Epic 4)

6. **And** the homepage is responsive across `sm`/`md`/`lg` breakpoints

7. **And** `pnpm run test` passes

## Tasks / Subtasks

- [ ] Task 1: Create content/_index.md with hero frontmatter (AC: #1)
  - [ ] 1.1 Create `content/_index.md` with YAML frontmatter:
    ```yaml
    title: "Nicolas de Barquin"
    description: "Portfolio & Skills Repository"
    hero:
      tagline: "Building a living knowledge graph from documented work"
      description: "Capturing knowledge pearls, discovering skill patterns, and making the invisible visible — one evidence trail at a time."
      stats:
        pearls: 0
        projects: 0
        patterns: 0
    ```
  - [ ] 1.2 Content can be placeholder — Nicolas will refine text later

- [ ] Task 2: Create hero.html partial (AC: #2)
  - [ ] 2.1 Create `layouts/partials/hero.html` matching DESIGN-SPEC §6 Hero:
    - Name: `font-heading text-[40px] font-bold tracking-[-0.02em]` max 18ch
    - Description: `font-body text-[17px] leading-[1.7] text-[var(--text-secondary)]` max 54ch
    - Stats: `font-mono text-[12px] text-[var(--text-tertiary)]` with `·` separators
    - Freshness heartbeat: `font-mono text-[11px] text-[var(--frontier)]` — static placeholder for Phase 0
  - [ ] 2.2 Read all content from `.Params.hero` — no hardcoded strings
  - [ ] 2.3 Use `{{ with }}` guards for optional fields (stats, freshness)
  - [ ] 2.4 Page max-width: 920px centered with 32px horizontal padding

- [ ] Task 3: Create section-heading.html partial (AC: #4)
  - [ ] 3.1 Create `layouts/partials/section-heading.html`
  - [ ] 3.2 Implement DESIGN-SPEC §6 Section Heading:
    - `font-mono text-[12px] font-normal uppercase tracking-[0.08em] text-[var(--text-tertiary)]`
    - Optional right-aligned "All posts →" link (same style)
    - Flex row, space-between
  - [ ] 3.3 Interface: `{{ partial "section-heading.html" (dict "label" "LATEST POSTS" "link" "/posts/" "linkText" "All posts →") }}`
  - [ ] 3.4 `link` and `linkText` are optional — guard with `{{ with }}`

- [ ] Task 4: Rebuild layouts/index.html (AC: #3, #5, #6)
  - [ ] 4.1 Remove all TailBliss P1/P2/P3 section content
  - [ ] 4.2 Add hero partial call: `{{ partial "hero.html" . }}`
  - [ ] 4.3 Add empty placeholder sections with section-heading partial:
    - "LATEST POSTS" — `<!-- Wired in Story 4.4 -->`
    - "SKILL PATTERNS" — `<!-- Wired in Story 4.4 -->`
    - "MOMENTS OF RECOGNITION" — `<!-- Wired in Story 4.4 -->`
  - [ ] 4.4 Each section: 920px max-width centered, semantic `<section>` element
  - [ ] 4.5 Section padding-y: 44px per DESIGN-SPEC §4
  - [ ] 4.6 Responsive: mobile-first, test at `sm`/`md`/`lg` breakpoints

- [ ] Task 5: Clean up hugo.yaml params (AC: #1)
  - [ ] 5.1 Remove TailBliss `p1`, `p2`, `p3`, `p4`, `p5` param blocks from `hugo.yaml`
  - [ ] 5.2 Remove `social_media` block if not used
  - [ ] 5.3 Keep `author`, `authorimage`, `description`, `disable_theme_toggle`

- [ ] Task 6: Verify build and rendering (AC: #7)
  - [ ] 6.1 Run `pnpm run build` — must succeed
  - [ ] 6.2 Run `pnpm run test` — Hugo build validation must pass
  - [ ] 6.3 Verify hero renders with correct fonts and spacing
  - [ ] 6.4 Verify placeholder sections show headings with no errors

## Dev Notes

### DESIGN-SPEC §6 — Hero Component

- Name: Literata 40px, weight 700, letter-spacing -0.02em, max 18ch
- Description: Hanken Grotesk 17px, line-height 1.7, text-secondary, max 54ch
- Stats: Commit Mono 12px, text-tertiary, flex with `·` separators
- Freshness heartbeat: Commit Mono 11px, frontier color, 8px below stats
  - Format: `last pearl captured {time} ago`
  - Phase 0: static placeholder (e.g., "last pearl captured — ago" or omit entirely)

### Homepage Architecture Pattern

```
content/_index.md → .Params.hero → hero.html partial → rendered in index.html
```

**Critical:** `layouts/index.html` reads `.Params` from `content/_index.md`. No hardcoded strings in templates. This makes content editable without touching templates.

### DESIGN-SPEC §6 — Section Heading Component

- Commit Mono 12px, weight 400, uppercase, letter-spacing 0.08em, text-tertiary
- Optional right-aligned link (same style)
- Flex row, space-between

### Current hugo.yaml Cleanup

The `params` section still has TailBliss placeholder content:
- `p1` through `p5`: lorem ipsum sections with images — **remove all**
- `social_media`: all disabled — **remove**
- `moto`: keep (used as site subtitle)

### Spacing Reference (DESIGN-SPEC §4)

- Page max-width: 920px
- Page padding-x: 32px (portfolio)
- Section padding-y: 44px
- Card padding: 24px

### Partial Interface Conventions (Architecture Doc)

- Partials needing context beyond `.` receive a `dict`:
  ```go-html-template
  {{ partial "section-heading.html" (dict "label" "LATEST POSTS" "link" "/posts/" "linkText" "All posts →") }}
  ```
- Use `"page"` as dict key for page context when needed (not `"context"`)
- Hero partial receives `.` directly since it reads `.Params.hero`

### What NOT to Do

- ❌ Do NOT hardcode hero text in the template — read from `content/_index.md`
- ❌ Do NOT wire homepage content sections to actual content queries — that's Story 4.4
- ❌ Do NOT create post-list-item, pattern-card, or timeline-moment partials — those are Epic 3/4
- ❌ Do NOT add the freshness heartbeat as a live API call — Phase 0 is static
- ❌ Do NOT use custom CSS for spacing/layout — Tailwind utilities only
- ❌ Do NOT keep any TailBliss P1-P5 section templates or params

### Scope Boundary

This story builds the hero and homepage shell. It does NOT:
- Create post, pattern, or timeline partials (Epics 3-4)
- Wire homepage sections to real content queries (Story 4.4)
- Redesign nav (Story 2.2) or footer (Story 2.4)
- Add real content beyond placeholder frontmatter

### Previous Story Dependencies

- **Story 2.1**: Font utilities (`font-heading`, `font-body`, `font-mono`) must be available
- **Story 2.2**: Nav redesign should be complete for visual coherence, but hero works independently

### Project Structure Notes

**Files to create:**
- `content/_index.md` — Homepage content with hero frontmatter
- `layouts/partials/hero.html` — Hero section partial
- `layouts/partials/section-heading.html` — Reusable section label

**Files to modify:**
- `layouts/index.html` — Replace TailBliss content with hero + placeholder sections
- `hugo.yaml` — Remove TailBliss p1-p5 params

**Files NOT to modify:**
- `layouts/_default/baseof.html` — Already wraps index.html
- `layouts/partials/nav.html` — Story 2.2 handles this
- `layouts/partials/footer.html` — Story 2.4 handles this

### References

- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#Component: Hero]
- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#Component: Section Heading]
- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#Page: Home]
- [Source: _bmad-output/planning-artifacts/architecture.md#Homepage Content Pattern]
- [Source: _bmad-output/planning-artifacts/architecture.md#Partial Interface Pattern]
- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.3]

### Branch Strategy

Per architecture D3.1: Create branch `phase0/2-3-build-homepage-hero-content-structure` from `main`.

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
