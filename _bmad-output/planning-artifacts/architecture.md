---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments:
  - '_bmad-output/01-prd-skill-discovery-system.md'
  - '_bmad-output/planning-artifacts/ux-design-specification.md'
  - '_bmad-output/implementation-artifacts/DESIGN-SPEC.md'
  - '_bmad-output/implementation-artifacts/design-tokens.css'
  - '_bmad-output/implementation-artifacts/sprint-status.yaml'
workflowType: 'architecture'
lastStep: 8
status: 'complete'
completedAt: '2026-02-11'
project_name: 'ndb_hugo-tailbliss'
user_name: 'Nicolas'
date: '2026-02-11'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
7 FRs covering the full pearl lifecycle: multi-format capture (FR-1), pearl management with temporal metadata (FR-2), LLM-driven skill discovery via embed→cluster→label→validate pipeline (FR-3), Neo4j graph storage with temporal queries and negative space detection (FR-4), evidence-based XP calculation with decay and compound effects (FR-5), visualization APIs for graph/timeline/frontier export (FR-6), and historical idea indexing with convergence matching (FR-7).

Architecturally, these decompose into: an ingestion layer (FR-1, FR-2), a processing layer (FR-3, FR-7), a storage layer (FR-4), a computation layer (FR-5), and a presentation layer (FR-6). The processing layer is the most architecturally significant — it orchestrates Claude API, text-embedding-3, HDBSCAN clustering, and Graphiti ingestion as a pipeline.

**Non-Functional Requirements:**
- **Emergence design** (NFR-1): Graph DB required, no fixed taxonomies, schema-flexible
- **Continuous operation** (NFR-2): Background processing, real-time graph updates, data consistency
- **Evidence-based** (NFR-3): Every claim links to documented work, proof-of-work linking
- **Scalability** (NFR-4): Sub-500ms graph queries, support 1000+ skills / 10000+ outcomes
- **Privacy & control** (NFR-5): Fine-grained visibility, private-first, full data export

**Scale & Complexity:**

- Primary domain: full-stack (static site + SPA + API + graph DB + LLM)
- Complexity level: high
- Estimated architectural components: 12+ (Hugo templates, Svelte app, FastAPI backend, Graphiti pipeline, Neo4j, PostgreSQL, embedding service, clustering service, LLM service, D3 visualization, git publishing bridge, design token system)

### Phase 0 Scope Boundary

**Critical distinction:** The full system is complex, but Phase 0 architecture is deliberately thin.

