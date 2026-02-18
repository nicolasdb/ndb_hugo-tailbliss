# Story 2.4: Redesign Footer

Status: ready-for-dev

## Story

As a **portfolio visitor**,
I want a clean footer with relevant links and attribution,
So that I can find additional information and the site feels complete.

## Acceptance Criteria

1. **Given** the footer uses the default TailBliss design
   **When** the footer is redesigned
   **Then** `layouts/partials/footer.html` matches DESIGN-SPEC §6.7

2. **And** the footer uses design token colors

3. **And** the footer includes relevant links (GitHub, contact, etc.)

4. **And** the footer includes license attribution (Apache 2.0 theme, CC-BY 4.0 content)

5. **And** the footer is responsive

6. **And** `pnpm run test` passes

## Tasks / Subtasks

- [ ] Task 1: Rewrite footer.html partial (AC: #1, #2, #3, #4, #5)
  - [ ] 1.1 Read current `layouts/partials/footer.html` completely
  - [ ] 1.2 Rewrite to DESIGN-SPEC §6.7:
    - **Top row:** Name (Literata 15px, 500) + Location (Hanken Grotesk 13px, text-tertiary) left, links (Commit Mono 12px, text-tertiary) right
    - **Trust line:** Commit Mono 11px, frontier color — "Patterns are extracted from documented work, not self-assessment. Confidence reflects evidence density. Built with Hugo, powered by curiosity."
  - [ ] 1.3 Max-width: 920px centered (same as nav and page content)
  - [ ] 1.4 Use design token colors via Tailwind arbitrary values or mapped utilities
  - [ ] 1.5 Add license attribution: Apache 2.0 (theme) + CC-BY 4.0 (content)
  - [ ] 1.6 Links: read from `hugo.yaml` `menu.footer` OR hardcode minimal set (GitHub, About, Contact)
  - [ ] 1.7 Responsive: stack to single column on mobile, flex row on `sm:` and above

- [ ] Task 2: Update hugo.yaml footer menu if needed (AC: #3)
  - [ ] 2.1 If using menu-driven footer links, update `menu.footer` in hugo.yaml:
    - GitHub (external link)
    - About (`/about/`)
    - Contact (`/contact/`)
  - [ ] 2.2 Remove TailBliss blog/news category links from footer menu

- [ ] Task 3: Verify build and rendering (AC: #6)
  - [ ] 3.1 Run `pnpm run build` — must succeed
  - [ ] 3.2 Run `pnpm run test` — Hugo build validation must pass
  - [ ] 3.3 Verify footer renders correctly on desktop and mobile

## Dev Notes

### DESIGN-SPEC §6.7 — Footer Component

- **Top row:** Name (Literata 15px, 500) + Location (Hanken Grotesk 13px, text-tertiary) left, links (Commit Mono 12px, text-tertiary) right
- **Trust line:** Commit Mono 11px, frontier color — "Patterns are extracted from documented work, not self-assessment. Confidence reflects evidence density. Built with Hugo, powered by curiosity."

### Footer Implementation

```html
<footer class="w-full border-t border-[var(--border)]">
  <div class="mx-auto max-w-[920px] px-8 py-8">
    {{/* Top row */}}
    <div class="flex flex-col sm:flex-row sm:justify-between sm:items-baseline gap-4">
      <div>
        <span class="font-heading text-[15px] font-medium">Nicolas de Barquin</span>
        <span class="font-body text-[13px] text-[var(--text-tertiary)] ml-2">Brussels, Belgium</span>
      </div>
      <div class="flex gap-4">
        {{/* Footer links */}}
      </div>
    </div>
    {{/* Trust line */}}
    <p class="font-mono text-[11px] text-[var(--frontier)] mt-6 max-w-[65ch]">
      Patterns are extracted from documented work, not self-assessment.
      Confidence reflects evidence density. Built with Hugo, powered by curiosity.
    </p>
  </div>
</footer>
```

### Design Rules (DESIGN-SPEC §10)

- No gradients, no shadows, no decoration
- Color is information, not ornamentation
- Typography-forward — the writing IS the showcase
- Confidence as social contract — the trust line reinforces this

### Current Footer State

The existing `footer.html` is TailBliss default with:
- Newsletter signup section
- Social media links (all disabled)
- Complex multi-column layout
- TailBliss branding

**Rewrite entirely** — simpler is better per DESIGN-SPEC.

### What NOT to Do

- ❌ Do NOT keep TailBliss newsletter section
- ❌ Do NOT keep social media icon row
- ❌ Do NOT add shadows or gradients
- ❌ Do NOT use custom CSS for layout — Tailwind utilities only
- ❌ Do NOT add dark mode styles — light theme only

### Scope Boundary

This is the final story in Epic 2. After completion:
- The portfolio has a complete shell: nav, hero, footer
- All pages share consistent typography and structure
- Tag milestone: discussed in retrospective (Epic 2 complete)

### Previous Story Dependencies

- **Story 2.1**: Font utilities must be available
- **Story 2.2**: Nav should be complete for visual coherence
- **Story 2.3**: Hero and homepage structure should be in place

### Project Structure Notes

**Files to modify:**
- `layouts/partials/footer.html` — Complete rewrite to DESIGN-SPEC
- `hugo.yaml` — Update `menu.footer` entries (if using menu-driven links)

**Files NOT to modify:**
- `layouts/_default/baseof.html` — Already calls footer partial
- `assets/css/main.css` — No CSS changes (Tailwind utilities only)

### References

- [Source: _bmad-output/implementation-artifacts/DESIGN-SPEC.md#Component: Footer]
- [Source: _bmad-output/planning-artifacts/architecture.md#Naming Patterns]
- [Source: _bmad-output/planning-artifacts/epics.md#Story 2.4]

### Branch Strategy

Per architecture D3.1: Create branch `phase0/2-4-redesign-footer` from `main`.

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
