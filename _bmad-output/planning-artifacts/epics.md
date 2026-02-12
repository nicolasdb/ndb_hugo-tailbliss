---
stepsCompleted: ["step-01", "step-02", "step-03", "step-04"]
status: complete
completedAt: '2026-02-12'
totalEpics: 5
totalStories: 23
inputDocuments:
  - '_bmad-output/01-prd-skill-discovery-system.md'
  - '_bmad-output/planning-artifacts/architecture.md'
  - '_bmad-output/planning-artifacts/ux-design-specification.md'
  - '_bmad-output/implementation-artifacts/DESIGN-SPEC.md'
  - '_bmad-output/implementation-artifacts/sprint-status.yaml'
---

# ndb_hugo-tailbliss - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for ndb_hugo-tailbliss, decomposing the requirements from the PRD, UX Design, and Architecture into implementable stories.

## Requirements Inventory

### Functional Requirements

FR-1: The system must capture and store work outcomes with minimum friction — multi-format input (CLI, markdown, API), validation (date, skills, success metrics), proof-of-work linking (git commit, URL), automatic timestamping, versioning
FR-2: The system must allow creation and retrieval of memory pearls — timestamp + content + optional tags + source, persist without requiring completion, search & browse, surface relevant pearls by current work context, track activation timing (written vs. became relevant)
FR-3: The system must discover skill patterns from outcomes — batch process outcomes to extract skill signals, identify skill clusters through pattern analysis, generate confidence scores, suggest relationships between skills, handle manual validation/refinement
FR-4: The system must store and query a knowledge graph efficiently — nodes (skills, projects, outcomes, ideas), edges (depends on, compounds with, related to), timeline queries, efficient negative space identification
FR-5: The system must calculate and track skill proficiency levels — XP algorithm (frequency x recency x success_metrics), proficiency levels (Novice/Intermediate/Advanced/Expert), compound XP, decay weighting, calculation transparency
FR-6: The system must provide data for visualization — graph export (node/edge lists with attributes), timeline export, frontier export, real-time updates, multiple formats (JSON, CSV, GraphML)
FR-7: The system must connect old ideas to current work — index historical ideas, relevance matching (idea X to project Y), timeline tracking (first mention to relevance), actionable notifications, manual linking

### NonFunctional Requirements