**In scope for Phase 0 (this repo):**
- Hugo templates restyled to DESIGN-SPEC (replace generic TailBliss)
- TailwindCSS 4.1 with design tokens from `assets/css/main.css`
- Netlify deployment (already configured in `netlify.toml`)
- Content model: Hugo frontmatter schema supporting evidence trail, pearl counts, patterns, confidence, timespan
- Manually-written posts that conform to the content contract (backoffice generates them later)
- Responsive portfolio (mobile breakpoints — Open Question #5, must be addressed)

**Out of scope for Phase 0 architecture (deferred to ndb_backoffice repo):**
- FastAPI backend, Neo4j, Graphiti, PostgreSQL
- Svelte application, D3 visualization
- AI pipeline (embed, cluster, label, validate)
- Docker container deployment

**The architecture doc should define the full system context but clearly mark the Phase 0 implementation boundary.** Don't specify container orchestration for software that doesn't exist yet.

### Technical Constraints & Dependencies

**Locked technology decisions (PRD v2.1 + DESIGN-SPEC):**
- Portfolio: Hugo + TailBliss + TailwindCSS 4.1 → Netlify (git push → CDN)
- BackOffice: Svelte + Vite + TailwindCSS 4.1 → Docker container (local → VPS)
- Backend: FastAPI (Python), config-driven, layered architecture (betterCallSaul baseline)
- Storage: Neo4j + Graphiti (operational graph) + PostgreSQL (training log)
- Visualization: D3.js (custom SVG/Canvas)
- AI: Claude API (labeling, NL queries, drafts) + text-embedding-3 (vectors)
- Diagrams: Mermaid (text-based, renders in both apps)

**Deployment constraint:** Portfolio publishes independently (git commit .md → Netlify rebuild). BackOffice publishes TO portfolio via git commit bridge. Portfolio doesn't depend on backoffice being operational.

**Proven baseline:** betterCallSaul MVP provides config-driven FastAPI + SvelteKit with layered services, session auth, and 49 passing tests. Adapt, not rebuild.

**Riskiest dependency: Graphiti.** The entire temporal tracking concern depends on Graphiti working as expected. It's the least mature component in the stack. Fallback: plain Neo4j with manual timestamp management on nodes/edges. This should be validated early in backoffice development.

### Cross-Cutting Concerns Identified

1. **Design token sharing** — Defined in Hugo first (`assets/css/main.css`), extracted to shared package when backoffice consumes them. OKLCH color space, semantic pearl system colors.

2. **Content contract (Hugo frontmatter schema)** — The portfolio needs structured frontmatter even before the backoffice generates it. Required fields: `title`, `date`, `description`, `tags`, `pearlCount`, `timespan` (e.g., "March 2024 → January 2025"), `patterns` (linked skill patterns), `confidence`, `evidenceTrail` (list of chronological pearls with timestamp, color, content). This contract is the data interface between apps — define it in Phase 0, backoffice conforms to it later.

3. **Git publishing bridge** — The only integration point between two otherwise independent systems. Needs a contract: valid frontmatter schema, image handling conventions, branch strategy (direct to main? PR?), error handling (malformed frontmatter, concurrent publishes). Load-bearing architecture — treat it as a first-class concern.

4. **Temporal tracking** — Every entity needs timestamps. Graphiti manages temporal graph ingestion. Convergence detection depends on precise temporal data. Entirely dependent on Graphiti (see risk note above).

5. **AI pipeline orchestration** — Embed → cluster → label → validate touches capture, storage, and presentation. Must be async/background ("nothing automatic, everything automated").

6. **Evidence tracing** — Every public claim must link to documented work. Flows through: pearl → necklace → pattern → published post. Cross-cuts storage, API, and both frontends. Even in Phase 0 with manual posts, the evidence trail structure must exist in frontmatter/data.

7. **Privacy boundary** — Clear separation between private (backoffice, raw pearls, graph) and public (portfolio, published posts, curated patterns). Git commit is the publish gate.

8. **Validation-as-training** — User accept/reject/edit actions logged to PostgreSQL as immutable training signals. Affects capture UI, API design, and storage schema.

9. **Single-user auth** — Session-based, local auth. No registration flow. Created via CLI/first-run. Minimal but required for backoffice access.

10. **Freshness data source** — "Last pearl captured 3h ago" is static in Phase 0 (Hugo build-time value or hardcoded placeholder). Architecture should acknowledge this transitions to a live API endpoint when backoffice is operational. Design the Hugo partial to accept both static and dynamic sources.

11. **Responsive behavior** — Portfolio needs mobile breakpoints (visitors use phones). BackOffice is desktop-only. Breakpoints TBD (DESIGN-SPEC Open Question #5) but must be addressed in Phase 0 since the portfolio is public-facing.

12. **Progressive disclosure as data pattern** — Evidence trail (collapsed), timeline (curated 10 moments), pattern cards (mini-graph preview) all require "summary + detail" data structures. Hugo templates need two levels: what shows on homepage vs. what shows on detail page. Content model must support both.

## Starter Template Evaluation

### Primary Technology Domain

Full-stack with two distinct applications: static site (portfolio) + reactive SPA with API backend (backoffice).

### Starter Options Considered

No evaluation needed — both starters are pre-selected and proven.

### Selected Starters

**Portfolio: Hugo + TailBliss (already running)**

- **Language & Runtime:** Go templates (Hugo v0.148.2+), JavaScript (Alpine.js 3.15.8, Vite)
- **Styling:** TailwindCSS 4.1 with `@theme` directive, OKLCH color space, custom prose styles
- **Build Tooling:** Vite processes CSS → Hugo compiles markdown/templates → Netlify deploys
- **Testing:** `pnpm run test` (Hugo build validation)
- **Code Organization:** Hugo standard: `layouts/` (templates), `content/` (markdown), `assets/` (CSS/JS), `static/` (generated CSS)
- **Development:** `pnpm run dev:watch` — Vite CSS watcher + Hugo live reload, concurrent
- **Initialization:** N/A — already initialized, running at `http://localhost:1313`

**BackOffice: betterCallSaul MVP (proven baseline, separate repo)**

- **Language & Runtime:** Python 3.x (FastAPI), TypeScript (SvelteKit), containerized (Docker)
- **Styling:** TailwindCSS 4.1 (to be configured with same tokens)
- **Build Tooling:** Vite (Svelte), uvicorn (FastAPI)
- **Testing:** pytest (49 tests, 100% pass in baseline)
- **Code Organization:** Config-driven, layered services, session auth
- **Development:** Docker compose (FastAPI + SvelteKit + databases)
- **Initialization:** Fork/adapt betterCallSaul repo when backoffice work begins

### Rationale

Both starters are locked decisions (PRD v2.1, DESIGN-SPEC §1). Hugo + TailBliss is already running with TailwindCSS 4.1 and OKLCH configured. betterCallSaul is proven with 49 passing tests and the exact architecture pattern needed (config-driven FastAPI + SvelteKit). No evaluation of alternatives warranted — these are the right tools, already working.

**Phase 0 note:** Only the Hugo portfolio starter is active now. The betterCallSaul baseline is documented here for completeness but won't be initialized until `ndb_backoffice` repo is created.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Content contract (frontmatter schema) — defines data interface between apps
- CSS integration strategy — determines how design tokens flow into templates
- Responsive breakpoints — portfolio is public-facing, must work on mobile

**Important Decisions (Shape Architecture):**
- Content types for patterns/timeline — determines Hugo structure
- Branch strategy — affects development workflow
- Alpine.js scope — bounds frontend interactivity

**Deferred Decisions (Post-Phase 0):**
- BackOffice authentication (session auth from betterCallSaul, separate repo)
- API design patterns (FastAPI, separate repo)
- Graph database schema (Neo4j, separate repo)
- Container orchestration (Docker, separate repo)
- Git publishing bridge contract details (when backoffice starts publishing)

### Data Architecture

**D1.1: Post Frontmatter Schema — Core + Optional (Graceful Degradation)**

Core fields (required for all posts):
```yaml
title: "Post Title"
date: 2026-02-11
description: "Short description for meta/cards"
tags: ["tag1", "tag2"]
categories: ["category"]
draft: false
```

Pearl-specific fields (optional, gracefully handled when absent):
```yaml
pearlCount: 15
timespan: "March 2024 → January 2025"
patterns: ["container-orchestration", "teaching"]
confidence: 87
```

Evidence trail as page bundle sidecar (not inline frontmatter):
```
content/posts/my-docker-journey/
├── index.md          ← post content + core/pearl frontmatter
└── evidence.yaml     ← pearl trail as sidecar data file
```

`evidence.yaml` structure:
```yaml
pearls:
  - date: 2024-03-15
    color: "fresh"
    content: "First Docker networking notes"
  - date: 2024-06-20
    color: "convergence"
    content: "Connected to teaching workflow"
```

Template reads via `.Resources.GetMatch "evidence.yaml"`. Keeps frontmatter clean, makes backoffice git commits simpler (write two files instead of one massive frontmatter block), and scales to 50+ pearls without degrading readability.

Templates use graceful degradation via a shared `partials/pearl-meta.html` partial that handles all optional pearl field rendering with `{{ with }}` / `{{ isset }}` guards. Called from post-list, post-single, and pattern cards — DRY from the start.

Hugo archetypes (`archetypes/posts.md`) pre-fill required frontmatter fields with sensible defaults.

- **Rationale:** Documents the contract now. Backoffice targets this schema. No migration needed — just start populating optional fields. Page bundles keep posts self-contained.
- **Affects:** All Hugo templates (partials use existence guards), backoffice publish pipeline (future)

**D1.2: Pattern and Timeline Data — Hugo Content Sections**

- `content/patterns/` — Each pattern is a content file with frontmatter (title, confidence, pearlCount, timespan, trajectory, description, miniGraph data). Gets its own `/patterns/{slug}` page.
- `content/timeline/` — Each moment is a content file with frontmatter (date, year, color, quote, linkedPost, featuredOnHomepage). Enables `/timeline/` page.
- Homepage queries: `where .Params.featuredOnHomepage true` or sort by date, limited to 4 patterns and 10 moments. Exact filtering TBD during implementation.

Hugo archetypes required:
- `archetypes/patterns.md` — pre-filled with all pattern frontmatter fields
- `archetypes/timeline.md` — pre-filled with all timeline moment fields

- **Rationale:** Content sections scale naturally. Each item gets its own page (DESIGN-SPEC requires `/patterns/{slug}`). Hugo's content management handles ordering, filtering, and pagination. Archetypes reduce manual error.
- **Affects:** Hugo directory structure, homepage template queries, future backoffice git commits

**D1.3: Image Handling — Hugo Image Processing**

Use existing `imgc` shortcode for WebP optimization with lazy loading. Images stored in `assets/images/`. No change from current setup.

- **Rationale:** Already works. WebP + lazy loading = good performance out of the box.
- **Affects:** Content authoring (use `{{</* imgc */>}}` shortcode)

### Frontend Architecture

**D2.1: CSS Integration — Separate Import**

Design tokens lifecycle:
- **Spec reference:** `_bmad-output/implementation-artifacts/design-tokens.css` (authoritative spec)
- **Live implementation:** `assets/css/design-tokens.css` (what Hugo/Vite actually processes)
- Phase 0 implementation copies spec tokens into `assets/css/`. The `_bmad-output` version remains the design reference; `assets/css/` is the running code.

Import chain in `assets/css/main.css` (order matters):
```css
/* 1. Design tokens first — CSS custom properties must exist before Tailwind references them */
@import "./design-tokens.css";
/* 2. TailwindCSS */
@import "tailwindcss";
/* 3. Theme overrides using tokens */
@theme { ... }
/* 4. Custom utilities and components */
```

Vite processes this import chain. Tokens must be defined before Tailwind's `@theme` block references them — verify during first build.

- **Rationale:** Clean separation. When backoffice starts, extract `assets/css/design-tokens.css` to `ndb_design-tokens/` package. The `@import` path changes, nothing else does.
- **Affects:** Build pipeline (Vite processes the import chain), future token extraction

**D2.2: Responsive Breakpoints — Tailwind Defaults**

Use TailwindCSS default breakpoints:
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px

Portfolio max-width is 920px, so most responsive work happens between `sm` and `lg`. Post content at 700px fits naturally within `md` breakpoint.

- **Rationale:** Well-documented, familiar, good enough for Phase 0. Revisit if content-specific breakpoints prove necessary.
- **Affects:** All Hugo templates and partials

**D2.3: Alpine.js Scope — Moderate**

Alpine.js handles:
- Dark mode toggle (existing, localStorage-backed)
- Nav backdrop blur on scroll (DESIGN-SPEC)
- Evidence trail expand/collapse (DESIGN-SPEC)
- Any small interactive behaviors as templates are built

Alpine.js does NOT handle:
- Graph visualization (D3.js, backoffice only)
- NL queries (backoffice only)
- Real-time data fetching (deferred, possibly freshness heartbeat later)
- Complex state management (Svelte reactivity, backoffice only)

- **Rationale:** Portfolio is a reading-focused static site. Heavy interactivity belongs in the Svelte backoffice. Alpine.js stays lightweight — progressive enhancement, not application framework.
- **Affects:** Template complexity (Alpine directives inline in HTML), JS bundle size (minimal)

### Infrastructure & Deployment

**D3.1: Branch Strategy — Feature Branches per Story, Incremental Merges**

- `main` = production (Netlify deploys from main)
- `phase0/{story-id}-{short-name}` = one branch per story
- Merge each story to `main` when the story is complete and tested
- Epic completion = all stories merged → tag the commit (e.g., `v0.1.0-phase0-ui`)

Branch convention uses `phase0/` prefix for clean filtering (`git branch --list 'phase0/*'`). Future phases use `phase1/`, etc.

- **Rationale:** Incremental merges give Netlify deployment feedback after each story. Avoids long-lived branches and merge conflicts. Tags mark epic milestones.
- **Affects:** Git workflow, Netlify deploy triggers, epic tracking

**D3.2: CI/CD — Netlify Only**

Netlify runs `pnpm run build` on push to main. No separate GitHub Actions CI (CodeQL already runs on schedule for security scanning).

- **Rationale:** Learning Netlify is a Phase 0 goal. Keep the pipeline simple — push → build → deploy. Add GitHub Actions later if build validation or test automation is needed.
- **Affects:** Deployment workflow, build error visibility

### Decision Impact Analysis

**Implementation Sequence:**
1. CSS integration (D2.1) — copy tokens to `assets/css/`, set up import chain, verify build
2. Responsive breakpoints (D2.2) — configure in templates from the start
3. Content types (D1.2) — create `content/patterns/` and `content/timeline/` with archetypes
4. Post schema (D1.1) — define frontmatter, create `pearl-meta.html` partial, build templates
5. Alpine.js interactions (D2.3) — add as components are built
6. Branch strategy (D3.1) — adopt when first story starts
7. Netlify deploy (D3.2) — push to GitHub, configure Netlify

**Cross-Component Dependencies:**
- D2.1 (CSS import) must happen before templates can use design tokens
- D1.1 (frontmatter schema) must be defined before building post templates
- D1.2 (content types) must exist before homepage can query patterns/timeline
- D2.1 import order (tokens → Tailwind → theme → utilities) must be verified on first build
- D3.1 (branches) and D3.2 (Netlify) are independent of template work

## Implementation Patterns & Consistency Rules

### Critical Conflict Points Identified

10 areas where AI agents could make different choices, plus 6 Hugo-specific patterns from party mode review.

### Build Pipeline Clarification

**CRITICAL: This project uses Vite for CSS processing, NOT Hugo Pipes.**

```
Vite (assets/css/main.css) → processes TailwindCSS + tokens → outputs to static/css/
Hugo reads static/css/ → compiles templates + content → outputs to public/
Netlify deploys public/
```

Do NOT use `resources.PostCSS` or `resources.Get "css/main.css" | resources.PostCSS` in templates. The existing Vite pipeline handles CSS. Hugo consumes the pre-built CSS from `static/css/`. This is already configured in `vite.config.mjs` and `pnpm run dev:watch`.

### Naming Patterns

**Hugo Partials:** Flat, kebab-case.
- `partials/nav.html`, `partials/hero.html`, `partials/post-list-item.html`
- `partials/pearl-meta.html`, `partials/pattern-card.html`, `partials/timeline-moment.html`
- No subdirectories unless partial count exceeds ~15 files
- Name describes the component, not the page it's used on

**CSS Classes:** Tailwind-first, custom classes only for design token semantics.
- Layout, spacing, sizing: Tailwind utilities (`flex`, `gap-4`, `max-w-[920px]`)
- Semantic design roles: custom classes from tokens (`.section-label`, `.meta`, `.font-heading`, `.meta-sep`)
- Never create custom classes for things Tailwind handles
- Custom classes only for *design concepts* (e.g., `.confidence-bar`, `.pearl-dot`)

**Content Files:**
- Posts: page bundles — `content/posts/{slug}/index.md` (supports evidence sidecar)
- Patterns: leaf files — `content/patterns/{slug}.md` (no sidecar needed yet)
- Timeline: leaf files — `content/timeline/{slug}.md` (no sidecar needed yet)
- Slugs: kebab-case, descriptive (`container-orchestration`, not `pattern-1`)

**Page Bundle vs Leaf File Decision Rule:**
- **Page bundle** when content has associated resources (data files, images, evidence trail)
- **Leaf file** when content is frontmatter + markdown only
- **Promotion path:** To promote a leaf file to a page bundle, create a directory with the slug name and move the file to `index.md`. Example: `content/patterns/container-orchestration.md` → `content/patterns/container-orchestration/index.md`
- Note: `.Resources.GetMatch` only works in page bundles, not leaf files

**Template Variables:** camelCase (`$pearlCount`, `$isActive`).
**Frontmatter Fields:** camelCase for custom fields (`pearlCount`, `featuredOnHomepage`). Hugo built-ins stay as-is (`title`, `date`, `tags`).

### Structure Patterns

**Layout Organization:**
```
layouts/
├── _default/
│   ├── baseof.html
│   ├── list.html
│   └── single.html
├── posts/
│   ├── list.html
│   └── single.html
├── patterns/
│   ├── list.html
│   └── single.html
├── timeline/
│   └── list.html
├── partials/
│   ├── nav.html
│   ├── hero.html
│   ├── footer.html
│   ├── post-list-item.html
│   ├── pattern-card.html
│   ├── timeline-moment.html
│   ├── pearl-meta.html
│   ├── confidence-bar.html
│   ├── section-heading.html
│   └── evidence-trail.html
└── shortcodes/
    └── imgc.html (existing)
```

**Archetypes (directory archetype for page bundles):**
```
archetypes/
├── posts/
│   ├── index.md          ← frontmatter template with all core + pearl fields
│   └── evidence.yaml     ← empty evidence sidecar template
├── patterns.md           ← frontmatter template for pattern content
└── timeline.md           ← frontmatter template for timeline moments
```

`hugo new content posts/my-new-post` creates the entire page bundle with sidecar. Without the directory archetype, agents would create flat `.md` files and miss the evidence sidecar.

**Alpine.js Code Location:**
- Inline `x-data` for simple, component-scoped state (collapse toggle, scroll detection)
- Separate file in `assets/js/` only for shared cross-template behavior (dark mode toggle already follows this pattern)
- No JS framework, no build step for Alpine — loaded from CDN via `<script>` tag

### Format Patterns

**Dates:**
- Frontmatter: ISO format (`2026-02-11`)
- Display in templates using Hugo `.Format`:
  - Article meta: `{{ .Date.Format "January 2, 2006" }}` → "February 11, 2026"
  - Mono metadata: `{{ .Date.Format "2006-01-02" }}` → "2026-02-11"
  - Timeline: `{{ .Date.Format "Jan 2006" }}` → "Feb 2026"
  - Timespan: stored as string in frontmatter, rendered as-is

**Frontmatter Convention:**
- Hugo built-in fields: lowercase (`title`, `date`, `draft`, `tags`)
- Custom pearl fields: camelCase (`pearlCount`, `timespan`, `confidence`, `featuredOnHomepage`)
- This visual distinction makes it immediately clear which fields are "ours"

### Process Patterns

**Template Guards (Graceful Degradation):**
```go-html-template
{{/* ✅ CORRECT: guard optional pearl fields with "with" */}}
{{ with .Params.pearlCount }}
  <span class="meta">{{ . }} pearls</span>
{{ end }}

{{/* ✅ CORRECT: guard boolean/numeric with "if" */}}
{{ if .Params.confidence }}
  {{ partial "confidence-bar.html" . }}
{{ end }}

{{/* ✅ CORRECT: evidence trail from page bundle sidecar */}}
{{/* NOTE: .Resources.GetMatch only works in page bundles, not leaf files */}}
{{ $evidence := .Resources.GetMatch "evidence.yaml" }}
{{ with $evidence }}
  {{ $data := . | transform.Unmarshal }}
  {{ partial "evidence-trail.html" (dict "data" $data "page" $) }}
{{ end }}

{{/* ❌ WRONG: no guard — breaks on posts without pearl fields */}}
<span>{{ .Params.pearlCount }} pearls</span>
```

**Partial Interface Pattern:**
- Partials that need context beyond `.` receive a `dict`:
  ```go-html-template
  {{ partial "pattern-card.html" (dict "pattern" . "index" $i) }}
  ```
- Partials that only need the current page context receive `.`:
  ```go-html-template
  {{ partial "pearl-meta.html" . }}
  ```
- Use `"page"` as dict key for the page context (not `"context"` — loaded term in Go templates)

**Comment Conventions:**
- Use `{{/* */}}` for Hugo comments (not rendered in output)
- Comment the "why", not the "what"
- Mark section boundaries: `{{/* ── Hero Section ── */}}`
- Reference design spec: `{{/* DESIGN-SPEC §6: Nav blurs on scroll */}}`
- Don't comment obvious Tailwind utilities or standard Hugo loops

### Enforcement Guidelines

**All AI Agents MUST:**
1. Use template guards (`with`/`if`) for ALL optional pearl frontmatter fields
2. Use Tailwind utilities for layout/spacing; custom classes only for design token semantics
3. Follow page bundle pattern for posts, leaf files for patterns/timeline
4. Store dates as ISO, format in templates
5. Use camelCase for custom frontmatter fields
6. Comment the "why" with design-spec references, not the "what"
7. Use the `pearl-meta.html` partial for rendering optional pearl data — don't inline guards in every template
8. Never use Hugo Pipes for CSS — Vite handles the CSS pipeline
9. Use `"page"` (not `"context"`) as dict key when passing page context to partials

**Pattern Verification Checklist (apply to every story before marking complete):**
- [ ] All optional pearl fields use `{{ with }}` or `{{ if }}` guards
- [ ] No custom CSS classes for layout/spacing (Tailwind utilities only)
- [ ] All new partials follow kebab-case naming
- [ ] Post content uses page bundle structure (not leaf file)
- [ ] Comments reference DESIGN-SPEC section where applicable
- [ ] `pnpm run test` passes (Hugo build validation)

**Anti-Patterns:**
- ❌ Creating `.post-title { font-size: 20px; }` when `text-[20px] font-heading font-bold` works
- ❌ Putting evidence trail data in frontmatter instead of `evidence.yaml` sidecar
- ❌ Rendering `{{ .Params.pearlCount }}` without a `with` or `if` guard
- ❌ Creating `partials/components/cards/pattern/pattern-card.html` (over-nesting)
- ❌ Adding JavaScript files for one-off Alpine interactions
- ❌ Using `resources.PostCSS` or Hugo Pipes for CSS (Vite handles this)
- ❌ Using `.Resources.GetMatch` on a leaf file (only works in page bundles)
- ❌ Creating flat `.md` posts without the page bundle directory structure

## Project Structure & Boundaries

### Complete Project Directory Structure

```
ndb_hugo-tailbliss/
├── README.md                              [existing]
├── CLAUDE.md                              [existing]
├── hugo.yaml                              [existing, modify]
├── package.json                           [existing]
├── pnpm-lock.yaml                         [existing]
├── pnpm-workspace.yaml                    [existing]
├── vite.config.mjs                        [existing]
├── postcss.config.js                      [existing]
├── netlify.toml                           [existing]
├── LICENSE                                [existing]
├── .gitignore                             [existing]
├── .github/
│   ├── dependabot.yml                     [existing]
│   └── workflows/
│       └── codeql.yml                     [existing]
│
├── archetypes/
│   ├── default.md                         [existing, keep as fallback]
│   ├── posts/                             [new — directory archetype]
│   │   ├── index.md                       [new — core + pearl frontmatter template]
│   │   └── evidence.yaml                  [new — empty evidence sidecar template]
│   ├── patterns.md                        [new — pattern frontmatter template]
│   └── timeline.md                        [new — timeline moment template]
│
├── assets/
│   ├── css/
│   │   ├── main.css                       [existing, modify — add @import design-tokens]
│   │   ├── design-tokens.css              [new — live implementation of spec tokens]
│   │   └── style.css                      [existing, audit — merge into main.css or remove]
│   ├── images/
│   │   ├── global/
│   │   │   └── author.webp                [existing, replace with Nicolas's photo]
│   │   ├── featured/                      [existing]
│   │   ├── pages/                         [existing, remove TailBliss samples]
│   │   └── posts/                         [existing — content images live here or in page bundles]
│   └── js/
│       └── darkmode.js                    [existing, modify — align with design tokens]
│
├── content/
│   ├── _index.md                          [new — homepage hero content in frontmatter]
│   ├── about.md                           [existing, modify — update to Nicolas's bio]
│   ├── contact.md                         [existing, review]
│   ├── posts/
│   │   ├── _index.md                      [new — posts list page config]
│   │   └── {slug}/                        [new structure — page bundles]
│   │       ├── index.md                   [post content + core/pearl frontmatter]
│   │       └── evidence.yaml              [optional pearl trail sidecar]
│   ├── patterns/                          [new — skill pattern content section]
│   │   ├── _index.md                      [new — patterns list page config]
│   │   └── {slug}.md                      [leaf files for each pattern]
│   └── timeline/                          [new — timeline content section]
│       ├── _index.md                      [new — timeline list page config]
│       └── {slug}.md                      [leaf files for each moment]
│
├── layouts/
│   ├── _default/
│   │   ├── baseof.html                    [existing, modify — new nav/footer/fonts]
│   │   ├── list.html                      [existing, modify]
│   │   └── single.html                    [existing, modify]
│   ├── index.html                         [existing, modify — reads hero from content/_index.md]
│   ├── posts/
│   │   ├── list.html                      [new — post archive page]
│   │   └── single.html                    [new — post detail with evidence trail]
│   ├── patterns/
│   │   ├── list.html                      [new — pattern gallery page]
│   │   └── single.html                    [new — pattern detail page]
│   ├── timeline/
│   │   └── list.html                      [new — timeline page]
│   ├── 404.html                           [existing]
│   ├── partials/
│   │   ├── head.html                      [existing, modify — add Google Fonts]
│   │   ├── meta.html                      [existing]
│   │   ├── nav.html                       [existing, modify — redesign to DESIGN-SPEC]
│   │   ├── footer.html                    [existing, modify — redesign to DESIGN-SPEC]
│   │   ├── hero.html                      [new — hero section partial]
│   │   ├── post-list-item.html            [new — replaces post-tile.html]
│   │   ├── pattern-card.html              [new — pattern card with mini-graph]
│   │   ├── timeline-moment.html           [new — timeline moment partial]
│   │   ├── pearl-meta.html                [new — shared pearl field renderer]
│   │   ├── confidence-bar.html            [new — confidence bar component]
│   │   ├── section-heading.html           [new — mono uppercase section label]
│   │   ├── evidence-trail.html            [new — collapsible evidence trail]
│   │   └── pagination.html                [existing]
│   └── shortcodes/
│       └── imgc.html                      [existing]
│
├── static/
│   ├── css/                               [existing — Vite output, auto-generated]
│   ├── favicon/                           [existing, modify — replace TailBliss branding]
│   └── images/
│       ├── og-images/                     [existing — static, no processing needed]
│       └── site-logo.svg                  [existing, replace with ndb logo]
│
├── _bmad-output/                          [existing — planning artifacts, not deployed]
│   ├── 01-prd-skill-discovery-system.md
│   ├── planning-artifacts/
│   │   ├── architecture.md                [this document]
│   │   └── ux-design-specification.md
│   └── implementation-artifacts/
│       ├── DESIGN-SPEC.md
│       ├── design-tokens.css              [spec reference — not processed by Vite]
│       ├── sprint-status.yaml
│       ├── portfolio-home-v4.html         [prototype reference]
│       ├── post-page.html                 [prototype reference]
│       ├── backoffice-v2.html             [prototype reference — out of scope]
│       └── lobby-v2.html                  [prototype reference — out of scope]
│
└── public/                                [existing — Hugo build output, gitignored]
```

### Image Location Rule

| Image Type | Location | Rationale |
|------------|----------|-----------|
| Site logo, favicon, OG images | `static/images/`, `static/favicon/` | Served as-is, no processing needed |
| Content images (posts, pages) | `assets/images/` or inside page bundles | Hugo image processing (resize, WebP via `imgc`) |
| Author photo | `assets/images/global/author.webp` | May need processing for responsive sizes |

### Homepage Content Pattern

`content/_index.md` provides hero content via frontmatter — the template reads `.Params`, no hardcoded strings:

```yaml
---
title: "Nicolas de Barquin"
description: "Portfolio & Skills Repository"
hero:
  tagline: "Capture knowledge pearls first..."
  description: "Building a living knowledge graph..."
---
```

`layouts/index.html` reads `{{ .Params.hero.tagline }}` instead of hardcoding text. This makes content editable without touching templates.

### Requirements to Structure Mapping

| Requirement / Component | Primary Location | Related Files |
|--------------------------|-----------------|---------------|
| Hero section (DESIGN-SPEC §6.1) | `layouts/index.html` | `partials/hero.html`, `content/_index.md` |
| Navigation (DESIGN-SPEC §6.2) | `partials/nav.html` | `assets/js/darkmode.js` |
| Post list (DESIGN-SPEC §6.3) | `layouts/posts/list.html` | `partials/post-list-item.html`, `partials/pearl-meta.html` |
| Timeline (DESIGN-SPEC §6.4) | `layouts/timeline/list.html` | `partials/timeline-moment.html`, `content/timeline/` |
| Pattern gallery (DESIGN-SPEC §6.5) | `layouts/patterns/list.html` | `partials/pattern-card.html`, `content/patterns/` |
| Post detail (DESIGN-SPEC §6.6) | `layouts/posts/single.html` | `partials/evidence-trail.html`, `partials/confidence-bar.html` |
| Footer (DESIGN-SPEC §6.7) | `partials/footer.html` | — |
| Design tokens (D2.1) | `assets/css/design-tokens.css` | `assets/css/main.css` |
| Content contract (D1.1) | `archetypes/posts/` | `content/posts/{slug}/index.md` |

### Architectural Boundaries

**Content Boundary (Phase 0):**
- Content in = Hugo frontmatter + markdown (manually authored)
- Content out = static HTML via Hugo build → Netlify CDN
- No API, no services, no database within this repo

**Data Flow:**
```
content/*.md + evidence.yaml → Hugo templates → public/ → Netlify CDN
                                    ↑
assets/css/design-tokens.css → Vite → static/css/ → Hugo reads
```

**`_bmad-output/` safety:** Hugo ignores directories starting with `_` by default. Do not add `_bmad-output/` to Hugo's mount config — prototypes must never leak into production.

### File Removal List

**Remove (TailBliss defaults — recommend as first epic story):**
- 14 sample posts: `content/posts/blog-post-{1-7}.md`, `content/posts/news-post-{1-7}.md`
- Unused partials: `layouts/partials/newsletter.html`, `layouts/partials/posts-template.html`, `layouts/partials/post-tile.html`
- TailBliss branding: `static/images/tailbliss-*.png/svg/webp` (5 files), `static/images/sample-logo.svg`
- TailBliss sample images: `assets/images/pages/tailbliss-rocket-indigo.png`, `assets/images/pages/hacktoberfest.jpg`
- Unused platform configs: `Dockerfile`, `nginx.conf`, `nixpacks.toml`, `cloudflare.md`, `theme.toml`
- Review: `content/prose.md` (likely not needed)

**Audit (first story):**
- `assets/css/style.css` — merge useful content into `main.css` or remove entirely

### Epic Story Sequencing Recommendation

**Story 1: Remove TailBliss defaults** — Clean slate before building. Remove sample posts, unused partials, branding images, unused config files. Audit `style.css`. Verify `pnpm run test` passes with empty content.

Subsequent stories build on the clean base: design tokens → nav → hero → posts → timeline → patterns → footer.

### Pattern Verification Checklist (updated)

- [ ] All optional pearl fields use `{{ with }}` or `{{ if }}` guards
- [ ] No custom CSS classes for layout/spacing (Tailwind utilities only)
- [ ] All new partials follow kebab-case naming
- [ ] Post content uses page bundle structure (not leaf file)
- [ ] Comments reference DESIGN-SPEC section where applicable
- [ ] `pnpm run test` passes (Hugo build validation)
- [ ] Test `hugo new content posts/test-post` creates page bundle (not flat file)
- [ ] Static assets in `static/`, content images in `assets/` or page bundles

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:** All 8 decisions (D1.1–D3.2) are mutually compatible. No contradictions between CSS pipeline (Vite), content model (page bundles), interactivity scope (Alpine.js moderate), and deployment (Netlify from main). The Vite → static/css/ → Hugo pipeline is explicitly documented to prevent Hugo Pipes confusion.

**Pattern Consistency:** Naming conventions (kebab-case partials, camelCase frontmatter/vars), template guards (`{{ with }}`/`{{ if }}`), partial interfaces (`.` vs `dict` with `"page"` key), and comment conventions are all internally consistent with examples and anti-patterns documented.

**Structure Alignment:** Project tree maps 1:1 to architectural decisions. Directory archetype supports page bundles (D1.1). Content sections support patterns/timeline (D1.2). CSS import chain supports token strategy (D2.1).

### Requirements Coverage ✅

All Phase 0 requirements covered. FR-2 (pearl management) structurally supported via frontmatter schema and evidence sidecar. FR-5 and FR-6 supported via display-only components (confidence bar, pattern cards, timeline). FR-1, FR-3, FR-4, FR-7 explicitly deferred to BackOffice repo. NFR-3 (evidence-based) structurally supported via evidence trail. Content contract bridges both apps.

### Implementation Readiness ✅

8 architectural decisions with rationale, code examples, and anti-patterns. Complete project tree with annotations. 9 agent enforcement rules. 8-item verification checklist. Build pipeline explicitly documented (Vite, NOT Hugo Pipes).

### Gaps Identified

**Important:**
1. **Mobile-first convention** — Tailwind defaults to mobile-first. Document as explicit convention: write base styles for mobile, use `sm:`, `md:`, `lg:` for wider screens.
2. **`hugo.yaml` changes** — New content sections (patterns, timeline) may need menu items and taxonomy adjustments. Address during implementation per-story.

**Nice-to-Have:**
3. **Accessibility** — Use semantic HTML (`nav`, `main`, `article`, `section`, `footer`). Add alt text to all images. No dedicated accessibility audit for Phase 0 — address per-component.

### Architecture Completeness Checklist

**✅ Requirements Analysis**
- [x] Project context analyzed with Phase 0 scope boundary
- [x] Scale and complexity assessed (12+ components, Phase 0 = thin slice)
- [x] Technical constraints identified (locked tech stack, Graphiti risk)
- [x] 12 cross-cutting concerns mapped

**✅ Architectural Decisions**
- [x] 8 decisions documented (D1.1–D3.2) with rationale and code examples
- [x] Technology stack fully specified with versions
- [x] Content contract defined (frontmatter schema + evidence sidecar)
- [x] CSS integration strategy specified (Vite pipeline, import order)

**✅ Implementation Patterns**
- [x] Naming conventions: kebab-case partials, camelCase frontmatter/vars
- [x] Structure patterns: page bundles vs leaf files with promotion path
- [x] Template patterns: guards, partial interfaces, comment conventions
- [x] 9 enforcement rules + 8-item verification checklist + anti-patterns

**✅ Project Structure**
- [x] Complete directory tree with [existing/new/modify/remove] annotations
- [x] Requirements mapped to specific files and directories
- [x] File removal list explicit (TailBliss defaults)
- [x] Image location rule, homepage content pattern documented

### Architecture Readiness Assessment

**Overall Status:** READY FOR IMPLEMENTATION

**Confidence Level:** High — all Phase 0 decisions are documented, patterns are concrete with examples, and the scope boundary is clear.

**Key Strengths:**
- Clear Phase 0 boundary prevents over-architecture
- Content contract bridges two apps without coupling
- Every pattern has code examples AND anti-patterns
- Verification checklist enforces consistency across stories

**Areas for Future Enhancement:**
- Responsive layout details (mobile-first is implicit via Tailwind, make explicit per-story)
- Accessibility audit (Phase 1 or when content stabilizes)
- Performance budget (after Netlify deployment baseline established)

### Implementation Handoff

**AI Agent Guidelines:**
- Follow all architectural decisions exactly as documented
- Use implementation patterns consistently across all components
- Respect project structure and boundaries
- Refer to this document for all architectural questions
- Run verification checklist before marking any story complete

**First Implementation Priority:**
Story 1: Remove TailBliss defaults → clean slate for design implementation.