NFR-1: Emergence Design — use graph DB (not relational), LLM-driven categorization (not fixed taxonomies), allow new skill categories without schema changes, support serendipitous surfacing
NFR-2: Continuous Operation — minimal friction in outcome logging, background processing (don't block on LLM analysis), real-time visualization updates, data consistency guarantees
NFR-3: Evidence-Based — every skill claim links to documented outcomes, proof-of-work linking (commits, URLs, dates), transparency (users see evidence), no self-assessment without supporting work
NFR-4: Scalability — sub-500ms graph queries, support 1000+ skills / 10000+ outcomes, memory-efficient pearl storage, efficient negative space identification
NFR-5: Privacy & Control — fine-grained visibility controls (public/private/shared), exclude sensitive outcomes, control public visualization, full export/backup of all data

### Additional Requirements

**From Architecture:**
- Starter template: Hugo + TailBliss (already running, Phase 0 active); betterCallSaul MVP (backoffice, deferred to ndb_backoffice repo)
- D1.1: Post frontmatter schema with core + optional pearl fields; evidence trail as page bundle sidecar (evidence.yaml); graceful degradation via shared pearl-meta.html partial; archetypes for page bundles
- D1.2: Pattern and Timeline as Hugo content sections (content/patterns/, content/timeline/) with dedicated archetypes and list/single layouts
- D1.3: Image handling via existing imgc shortcode for WebP optimization with lazy loading
- D2.1: CSS integration — design tokens in assets/css/design-tokens.css imported before Tailwind in main.css; Vite pipeline (NOT Hugo Pipes); spec reference stays in _bmad-output
- D2.2: Responsive breakpoints — Tailwind defaults (sm:640px, md:768px, lg:1024px, xl:1280px); mobile-first convention; portfolio max-width 920px, post content 700px
- D2.3: Alpine.js scope moderate — dark mode toggle, nav backdrop blur on scroll, evidence trail expand/collapse; no heavy interactivity in portfolio
- D3.1: Branch strategy — phase0/{story-id}-{short-name}, merge each story to main, tag epic milestones (e.g., v0.1.0-phase0-ui)
- D3.2: CI/CD — Netlify only, pnpm run build on push to main; CodeQL for security scanning
- File removal list: 14 sample posts, unused partials (newsletter, posts-template, post-tile), TailBliss branding images (5 files + sample-logo), TailBliss sample images, unused platform configs (Dockerfile, nginx.conf, nixpacks.toml, cloudflare.md, theme.toml); audit style.css
- Build pipeline: Vite processes assets/css/main.css with TailwindCSS 4.1 plugin -> outputs to static/css/ -> Hugo reads; never use Hugo Pipes for CSS
- Implementation patterns: template guards (with/if) for optional pearl fields, partial interfaces (dict vs .), naming conventions (kebab-case partials, camelCase frontmatter), comment conventions (reference DESIGN-SPEC sections), verification checklist per story
- Homepage content pattern: content/_index.md provides hero content via frontmatter, template reads .Params (no hardcoded strings)
- Content file conventions: posts as page bundles, patterns/timeline as leaf files, kebab-case slugs

**From UX:**
- Two-app visual identity: dark office (BackOffice) / light gallery (Portfolio) — distinct visual languages connected by content flow
- Progressive disclosure: mode-aware UI, show only what's needed for current context
- Upload-first capture model: BackOffice receives content, doesn't create it; AI pre-processing with user validation
- Pearl/Necklace/Pattern: three narrative scales (moment/project/skill)
- %Confidence as social contract: trust mechanism + user incentive + transparent scoring
- "Nothing automatic, everything automated": system prepares silently, user initiates all workflows
- Responsive portfolio: must work on mobile (visitors use phones); BackOffice is desktop-only
- Accessibility: semantic HTML (nav, main, article, section, footer), alt text on all images
- Error state quality: honest, specific, calm failure messages — trust through transparency
- Typography-forward design: generous whitespace, content-focused, gallery aesthetic
- Skill patterns as visual cards: subgraph thumbnail + title + evidence count + link to narrative
- Timeline as scrollable narrative: vertical, magazine-style

**From Sprint Status:**
- Phase 0 strategy: frontend-first priority, deploy to Netlify, document progress as posts
- Expected scope: 5-8 stories covering nav, hero, post-list, timeline, post-page, footer
- Each story ships value independently
- Architecture doc already complete (2026-02-11)

### FR Coverage Map

| FR | Epic | Coverage |
|----|------|----------|
| FR-1 | Epic 5 | Structural — content seeding validates capture contract; full implementation deferred to backoffice |
| FR-2 | Epic 3 | Structural — pearl metadata display, evidence trail rendering; pearl creation deferred to backoffice |
| FR-3 | Epic 4 | Structural — pattern content type supports discovered skills; LLM pipeline deferred to backoffice |
| FR-4 | Epic 4 | Structural — content sections model graph data; graph DB deferred to backoffice |
| FR-5 | Epic 3 | Display-only — confidence bar renders from frontmatter; XP calculation deferred to backoffice |
| FR-6 | Epics 2, 4 | Display-only — homepage queries, pattern gallery, timeline; API export deferred to backoffice |
| FR-7 | Epic 4 | Structural — timeline tracks convergence moments; idea matching deferred to backoffice |

**Note:** All FRs have "structural support" in Phase 0 — the content model, templates, and visual components exist. The functional implementation (capture, processing, graph queries) is deferred to `ndb_backoffice`.

## Epic List

### Epic 1: Design Foundation, Clean Slate & Deploy Pipeline
**User outcome:** The portfolio has its own visual identity, TailBliss defaults are gone, design tokens are live, and the site is deployed on Netlify — proving the full build/deploy pipeline from day one.

- Remove TailBliss sample content, branding images, unused partials, unused platform configs
- Audit and merge/remove `style.css`
- Implement design tokens (`assets/css/design-tokens.css`) with OKLCH pearl system colors
- Establish CSS import chain (tokens → Tailwind → theme → utilities), verify Vite build
- Simplify dark mode: remove toggle button, keep OS preference only (`prefers-color-scheme`)
- Connect repo to Netlify, verify `pnpm run build` deploys successfully
- Tag milestone: `v0.1.0-phase0-foundation`

**FRs covered:** Structural foundation for all FRs
**NFRs covered:** NFR-1 (flexible token system), NFR-4 (Netlify CDN), NFR-5 (remove sample content)

### Epic 2: Portfolio Shell & Navigation
**User outcome:** Visitors land on a real homepage with Nicolas's identity, can navigate the site. The site feels like a finished product shell.

- Redesign `nav.html` to DESIGN-SPEC (backdrop blur on scroll, no dark mode toggle)
- Redesign `footer.html` to DESIGN-SPEC
- Update `baseof.html` (Google Fonts, semantic HTML structure)
- Create `hero.html` partial + `content/_index.md` with hero frontmatter
- Build homepage layout (`layouts/index.html`) reading from content, not hardcoded
- Create `section-heading.html` partial (mono uppercase label)

**FRs covered:** FR-6 (structural — homepage sections for future pattern/timeline queries)
**NFRs covered:** NFR-2 (real-time dark mode via OS preference)

### Epic 3: Pearl-Backed Content Publishing
**User outcome:** Nicolas can publish blog posts backed by pearl evidence trails. Visitors see posts with optional pearl metadata, collapsible evidence trail, and confidence indicators. The content contract is live.

- Create post archetypes (directory archetype: `index.md` + `evidence.yaml` sidecar)
- Build `pearl-meta.html` partial (shared renderer with template guards)
- Build `confidence-bar.html` partial
- Build `evidence-trail.html` partial (collapsible, reads from sidecar)
- Build `post-list-item.html` partial
- Build `layouts/posts/list.html` and `layouts/posts/single.html`
- Create at least one real post as page bundle with evidence trail

**FRs covered:** FR-2 (structural display), FR-5 (confidence display)
**NFRs covered:** NFR-3 (evidence-based — every claim links to work), NFR-2 (content pipeline)

### Epic 4: Patterns Gallery & Growth Timeline
**User outcome:** Visitors can explore emerging skill patterns as visual cards and browse a chronological timeline of growth moments. The knowledge-first narrative is visible.

- Create `content/patterns/` section with `_index.md` and archetype
- Build `pattern-card.html` partial (title, confidence, pearl count, timespan)
- Build `layouts/patterns/list.html` and `layouts/patterns/single.html`
- Create `content/timeline/` section with `_index.md` and archetype
- Build `timeline-moment.html` partial
- Build `layouts/timeline/list.html`
- Add homepage sections querying featured patterns and timeline moments
- Create sample content for both sections

**FRs covered:** FR-4 (structural content model), FR-6 (pattern gallery, timeline display), FR-7 (timeline tracks convergence)
**NFRs covered:** NFR-1 (emergence — patterns from content, not predefined), NFR-3 (evidence counts visible)

### Epic 5: Content Seeding & Branding
**User outcome:** The portfolio is populated with real content that demonstrates the knowledge-first approach. Nicolas's personal branding replaces all remaining generic assets.

- Replace author photo, favicon, OG images with Nicolas's branding
- Update `about.md` with Nicolas's real bio
- Update `hugo.yaml` with menu items for new sections (patterns, timeline)
- Seed real content: 2-3 posts (with evidence trails), 3-4 patterns, 5-10 timeline moments
- Tag milestone: `v0.2.0-phase0-complete`

**FRs covered:** FR-1 (content seeding validates the capture contract)
**NFRs covered:** NFR-5 (public vs. private content decisions)

### Definition of Done (per story, all epics)

- [ ] Mobile responsive verified (Tailwind mobile-first)
- [ ] Semantic HTML used (`nav`, `main`, `article`, `section`, `footer`)
- [ ] Alt text on all images
- [ ] All optional pearl fields use `{{ with }}` / `{{ if }}` guards
- [ ] No custom CSS for layout/spacing (Tailwind utilities only)
- [ ] New partials follow kebab-case naming
- [ ] Posts use page bundle structure
- [ ] Comments reference DESIGN-SPEC section where applicable
- [ ] `pnpm run test` passes

### Party Mode Decisions (2026-02-12)

**Round 1:**
- **Dark mode toggle removed** — Portfolio uses OS `prefers-color-scheme` only, no visible toggle button, no localStorage persistence. `dark:` CSS variants kept (zero cost). Clean separation: light gallery (portfolio) vs dark workshop (backoffice).
- **Netlify deployment moved to Epic 1** — Prove pipeline on day one, deploy incrementally per epic.
- **Responsive testing → Definition of Done** — Per-story verification, not batched in separate epic.
- **Epic 5 slimmed** — Content seeding and branding only, no deployment or responsive batching.

**Round 2:**
- **Story 3.4 simplified** — Post list/detail layouts only, no homepage "Latest Posts" wiring (avoids touching `layouts/index.html` twice across epics).
- **Story 4.4 expanded** — Renamed to "Wire All Homepage Content Sections" — wires posts + patterns + timeline into homepage in one coherent story, single touch to `layouts/index.html`.
- **Story 4.5 minimal** — Just enough sample content to verify templates work. Real content seeding belongs in Epic 5.
- **Epic 5 owns all real content** — All authentic content seeding (posts, patterns, timeline) consolidated here.

---

## Epic 1: Design Foundation, Clean Slate & Deploy Pipeline

The portfolio has its own visual identity, TailBliss defaults are gone, design tokens are live, and the site is deployed on Netlify — proving the full build/deploy pipeline from day one.

### Story 1.1: Remove TailBliss Defaults & Sample Content

As a **portfolio owner**,
I want all generic TailBliss sample content and branding removed,
So that I have a clean slate to build my own visual identity.

**Acceptance Criteria:**

**Given** the repository contains TailBliss sample content
**When** the cleanup story is completed
**Then** all 14 sample posts are removed (`blog-post-{1-7}.md`, `news-post-{1-7}.md`)
**And** unused partials are removed (`newsletter.html`, `posts-template.html`, `post-tile.html`)
**And** TailBliss branding images are removed (5 `tailbliss-*.png/svg/webp` files, `sample-logo.svg`)
**And** TailBliss sample images are removed (`tailbliss-rocket-indigo.png`, `hacktoberfest.jpg`)
**And** unused platform configs are removed (`Dockerfile`, `nginx.conf`, `nixpacks.toml`, `cloudflare.md`, `theme.toml`)
**And** `content/prose.md` is reviewed and removed if not needed
**And** `assets/css/style.css` is audited — useful content merged into `main.css` or file removed entirely
**And** `pnpm run test` passes with the cleaned codebase (Hugo builds successfully)

### Story 1.2: Implement Design Tokens & CSS Pipeline

As a **portfolio owner**,
I want design tokens with OKLCH pearl system colors integrated into the Vite/Tailwind pipeline,
So that all subsequent templates use a consistent, branded color system.

**Acceptance Criteria:**

**Given** the design token spec exists at `_bmad-output/implementation-artifacts/design-tokens.css`
**When** the tokens are implemented
**Then** `assets/css/design-tokens.css` exists as the live implementation of the spec tokens
**And** `assets/css/main.css` imports design-tokens.css before TailwindCSS (`@import "./design-tokens.css"` first)
**And** the import chain order is: tokens → Tailwind → `@theme` overrides → custom utilities
**And** the Vite build processes the import chain without errors (`pnpm run build` succeeds)
**And** `pnpm run dev:watch` works with live reload when tokens are modified
**And** the `@theme` block in `main.css` references design token custom properties (not hardcoded color values)
**And** `pnpm run test` passes

### Story 1.3: Simplify Dark Mode to OS Preference Only

As a **portfolio visitor**,
I want the site to respect my OS dark/light preference automatically,
So that I get a comfortable viewing experience without needing to find a toggle.

**Acceptance Criteria:**

**Given** the portfolio currently has a visible dark mode toggle button
**When** the dark mode simplification is completed
**Then** the toggle button is removed from the nav
**And** `assets/js/darkmode.js` is simplified to only detect `prefers-color-scheme: dark` (or replaced with pure CSS media query)
**And** localStorage persistence for dark mode preference is removed
**And** visitors with OS dark preference see the dark theme automatically
**And** visitors with OS light preference (or no preference) see the light theme
**And** existing `dark:` Tailwind utility classes continue to function correctly
**And** `pnpm run test` passes

### Story 1.4: Deploy to Netlify & Verify Pipeline

As a **portfolio owner**,
I want the cleaned, branded site deployed to Netlify,
So that the full build/deploy pipeline is proven and I can deploy incrementally from now on.

**Acceptance Criteria:**

**Given** the repo has a clean codebase with design tokens and simplified dark mode
**When** the deployment is configured
**Then** the GitHub repo is connected to Netlify
**And** `netlify.toml` build command (`pnpm run build`) executes successfully on Netlify
**And** the deployed site loads without CSS errors or broken images
**And** the site is accessible at the Netlify URL
**And** subsequent pushes to `main` trigger automatic rebuilds
**And** the deployed site respects OS dark/light preference correctly
**And** the epic milestone is tagged: `v0.1.0-phase0-foundation`

---

## Epic 2: Portfolio Shell & Navigation

Visitors land on a real homepage with Nicolas's identity, can navigate the site. The site feels like a finished product shell.

### Story 2.1: Update Base Layout & Typography

As a **portfolio visitor**,
I want the site to use the correct fonts and semantic HTML structure,
So that the portfolio feels polished and professional from any page.

**Acceptance Criteria:**

**Given** the base layout uses default TailBliss structure
**When** the base layout is updated
**Then** `layouts/_default/baseof.html` loads Google Fonts per DESIGN-SPEC (heading + body fonts)
**And** the HTML structure uses semantic elements (`nav`, `main`, `article`, `section`, `footer`)
**And** the `<html>` element supports `prefers-color-scheme` class application
**And** `layouts/partials/head.html` includes correct font preload links
**And** the page renders correctly with the new font stack
**And** `pnpm run test` passes

### Story 2.2: Redesign Navigation

As a **portfolio visitor**,
I want a clean navigation bar that matches the portfolio's visual identity,
So that I can navigate between sections easily on any device.

**Acceptance Criteria:**

**Given** the nav uses the default TailBliss design
**When** the nav is redesigned
**Then** `layouts/partials/nav.html` matches DESIGN-SPEC §6.2
**And** the nav has backdrop blur on scroll (Alpine.js `x-data` for scroll detection)
**And** there is no dark mode toggle button in the nav
**And** nav links include: Home, Posts, Patterns, Timeline, About
**And** the nav is responsive (mobile hamburger menu or equivalent at `sm` breakpoint)
**And** the nav uses design token colors (not hardcoded values)
**And** `pnpm run test` passes

### Story 2.3: Build Homepage Hero & Content Structure

As a **portfolio visitor**,
I want to see Nicolas's identity and tagline when landing on the site,
So that I immediately understand what this portfolio is about.

**Acceptance Criteria:**

**Given** the homepage shows generic TailBliss content
**When** the hero and homepage are rebuilt
**Then** `content/_index.md` exists with hero content in frontmatter (tagline, description)
**And** `layouts/partials/hero.html` renders hero content from `.Params.hero` (no hardcoded strings)
**And** `layouts/index.html` uses the hero partial and reads from `content/_index.md`
**And** `layouts/partials/section-heading.html` exists (mono uppercase section label, reusable)
**And** the homepage has empty placeholder sections for "Latest Posts", "Patterns", and "Timeline" (wired in Epic 4)
**And** the homepage is responsive across `sm`/`md`/`lg` breakpoints
**And** `pnpm run test` passes

### Story 2.4: Redesign Footer

As a **portfolio visitor**,
I want a clean footer with relevant links and attribution,
So that I can find additional information and the site feels complete.

**Acceptance Criteria:**

**Given** the footer uses the default TailBliss design
**When** the footer is redesigned
**Then** `layouts/partials/footer.html` matches DESIGN-SPEC §6.7
**And** the footer uses design token colors
**And** the footer includes relevant links (GitHub, contact, etc.)
**And** the footer includes license attribution (Apache 2.0 theme, CC-BY 4.0 content)
**And** the footer is responsive
**And** `pnpm run test` passes

---

## Epic 3: Pearl-Backed Content Publishing

Nicolas can publish blog posts backed by pearl evidence trails. Visitors see posts with optional pearl metadata, collapsible evidence trail, and confidence indicators. The content contract is live.

### Story 3.1: Create Post Archetypes & Content Structure

As a **portfolio owner**,
I want a post archetype that scaffolds page bundles with evidence sidecars,
So that every new post is created with the correct structure from the start.

**Acceptance Criteria:**

**Given** no post archetype exists for page bundles
**When** the archetype is created
**Then** `archetypes/posts/index.md` exists with all core frontmatter fields (`title`, `date`, `description`, `tags`, `categories`, `draft`)
**And** the archetype includes optional pearl fields (`pearlCount`, `timespan`, `patterns`, `confidence`)
**And** `archetypes/posts/evidence.yaml` exists as an empty sidecar template with example structure commented out
**And** running `hugo new content posts/test-post` creates a page bundle directory with both files
**And** `content/posts/_index.md` exists as the posts list page config
**And** `pnpm run test` passes

### Story 3.2: Build Pearl Meta & Confidence Partials

As a **portfolio visitor**,
I want to see pearl metadata (pearl count, timespan, patterns, confidence) on posts that have it,
So that I understand the evidence backing each post.

**Acceptance Criteria:**

**Given** posts may or may not have optional pearl frontmatter fields
**When** the pearl meta partial is built
**Then** `layouts/partials/pearl-meta.html` renders optional pearl fields using `{{ with }}` / `{{ if }}` guards
**And** the partial displays pearl count, timespan, linked patterns, and confidence when present
**And** the partial renders nothing when pearl fields are absent (graceful degradation)
**And** `layouts/partials/confidence-bar.html` renders a visual confidence indicator from `.Params.confidence`
**And** the confidence bar renders nothing when confidence is absent
**And** both partials use design token colors and Tailwind utilities (no custom CSS for layout)
**And** `pnpm run test` passes

### Story 3.3: Build Evidence Trail Partial

As a **portfolio visitor**,
I want to expand a collapsible evidence trail on posts that have one,
So that I can see the chronological pearl journey behind the post.

**Acceptance Criteria:**

**Given** a post is a page bundle with an `evidence.yaml` sidecar
**When** the evidence trail partial is built
**Then** `layouts/partials/evidence-trail.html` reads from `.Resources.GetMatch "evidence.yaml"` and unmarshals the data
**And** the evidence trail renders as a collapsible section (Alpine.js `x-data` for expand/collapse)
**And** each pearl in the trail displays date, color indicator, and content text
**And** the trail is collapsed by default, expandable on click
**And** the partial renders nothing when no `evidence.yaml` sidecar exists (graceful degradation)
**And** pearl colors use design token semantic colors (`fresh`, `convergence`, etc.)
**And** `pnpm run test` passes

### Story 3.4: Build Post List & Post Detail Pages

As a **portfolio visitor**,
I want to browse a list of posts and read individual posts with their full pearl context,
So that I can explore Nicolas's knowledge-backed content.

**Acceptance Criteria:**

**Given** post archetypes, pearl-meta, confidence-bar, and evidence-trail partials exist
**When** the post layouts are built
**Then** `layouts/partials/post-list-item.html` renders a post card with title, date, description, and pearl-meta (if present)
**And** `layouts/posts/list.html` displays paginated post list using `post-list-item.html` partial
**And** `layouts/posts/single.html` renders full post content with pearl-meta, confidence-bar, and evidence-trail
**And** the post detail page uses the `pearl-meta.html` partial (not inline guards)
**And** posts without pearl fields render cleanly as standard blog posts
**And** all layouts are responsive across `sm`/`md`/`lg` breakpoints
**And** `pnpm run test` passes

### Story 3.5: Create First Real Post with Evidence Trail

As a **portfolio owner**,
I want at least one real post published as a page bundle with evidence trail,
So that the content pipeline is proven end-to-end and visitors see a working example.

**Acceptance Criteria:**

**Given** all post templates and partials are built
**When** the first real post is created
**Then** a post exists at `content/posts/{slug}/index.md` with all core and pearl frontmatter fields populated
**And** an `evidence.yaml` sidecar exists with at least 3 pearl entries (real content)
**And** the post renders correctly on the post list page with pearl metadata visible
**And** the post detail page shows the confidence bar and collapsible evidence trail
**And** the evidence trail expands/collapses correctly
**And** `pnpm run test` passes

---

## Epic 4: Patterns Gallery & Growth Timeline

Visitors can explore emerging skill patterns as visual cards and browse a chronological timeline of growth moments. The knowledge-first narrative is visible.

### Story 4.1: Create Pattern Content Section & Archetype

As a **portfolio owner**,
I want a patterns content section with a proper archetype,
So that I can create skill pattern pages with consistent frontmatter structure.

**Acceptance Criteria:**

**Given** no patterns content section exists
**When** the pattern structure is created
**Then** `content/patterns/_index.md` exists as the patterns list page config
**And** `archetypes/patterns.md` exists with all pattern frontmatter fields (`title`, `description`, `confidence`, `pearlCount`, `timespan`, `trajectory`, `tags`)
**And** pattern files are leaf files at `content/patterns/{slug}.md` (not page bundles)
**And** running `hugo new content patterns/test-pattern` creates a correctly structured file
**And** `hugo.yaml` is updated with patterns section in site menu if needed
**And** `pnpm run test` passes

### Story 4.2: Build Pattern Card & Pattern Pages

As a **portfolio visitor**,
I want to browse skill patterns as visual cards and drill into pattern detail pages,
So that I can understand Nicolas's emerging capabilities and their evidence.

**Acceptance Criteria:**

**Given** the patterns content section and archetype exist
**When** pattern layouts are built
**Then** `layouts/partials/pattern-card.html` renders a card with title, confidence, pearl count, timespan, and short description
**And** the partial receives context via `dict` (`{{ partial "pattern-card.html" (dict "pattern" . "index" $i) }}`)
**And** `layouts/patterns/list.html` displays pattern cards in a responsive grid
**And** `layouts/patterns/single.html` renders full pattern detail (description, confidence bar, pearl count, timespan, trajectory, linked tags)
**And** the confidence-bar partial (from Epic 3) is reused on pattern detail pages
**And** pattern cards use design token colors and Tailwind utilities
**And** `pnpm run test` passes

### Story 4.3: Create Timeline Content Section & Partial

As a **portfolio visitor**,
I want to browse a chronological timeline of growth moments,
So that I can understand Nicolas's learning trajectory over time.

**Acceptance Criteria:**

**Given** no timeline content section exists
**When** the timeline structure and layout are created
**Then** `content/timeline/_index.md` exists as the timeline list page config
**And** `archetypes/timeline.md` exists with timeline frontmatter fields (`title`, `date`, `year`, `color`, `quote`, `linkedPost`, `featuredOnHomepage`)
**And** timeline files are leaf files at `content/timeline/{slug}.md`
**And** `layouts/partials/timeline-moment.html` renders a single timeline entry with date, color indicator, quote, and optional link to related post
**And** `layouts/timeline/list.html` displays timeline moments in chronological order (vertical, magazine-style per UX spec)
**And** timeline colors use design token semantic colors
**And** `hugo.yaml` is updated with timeline section in site menu if needed
**And** `pnpm run test` passes

### Story 4.4: Wire All Homepage Content Sections

As a **portfolio visitor**,
I want to see latest posts, featured patterns, and recent timeline moments on the homepage,
So that the knowledge-first narrative is immediately visible when I land on the site.

**Acceptance Criteria:**

**Given** post list-item, pattern card, and timeline moment partials all exist
**When** the homepage content sections are wired
**Then** the homepage queries and displays recent posts using `post-list-item.html` partial
**And** the homepage queries and displays up to 4 featured patterns (using `where .Params.featuredOnHomepage true` or similar) using `pattern-card.html` partial
**And** the homepage queries and displays up to 10 recent timeline moments using `timeline-moment.html` partial
**And** each section uses the `section-heading.html` partial for its label
**And** sections render gracefully when no content exists yet (empty state, no errors)
**And** the homepage is responsive across `sm`/`md`/`lg` breakpoints
**And** `pnpm run test` passes

### Story 4.5: Seed Minimal Sample Content for Template Verification

As a **developer**,
I want minimal sample content in patterns and timeline sections,
So that templates can be visually verified before real content is added in Epic 5.

**Acceptance Criteria:**

**Given** all pattern and timeline templates and partials are built
**When** sample content is seeded
**Then** at least 2 pattern files exist in `content/patterns/` with placeholder skill descriptions and pearl counts
**And** at least 3 timeline moment files exist in `content/timeline/` with placeholder dates and quotes
**And** at least 1 pattern has `featuredOnHomepage: true` and appears on the homepage
**And** at least 1 timeline moment has `featuredOnHomepage: true` and appears on the homepage
**And** pattern detail pages render correctly with all fields
**And** timeline list page displays moments in chronological order
**And** `pnpm run test` passes

---

## Epic 5: Content Seeding & Branding

The portfolio is populated with real content that demonstrates the knowledge-first approach. Nicolas's personal branding replaces all remaining generic assets.

### Story 5.1: Replace Branding Assets

As a **portfolio owner**,
I want all generic branding replaced with my own,
So that the portfolio is unmistakably mine.

**Acceptance Criteria:**

**Given** the site still has TailBliss/placeholder branding assets
**When** branding is replaced
**Then** `assets/images/global/author.webp` is replaced with Nicolas's photo
**And** `static/favicon/` contains Nicolas's favicon set
**And** `static/images/site-logo.svg` is replaced with Nicolas's logo (or removed if no logo)
**And** OG images in `static/images/og-images/` are updated for social sharing
**And** no TailBliss or placeholder branding remains anywhere in the site
**And** `pnpm run test` passes

### Story 5.2: Update About Page & Site Configuration

As a **portfolio visitor**,
I want to read Nicolas's real bio and navigate to all sections from the menu,
So that I understand who Nicolas is and can explore the full site.

**Acceptance Criteria:**

**Given** the about page has placeholder content and menus may be incomplete
**When** the about page and config are updated
**Then** `content/about.md` contains Nicolas's real bio, linking to the patterns gallery as capability evidence
**And** `hugo.yaml` includes menu items for all sections: Home, Posts, Patterns, Timeline, About
**And** menu items render correctly in the nav partial
**And** site metadata in `hugo.yaml` (title, description, author) reflects Nicolas's identity
**And** `pnpm run test` passes

### Story 5.3: Seed Real Posts & Evidence Trails

As a **portfolio owner**,
I want 2-3 real posts published with evidence trails,
So that the portfolio demonstrates the knowledge-first approach with authentic content.

**Acceptance Criteria:**

**Given** all post templates, partials, and archetypes are functional
**When** real posts are seeded
**Then** at least 2 additional posts exist as page bundles in `content/posts/` (beyond the one from Epic 3)
**And** each post has populated core and pearl frontmatter fields with real data
**And** at least 2 posts have `evidence.yaml` sidecars with real pearl entries
**And** posts appear on the post list page and homepage "Latest Posts" section
**And** evidence trails expand/collapse correctly on each post
**And** the content demonstrates variety (different pearl counts, timespans, confidence levels)
**And** `pnpm run test` passes

### Story 5.4: Seed Real Patterns & Timeline Content

As a **portfolio owner**,
I want real skill patterns and timeline moments replacing the minimal samples,
So that the patterns gallery and timeline showcase authentic growth narrative.

**Acceptance Criteria:**

**Given** minimal sample content exists from Epic 4
**When** real content replaces samples
**Then** at least 3 pattern files exist in `content/patterns/` with real skill descriptions, confidence scores, and pearl counts
**And** at least 5 timeline moment files exist in `content/timeline/` with real dates, quotes, and color indicators
**And** at least 2 patterns have `featuredOnHomepage: true` and appear on the homepage
**And** at least 3 timeline moments have `featuredOnHomepage: true` and appear on the homepage
**And** pattern detail pages render correctly with all fields
**And** timeline list page displays moments in chronological order
**And** sample/placeholder content from Epic 4 is replaced or removed
**And** `pnpm run test` passes

### Story 5.5: Final Polish & Phase 0 Milestone Tag

As a **portfolio owner**,
I want the Phase 0 portfolio complete, deployed, and tagged,
So that I have a proven foundation to build upon and a milestone to reference.

**Acceptance Criteria:**

**Given** all branding, content, and configuration are in place
**When** the final polish is applied
**Then** all pages render correctly on the deployed Netlify site
**And** no broken links, missing images, or layout issues exist
**And** the homepage displays real posts, patterns, and timeline moments
**And** the contact page (`content/contact.md`) is reviewed and updated if needed
**And** `pnpm run test` passes
**And** the milestone is tagged: `v0.2.0-phase0-complete`
