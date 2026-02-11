# ndb Visual Charter — Design Specification

**Date:** 2026-02-10
**Status:** Phase 0 foundation locked
**Token file:** `shared/tokens/design-tokens.css`
**Prototypes:** `portfolio-home-v4-final.html`, `backoffice-v2.html`

---

## Table of Contents

1. Architecture Overview
2. Typography
3. Color System
4. Spacing & Density
5. Borders & Radii
6. Portfolio (Hugo) — Layout & Components
7. BackOffice (Svelte) — Layout & Components
8. Lobby Layer
9. Interaction Patterns
10. Design Principles
11. Decisions Log
12. Open Questions

---

## 1. Architecture Overview

### Dual Application, Single Design Language

```
project-root/
├── shared/tokens/
│   └── design-tokens.css       ← single source of truth (CSS custom properties)
├── portfolio/                   ← Hugo static site (PUBLIC)
│   ├── assets/css/main.css     ← @import shared tokens
│   └── <html class="light">
├── backoffice/                  ← Svelte web app (PRIVATE)
│   ├── src/app.css             ← @import shared tokens
│   └── <html class="dark">
└── DESIGN-SPEC.md              ← this file
```

### Theme Model

**Theme is fixed. No toggle.**

| Application | Theme | Class | Rationale |
|---|---|---|---|
| Portfolio | Light | `.light` | Public gallery. Respect OS preference for contrast only. |
| BackOffice | Dark | `.dark` | Private workshop. Fixed dark. |

Two rooms in the same building. Same vocabulary (tokens), different density (scale), different mood (theme).

### Technology Stack

| Component | Technology | Notes |
|---|---|---|
| Portfolio | Hugo + TailBliss + TailwindCSS 4.1 | Static, markdown-native, OKLCH already configured |
| BackOffice | Svelte + Vite + TailwindCSS 4.1 | Reactive UI, containerized (Docker) |
| Shared tokens | CSS custom properties (OKLCH) | One file, imported by both apps |
| Graph viz | D3.js (Phase 0 static) → temporal force-directed | Custom SVG/Canvas, full style control |
| Diagrams | Mermaid | Text-based, renders in both Hugo and Svelte |

---

## 2. Typography: "The Archivist"

### Font Stack

| Role | Font | Weight(s) | Fallback | Google Fonts |
|---|---|---|---|---|
| Heading | Literata | 400, 500, 700 | Georgia, Times New Roman, serif | `Literata:opsz,wght@7..72,400;7..72,500;7..72,700` |
| Body | Hanken Grotesk | 400, 500, 600, 700 | system-ui, sans-serif | `Hanken+Grotesk:wght@400;500;600;700` |
| Mono | Commit Mono | 400, 700 | JetBrains Mono, Fira Code, monospace | `Commit+Mono:wght@400;700` |

### Rationale

- **Literata** — designed for Google Books. Optical sizing shifts character between display and body sizes. Warm serifs, reading-optimized. Quiet authority without stuffiness. Resonates with "de Barquin" identity.
- **Hanken Grotesk** — geometric but friendly. Excellent x-height for small sizes in dense dark UI. Clean at 11-14px range where most BackOffice text lives.
- **Commit Mono** — even character width. Terminal familiarity. Grounds the BackOffice in tool-oriented identity. All metadata, timestamps, labels, code.

### Font Role Mapping

| Element | Font | Style | Both apps? |
|---|---|---|---|
| Name / hero title | Heading, 700 | Normal | Portfolio |
| Article/post title | Heading, 700 | Normal | Both |
| Pattern card title | Heading, 700 | Normal | Both |
| Anagnorisis quote | Heading, 400 | Italic | Portfolio |
| Compose draft title | Heading, 700 | Normal | BackOffice |
| Body paragraphs | Body, 400 | Normal | Both |
| UI labels, nav links | Body, 400–600 | Normal | Both |
| Nudge prompts | Body, 400 | Italic | BackOffice |
| Section labels | Mono, 400 | Uppercase | Both |
| Stats, metadata, counts | Mono, 400 | Normal | Both |
| Timestamps | Mono, 400 | Normal | Both |
| Code blocks, TOML | Mono, 400 | Normal | Both |
| Confidence values | Mono, 500–700 | Normal | Both |
| Freshness heartbeat | Mono, 400 | Normal | Both |
| Tags/labels (pills) | Mono, 400 | Normal | BackOffice |
| Nav logo ("ndb") | Heading, 500 | Normal | Portfolio |
| Footer trust line | Mono, 400 | Normal | Portfolio |

### Type Scale — Dual Density

**Portfolio (.light) — generous, reading-optimized:**

| Token | Size | Line-height | Use |
|---|---|---|---|
| `--text-display` | 40px (2.5rem) | 1.15 | Hero name |
| `--text-h1` | 28px (1.75rem) | 1.3 | Page titles |
| `--text-h2` | 22px (1.375rem) | 1.3 | Section titles |
| `--text-h3` | 20px (1.25rem) | 1.3 | Article titles in lists |
| `--text-h4` | 17px (1.0625rem) | 1.25 | Pattern card titles |
| `--text-body` | 15px (0.9375rem) | 1.7 | Article text |
| `--text-small` | 13px (0.8125rem) | 1.6 | Card descriptions |
| `--text-meta` | 12px | 1.5 | Mono metadata |
| `--text-micro` | 11px | 1.5 | Timestamps, quiet stats |
| `--text-nano` | 10px | — | Uppercase section labels |

**BackOffice (.dark) — compact, density-optimized:**

| Token | Size | Line-height | Use |
|---|---|---|---|
| `--text-display` | 28px (1.75rem) | 1.2 | Mode headers (if needed) |
| `--text-h1` | 22px (1.375rem) | 1.25 | Compose draft title |
| `--text-h2` | 18px (1.125rem) | 1.25 | Panel titles |
| `--text-h3` | 16px (1rem) | 1.25 | Card titles |
| `--text-h4` | 14px (0.875rem) | 1.4 | Sub-headers |
| `--text-body` | 14px (0.875rem) | 1.5 | Primary UI text |
| `--text-small` | 13px (0.8125rem) | 1.4 | Pearl titles, list items |
| `--text-meta` | 12px | 1.5 | Mono metadata |
| `--text-micro` | 11px | 1.5 | Labels, timestamps |
| `--text-nano` | 10px | — | Uppercase micro-labels |

### Line Width (Measure)

| Context | Max width | Token |
|---|---|---|
| Portfolio body text | 65ch | `--measure-body` |
| Portfolio hero description | 54ch | `--measure-narrow` |
| BackOffice body text | 80ch | `--measure-body` |
| BackOffice panels | 100% | `--measure-wide` |

---

## 3. Color System (OKLCH)

### Semantic Colors — The Pearl System

Colors have *meaning*. They represent the knowledge system, not generic UI.

| Token | Dark value | Light value | Meaning |
|---|---|---|---|
| `--pearl` | `oklch(0.95 0.02 90)` | `oklch(0.35 0.04 90)` | Knowledge pearl — raw data, neutral |
| `--convergence` | `oklch(0.75 0.15 75)` | `oklch(0.52 0.14 75)` | Convergence — old idea meets new work (amber) |
| `--pattern` | `oklch(0.65 0.25 25)` | `oklch(0.48 0.18 25)` | Skill pattern — highlighted subgraph (coral) |
| `--temporal` | `oklch(0.70 0.12 240)` | `oklch(0.48 0.10 240)` | Time markers — timeline, dates (blue) |
| `--frontier` | `oklch(0.40 0.01 260)` | `oklch(0.75 0.01 260)` | Unexplored, quiet tertiary (gray) |
| `--fresh` | `oklch(0.75 0.18 145)` | `oklch(0.42 0.14 145)` | Fresh capture, strong signal (green) |

### Surface & Text

| Token | Light | Dark |
|---|---|---|
| `--surface` | `oklch(0.97 0.005 260)` | `oklch(0.15 0.02 260)` |
| `--surface-elevated` | `oklch(1.0 0 0)` | `oklch(0.20 0.02 260)` |
| `--surface-recessed` | — | `oklch(0.12 0.02 260)` |
| `--text-primary` | `oklch(0.15 0.02 260)` | `oklch(0.93 0.01 260)` |
| `--text-secondary` | `oklch(0.45 0.02 260)` | `oklch(0.65 0.02 260)` |
| `--text-tertiary` | `oklch(0.60 0.015 260)` | `oklch(0.40 0.01 260)` |
| `--border` | `oklch(0.90 0.005 260)` | `oklch(0.25 0.02 260)` |
| `--border-subtle` | `oklch(0.93 0.005 260)` | `oklch(0.22 0.02 260)` |

### Confidence Thresholds

| Range | Color token | Semantic |
|---|---|---|
| 85%+ | `--fresh` (green) | Strong, well-evidenced |
| 70–84% | `--convergence` (amber) | Solid, growing |
| <70% | `--temporal` (blue) | Emerging, needs more evidence |

### Color Rules

- **No gradients.** Flat only.
- **No shadows.** Elevation via `surface` vs. `surface-elevated` borders only.
- **No decoration.** Color is information, not ornamentation.
- Semantic colors appear as: dots, bars, text accents, border-left accents, SVG nodes. Never as large-area backgrounds (except `surface-recessed` for context bar / quick capture).

---

## 4. Spacing & Density

### Base Unit: 4px

| Token | Value |
|---|---|
| `--space-1` | 4px |
| `--space-2` | 8px |
| `--space-3` | 12px |
| `--space-4` | 16px |
| `--space-5` | 20px |
| `--space-6` | 24px |
| `--space-8` | 32px |
| `--space-10` | 40px |
| `--space-12` | 48px |
| `--space-16` | 64px |

### Density Comparison

| Property | Portfolio (light) | BackOffice (dark) |
|---|---|---|
| Page max-width | 920px | 100% (full viewport) |
| Page padding-x | 32px | 20px |
| Section padding-y | 44px | 20px |
| Card padding | 24px | 16–20px |
| Card gap | 16px | 16–20px |
| List item padding | 24px vert | 8px vert |
| Nav height | 54px | 38px (context) + 40px (tabs) |
| Quick capture height | — | 52px |

---

## 5. Borders & Radii

| Token | Value | Use |
|---|---|---|
| `--radius-sm` | 2px | Tags, pills, labels |
| `--radius-md` | 3px | Cards, inputs, panels |
| `--radius-lg` | 4px | Max for rectangular elements |
| `--radius-full` | 9999px | Dots, nodes, circles only |

**Rule:** Maximum 4px radius on any rectangular element. No bubbly aesthetics. `radius-full` reserved for pearl dots (6px ⌀), timeline nodes (11px ⌀), mode indicator circles (10px ⌀), and status dots.

---

## 6. Portfolio (Hugo) — Layout & Components

### Page: Home (`/`)

**Structure:** Tasting menu. Each section hooks to a deeper page.

```
┌─────────────────────────────────┐
│ Nav: "ndb" left, links right    │  sticky, blurs on scroll
├─────────────────────────────────┤
│ Hero: Name, description, stats, │  Literata 40px, max 18ch
│       freshness heartbeat       │
├─────────────────────────────────┤
│ § Latest posts (3)              │  → All posts
├─────────────────────────────────┤
│ § Skill patterns (4)            │  → All patterns
│   [2×2 grid, cards with         │
│    mini-graph + confidence]     │
├─────────────────────────────────┤
│ § Moments of recognition (10)   │  → Full timeline
│   [vertical spine, year groups, │
│    colored dots, italic quotes] │
├─────────────────────────────────┤
│ Footer: name, location, links,  │
│         trust line              │
└─────────────────────────────────┘
```

### Component: Navigation

- Sticky top, 54px height
- Background: transparent at top, blurs (`backdrop-filter: blur(10px)`) + semi-transparent + border on scroll
- Left: "ndb" in Literata 18px weight 500
- Right: Posts, Patterns, Timeline, About — Hanken Grotesk 14px
- Active link: text-primary, weight 600. Others: text-secondary, weight 400
- Max-width: 920px centered

### Component: Hero

- Name: Literata 40px, weight 700, letter-spacing -0.02em, max 18ch
- Description: Hanken Grotesk 17px, line-height 1.7, text-secondary, max 54ch
- Stats: Commit Mono 12px, text-tertiary, flex with `·` separators
- Freshness heartbeat: Commit Mono 11px, frontier color, 8px below stats
  - Format: `last pearl captured {time} ago`
  - Source: Hugo build-time from API, or client-side fetch for live value

### Component: Post List Item

- Title: Literata 20px, weight 700, letter-spacing -0.01em
- Date: Commit Mono 12px, text-tertiary, right-aligned, flex-shrink 0
- Excerpt: Hanken Grotesk 15px, line-height 1.7, text-secondary, max 65ch
- Meta row: Commit Mono 12px, text-tertiary — `{readTime} · {pearlCount} pearls · {tags}`
  - Pearl count in `--convergence` color
- Separator: 1px `--border-subtle` between items. None after last.
- Each item is a link (cursor pointer)

### Component: Pattern Card (2×2 grid)

- Border: 1px `--border`, radius 3px, background `--surface-elevated`
- Top row: Mini constellation SVG (72×44px) left + title/meta right
  - Title: Literata 16px, weight 700
  - Meta: Commit Mono 11px, text-tertiary — `{pearls} pearls · {span} · {trajectory}`
  - Trajectory in `--convergence` color
- Description: Hanken Grotesk 13px, line-height 1.6, text-secondary
- Confidence bar: label "CONFIDENCE" (Commit Mono 10px uppercase), 3px bar, percentage
- Mini constellation: procedural SVG, nodes connected when distance < 22px, color by confidence threshold

### Component: Anagnorisis Timeline

- Section label: "MOMENTS OF RECOGNITION" + "Full timeline →"
- Vertical spine: 1px line, `--timeline-line` color
- Year markers: 11px ring (2px stroke, `--temporal` color, white fill) on spine
  - Year text: Commit Mono 13px, weight 700, `--temporal` color
- Moment dots: 5px filled circle on spine, color = semantic color of the moment
- Moment text: Literata 14px, weight 400, italic, text-secondary
- Spacing: 24px between year groups, 10px between moments in same year
- Order: recent first (2025 → 2015)
- Content: 10 curated moments (BackOffice `featured_on_homepage: true` flag)
- Each quote is a link to related post/pattern

### Component: Section Heading

- Commit Mono 12px, weight 400, uppercase, letter-spacing 0.08em, text-tertiary
- Optional right-aligned "All posts →" link (same style)
- Flex row, space-between

### Component: Footer

- Top row: Name (Literata 15px, 500) + Location (Hanken Grotesk 13px, text-tertiary) left, links (Commit Mono 12px, text-tertiary) right
- Trust line: Commit Mono 11px, frontier color — "Patterns are extracted from documented work, not self-assessment. Confidence reflects evidence density. Built with Hugo, powered by curiosity."

### Pages Status

| Page | Content | Status |
|---|---|---|
| `/posts/` | Full post archive, tags, search | Structure defined, not prototyped |
| `/posts/{slug}` | Single post with evidence trail | **Prototyped** |
| `/patterns/` | Full patterns gallery | Not prototyped |
| `/patterns/{slug}` | Single pattern detail, pearl list | Not prototyped |
| `/timeline/` | Full interactive timeline | Not prototyped |
| `/about/` | Profile + link to patterns | Not prototyped |

### Page: Single Post (`/posts/{slug}`)

**Structure:** Reading-first, narrower than homepage. Evidence trail collapsed by default.

```
┌─────────────────────────────────────────┐
│ Nav (same as homepage, 920px)            │
├───────────────────────────────┐         │
│ ← Posts                       │ 700px   │
│                               │         │
│ Title (Literata 34px)         │         │
│ date · read time · pearls ·   │         │
│ timespan                      │         │
│ [tag] [tag] [tag]             │         │
│                               │         │
│ Article body (Literata 17px)  │         │
│ ...                           │         │
│                               │         │
│ ─────────────                 │         │
│ ▾ Evidence trail (collapsed)  │         │
│                               │         │
│ Connected patterns            │         │
│ [card]  [card]                │         │
│                               │         │
│ ─────────────                 │         │
│ ← Previous    Next →          │         │
├───────────────────────────────┘         │
│ Footer (same as homepage, 920px)         │
└─────────────────────────────────────────┘
```

**Content max-width: 700px** (narrower than homepage's 920px). Long-form reading needs a tighter measure. Nav and footer remain at 920px.

#### Component: Article Header

- Back link: "← Posts" — Commit Mono 12px, text-tertiary
- Title: Literata 34px, weight 700, letter-spacing -0.02em, max 22ch
- Meta row: Commit Mono 12px, text-tertiary, flex wrap
  - Format: `{date} · {readTime} · {pearlCount} pearls · {timespan}`
  - Pearl count in `--convergence` color
  - Timespan: "March 2024 → January 2025" — shows duration of learning, not just publication date
- Tags: pills — Commit Mono 11px, text-secondary, elevated bg, border, radius 2px

#### Component: Article Body

- **Font: Literata 17px, line-height 1.8, letter-spacing 0.005em** — reading font at optimal size. Optical sizing activates here.
- Paragraph spacing: 20px margin-bottom
- **H2: Hanken Grotesk 16px, weight 600** — sans-serif within serif body. Creates rhythm: serif = narrative, sans = structure. 36px margin-top, 14px margin-bottom.
- **Blockquote:** Literata italic, `--convergence` 3px left border, 20px left padding
- **Inline code:** Commit Mono 0.9em, code-bg background, 2px 6px padding, radius 2px
- **Code blocks:** Commit Mono 14px, recessed background, border, 16px padding, full width
- **Images:** full 700px width, caption in Commit Mono 11px below

| Element | Font | Size | Weight | Notes |
|---|---|---|---|---|
| Body text | Literata | 17px | 400 | line-height 1.8, letter-spacing 0.005em |
| H2 | Hanken Grotesk | 16px | 600 | Sans-serif for structural markers |
| Blockquote | Literata | 17px | 400 italic | Convergence left border |
| Inline code | Commit Mono | 0.9em | 400 | Background pill |
| Code block | Commit Mono | 14px | 400 | Full-width recessed box |
| Caption | Commit Mono | 11px | 400 | Below images |

#### Component: Evidence Trail (collapsible)

- **Collapsed by default.** Progressive disclosure: article stands alone. Curious visitors expand.
- Toggle: "EVIDENCE TRAIL" label + `{pearlCount} pearls · {timespan}` (convergence) + chevron ▾
- Intro text (expanded): Hanken Grotesk 13px, text-tertiary — "The pearls that built this post, in chronological order. Each is a timestamped note captured during the learning process — raw, unedited, as it happened."
- Container: `--timeline-bg` background, border, radius 3px, 20px padding
- Spine: same visual language as homepage timeline (1px, colored dots)
- Per pearl:
  - Dot (5px): `--fresh` (new insight), `--convergence` (connection made), `--pearl` (raw capture)
  - Date: Commit Mono 11px, `--temporal`
  - Content: Hanken Grotesk 13px, text-secondary, line-height 1.55
- Spacing: 14px between pearls
- Order: **chronological (oldest first)** — opposite of homepage timeline. Here the narrative builds forward.

#### Component: Connected Patterns

- Section label: "CONNECTED PATTERNS"
- Horizontal flex of pattern cards (elevated bg, border, radius 3px)
  - Title: Literata 15px, weight 700
  - Confidence bar: label + 3px bar + percentage (same as homepage)
- Links to `/patterns/{slug}`

#### Component: Post Navigation

- Divider: 1px `--border`
- Two columns: previous (left) / next (right), max 45% width
- Label: "← Previous" / "Next →" — Commit Mono 11px, text-tertiary
- Title: Literata 14px, weight 500, text-secondary

---

## 7. BackOffice (Svelte) — Layout & Components

### Two-Layer Architecture

```
LOBBY (login screen / session menu)
  ├── Continue (resume last session)
  ├── Load (project/snapshot picker)
  ├── Options (preferences, import sources, API config)
  └── Account (logout)
        │
        ▼  [Enter workshop]
WORKSHOP SESSION (the four modes)
  ┌──────────────────────────────────────┐
  │ Context bar: stats · freshness · ⏻   │  38px, recessed bg
  ├──────────────────────────────────────┤
  │ Mode tabs: ● CAPTURE ○ EXPLORE ...   │  40px, surface bg
  ├──────────────────────────────────────┤
  │                                      │
  │  Center workspace                    │  fills remaining space
  │  (transforms per active mode)        │
  │                                      │
  ├──────────────────────────────────────┤
  │ Quick capture: [input] 🎤 📷 📎      │  52px, recessed bg
  └──────────────────────────────────────┘
        │
        ▼  [End session]
LOBBY (auto-save snapshot, return to menu)
```

### Component: Context Bar

- Fixed top, full width, 38px height
- Background: `--surface-recessed`
- Left: stats in Commit Mono 12px, text-secondary — `847 pearls · 12 projects · 4 patterns`
- Right: freshness + end session
  - `+4 today (3 yesterday)` — today count in `--fresh`, yesterday in text-tertiary
  - `· last capture 2m ago` — text-tertiary
  - `· ⏻ End session` — text-tertiary, cursor pointer
- End Session action: auto-save all open work, create snapshot, transition to Lobby

### Component: Mode Tabs

- Horizontal, below context bar, surface background
- Border-bottom: 1px `--border`
- Each tab: Commit Mono 12px, uppercase, letter-spacing 0.06em
- Active: `--pearl` color, 2px solid bottom border, filled circle (●)
- Inactive: text-tertiary, transparent bottom border, empty circle (○)
- Notification state: inactive tab with subtle dot inside circle (◉) — indicates "something ready" (e.g., composition readiness, new pattern detected). This is a state indicator, not a notification.

### Mode Indicator Circles (SVG, 10px)

| State | Visual | Meaning |
|---|---|---|
| Active | ● 4px filled circle, pearl color | Currently in this mode |
| Inactive | ○ 3.5px radius ring, 1px stroke, text-tertiary | Available mode |
| Inactive + signal | ○ ring + 1.5px filled dot inside, convergence color | Something ready to review in this mode |

### Component: Quick Capture Bar

- Fixed bottom, full width (except nav column in v1 — now full width), 52px height
- Background: `--surface-recessed`
- Left label: "QUICK" in Commit Mono 10px, uppercase, text-tertiary
- Input: textarea 1 row, Hanken Grotesk 13px, elevated background, border
  - Placeholder: "Drop a pearl from anywhere... (Enter to capture)"
  - Enter = capture + clear. Shift+Enter = newline.
- Right: 🎤 📷 📎 buttons, 30×30px, elevated bg, border, radius 3px
- This bar is **always visible** regardless of mode. Capture is sacred.

### Mode: Capture (fully prototyped)

**Layout:** Two columns — main input left (flex 1), validation queue right (340px fixed).

**Left column:**
- Nudge prompt: Hanken Grotesk 14px, italic, text-tertiary. Rotates on each mode switch.
  - Pool: "What just happened?", "What did you notice?", "Quick snapshot — no editing needed", "Drop it here, sort it later", "Half-formed is perfect", "What's on your mind?"
- Main textarea: 6 rows, Hanken Grotesk 14px, elevated bg, border
  - Placeholder: "Type, paste, drag a file, or just start talking..."
- Attachment buttons: 🎤 Voice, 📷 Photo, 📎 File, 🔗 URL — Commit Mono 11px, elevated bg, border
- Capture button: right-aligned, Commit Mono 12px bold
  - Active (text present): pearl background, recessed text
  - Inactive (empty): border background, text-tertiary text
- Recent pearls list:
  - Label: "RECENT PEARLS" (standard section label)
  - Items: colored dot (6px, fresh/pearl/convergence) + title (Hanken Grotesk 13px) + time (Commit Mono 11px, text-tertiary right)
  - Separator: 1px border-subtle between items

**Right column (Validation Queue):**
- Panel: elevated bg, border, radius 3px, 16px padding
- Label: "VALIDATION QUEUE"
- Description: Hanken Grotesk 12px, text-tertiary — "AI-proposed labels. Accept, edit, or reject."
- Per pearl:
  - Header: "Pearl #{id}" left (Mono 11px, text-tertiary), confidence % right (colored by threshold)
  - Content preview: Hanken Grotesk 13px, text-secondary, line-height 1.5
  - AI labels: tag pills — Commit Mono 11px, convergence color, surface bg, border, radius 2px
  - Actions: Accept (fresh border), Edit (convergence border), Reject (pattern border) — Commit Mono 11px, outline buttons
- Avg confidence bar at bottom: label + 3px bar + percentage

### Mode: Explore (skeleton prototyped)

**Layout:** Vertical — NL query bar top, graph viewport center (flex 1), info bar bottom.

- NL query bar: Hanken Grotesk 14px, italic, temporal color, elevated bg — functions as both display and input
- Graph viewport: recessed bg, border, minimum 300px height
  - Phase 0: static force-directed graph (D3.js), custom SVG
  - Future: temporal force-directed graph from Neo4j query results
  - Manipulation tools: top-right corner — zoom in, zoom out, fit, reset (28×28px buttons)
  - Phase label: bottom-right, Commit Mono 11px, text-tertiary
- Info bar: Commit Mono 11px — cluster name (convergence color) + pearl count left, graph stats right

### Mode: Compose (skeleton prototyped)

**Layout:** Two columns — pearl source (240px) left, editor (flex 1) right.

- Left: pearl cluster list with draggable items, colored dots, "Add from graph" button (dashed border)
- Right: title (Literata 22px), draft meta (Mono 11px — pearl count, date range), timeline-narrative editor with temporal date markers, Export 7z + Preview buttons bottom

### Mode: Publish (skeleton prototyped)

**Layout:** Single column, max 640px.

- Title + meta
- Preview box: elevated bg, shows post as it will appear on portfolio
- Checklist: Frontmatter, Images, Patterns linked, Preview reviewed — checkboxes with fresh/text-tertiary colors
- Pipeline label: Commit Mono 11px, text-tertiary — `git commit → hugo rebuild → live`
- Publish button: pearl bg, recessed text, Commit Mono 13px bold — "Publish to Portfolio"

---

## 8. Lobby Layer (prototyped)

### Concept

The Lobby is the entry screen. Like a video game main menu: you see it on login and when you End Session. The workshop is always entered *through* the lobby. One workspace, always — no session files, no save/load. Pearls, drafts, and validation queue persist in their own storage (PostgreSQL/Neo4j).

### Layout

```
┌─────────────────────────────────────┐
│                                     │
│            ndb                      │  Literata 28px, centered
│         backoffice                  │  Commit Mono 11px, text-tertiary
│                                     │
│  ┌─────────────────────────────┐    │
│  │ Continue              2h ago│    │  DOMINANT — pearl title, elevated bg
│  │ Last in Capture mode        │    │  workspace state summary
│  │ +4 pearls · 3 validation ·  │    │  colored indicators
│  │ 1 draft                     │    │
│  └─────────────────────────────┘    │
│                                     │
│  [Options]  [Account]  [Logout]     │  3 equal secondary buttons
│                                     │
│  847 pearls · 12 projects · 4 pat  │  quiet stats, centered
│  last capture 2h ago                │  freshness heartbeat
│                                     │
│  ndb backoffice v0.1.0 · phase 0   │  version stamp
│                                     │
└─────────────────────────────────────┘
```

### Component: Continue Card (dominant)

- Max-width: 420px, centered
- Background: `--surface`, border `--border`. On hover: `--surface-elevated`, border `--pearl`
- Title: "Continue" in Literata 18px, weight 700, pearl color
- Timestamp: Commit Mono 11px, text-tertiary, right-aligned — "2 hours ago"
- Subtitle: "Last in {mode} mode" — Hanken Grotesk 13px, text-secondary
- Status indicators (Commit Mono 11px):
  - `+4 pearls today` — `--fresh` (green)
  - `3 awaiting validation` — `--convergence` (amber)
  - `1 draft in progress` — `--temporal` (blue)
- When not authenticated: subtitle becomes "Sign in to enter the workspace"

### Component: Secondary Buttons (equal row)

- Three buttons, flex equal width, max-width 420px total
- Background: `--surface`. On hover: `--surface-elevated`
- Label: Hanken Grotesk 14px, weight 600
- Subtitle: Commit Mono 11px, text-tertiary
- **Options** — "Preferences" — config, import sources, API, display
- **Account** — "Profile" — user profile settings
- **Logout** — "End & exit" — label in `--pattern` (coral), direct action, no submenu
- When not authenticated: all three greyed out (opacity 0.4)

### Login Flow

- When not authenticated, clicking Continue opens an inline login form (not a separate page)
- Form: username + password inputs, pearl-colored "Sign in" button
- Note: "Single-user system · local auth" — honest about the architecture
- After login: Continue card restores workspace state indicators
- No registration flow in UI (single-user, created via CLI or first-run setup)

### What Lives in Options (not in workshop)

- Import source configuration (Obsidian vault paths, Logseq, GitHub repos)
- API keys (Claude, embedding model)
- Display preferences (default mode on session start)
- Training gauge and model version management
- Export/backup settings

### What Lives in Account (not in workshop)

- User profile (name, avatar if any)
- Password change
- Data export (full pearl dump)

### Design Notes

- Background: `--surface-recessed` (darker than workshop `--surface`) — creates visual threshold
- Dark theme, same tokens as workshop
- Vertically + horizontally centered — this is a pause, not a workspace
- No quick capture bar (not in the workshop yet)
- No mode tabs (modes live inside the workshop)
- No nav (lobby IS the root)
- Continue is ~80% of lobby interactions — sized and positioned accordingly

### Why No "Load"

One workspace. No session files. Pearls persist in the database, drafts persist in their store, validation queue persists in its queue. The workspace is always the same room — you walk back in. There's no added value to saving/loading different workspace states. Removed to avoid premature complexity.

---

## 9. Interaction Patterns

### No Animation

No CSS transitions on layout changes. No fade-ins. No slide animations. Snappy, instant. The only exceptions:

- Nav backdrop blur on scroll (functional, not decorative)
- Nav label expand on hover — **REMOVED in v2** (was disruptive). Top tabs only.

### Mode Switching

- Clicking a mode tab instantly replaces center workspace content
- No transition animation
- Quick capture bar persists across all modes
- Context bar persists across all modes
- Nudge prompt rotates when switching TO Capture mode

### Notification Dot (Mode Tab Signal)

- Not a notification. A state indicator.
- Amber dot inside the empty circle of an inactive tab
- Means: "the AI has prepared something in this mode" (e.g., composition readiness score crossed threshold, new pattern detected)
- Follows "nothing pushes, everything pulls" principle
- Disappears when user visits the mode

### Quick Capture

- Always visible. Capture is sacred.
- Enter = capture + clear. Shift+Enter = newline.
- Attachment buttons: voice, photo, file
- This is the "drop a coin in a jar" interaction — zero friction

### End Session

- Located in context bar, top right
- Action: auto-save all work, create snapshot/savepoint, return to Lobby
- Like closing the workshop for the day: tools saved, bench cleaned

### Validation Flow (Capture Mode)

- AI proposes labels and confidence per pearl
- User sees: content preview, proposed labels (pills), confidence %
- User acts: Accept / Edit / Reject
- Accept = pearl ingested to graph with AI labels
- Edit = user modifies labels, pearl ingested with user corrections (training signal)
- Reject = pearl discarded or re-queued
- Every action is a training data point (logged to PostgreSQL)

---

## 10. Design Principles

Summarized from UX specification. These constrain all future decisions.

1. **No animation.** Snappy, reactive. Animation is disabled on the workstation.
2. **No theme toggle.** Light portfolio, dark backoffice. Fixed.
3. **No gradients, no shadows, no decoration.** Color is information.
4. **Typography-forward.** The writing IS the showcase.
5. **Progressive disclosure.** Show what's needed, when it's needed.
6. **No gamification.** Narrative momentum through reflection, not badges.
7. **Confidence as social contract.** Always show evidence density, not self-assessment.
8. **Freshness as trust signal.** "last pearl captured 3h ago" — heartbeat, not badge.
9. **Anti-over-engineering.** Build components as needed. Three similar implementations before abstracting.
10. **Two rooms, one building.** Same tokens, different density, different mood.
11. **Nothing pushes, everything pulls.** State indicators, not notifications.
12. **Workshop/Lobby separation.** Settings and admin never intrude on workflow.
13. **Capture is sacred.** Quick capture bar always visible. Zero friction.

---

## 11. Decisions Log

| Date | Decision | Rationale |
|---|---|---|
| 2026-02-10 | Typography: "The Archivist" | Library warmth, reading-optimized, quiet authority. Literata + Hanken Grotesk + Commit Mono. |
| 2026-02-10 | OKLCH color space | Perceptually uniform. Already in Hugo TailBliss 4.1. |
| 2026-02-10 | Homepage: 3 posts + 4 patterns + 10 moments | Tasting menu, not buffet. Each section hooks to deeper page. |
| 2026-02-10 | Timeline = anagnorisis moments, not events | Recognition quotes trigger more curiosity than accomplishment lists. |
| 2026-02-10 | Timeline visual: v1 style (spine + dots + bare quotes) | Light, tight, progression-focused. No cards, no categories on dots. |
| 2026-02-10 | Timeline order: recent first, grouped by year (not quarter) | Simpler grouping, most interesting content first. |
| 2026-02-10 | Freshness heartbeat | Single line: "last pearl captured 3h ago". Trust + self-incentive. |
| 2026-02-10 | No hover tooltips for meta | Invisible by default breaks progressive disclosure. Always inline. |
| 2026-02-10 | BackOffice: `featured_on_homepage` flag | User curates which 10 moments appear. Editorial choice. |
| 2026-02-10 | No left nav panel | Expandable sidebar was disruptive on mouseover. Top tabs only. |
| 2026-02-10 | Mode indicators: ●/○/◉ circles | Filled = active, empty = inactive, dot-in-circle = signal ready. |
| 2026-02-10 | End Session → top-right context bar | Not a mode, not in nav. Quiet exit, always available. |
| 2026-02-10 | Lobby/Workshop two-layer architecture | Settings, load, account live in Lobby. Workshop is pure workflow. Like a game session menu. |
| 2026-02-10 | No Load/Save sessions | One workspace, always. Pearls/drafts persist in DB, not session files. Removed premature complexity. |
| 2026-02-10 | Logout as direct button | No submenu. Options / Account / Logout as three equal buttons. One click to exit. |
| 2026-02-10 | "Continue" as universal entry label | Works for first connection (proceed to workspace) and return visits (resume). No overthinking. |
| 2026-02-10 | Login = inline form on Continue click | Not a separate page. Single-user local auth. Registration via CLI/first-run. |
| 2026-02-10 | Lobby background: surface-recessed | Darker than workshop surface. Visual threshold between layers. |
| 2026-02-10 | Post body: Literata 17px, line-height 1.8 | Reading font at optimal size. Optical sizing activates. Generous leading for sustained reading. |
| 2026-02-10 | Post H2: Hanken Grotesk (sans) within Literata (serif) body | Serif = narrative, sans = structure. Creates typographic rhythm within article. |
| 2026-02-10 | Post content max-width: 700px | Narrower than homepage (920px). Long-form reading needs tighter measure. Nav/footer stay at 920px. |
| 2026-02-10 | Evidence trail: collapsed by default | Progressive disclosure. Article stands alone. Curious visitors expand to see raw pearls. |
| 2026-02-10 | Evidence trail order: chronological (oldest first) | Opposite of homepage timeline (recent first). Post evidence builds forward as narrative. |
| 2026-02-10 | Post meta includes timespan | "March 2024 → January 2025" shows duration of learning, not just publication date. Unique differentiator. |
| 2026-02-10 | Blockquote: convergence left border | 3px amber accent, Literata italic. Visually distinct from body without heavy decoration. |

---

## 12. Open Questions

| # | Question | Context | Priority |
|---|---|---|---|
| 1 | Remaining portfolio pages | Patterns gallery, full Timeline, About — layout TBD. Posts archive (list/filter). | Phase 0 |
| 2 | Temporal force-directed graph: library or custom? | D3-force with time axis vs. fully custom Canvas renderer. Affects styling control. | Phase 0-1 |
| 3 | Freshness: build-time vs. live fetch? | Hugo static = shows "at build time". Client-side fetch = live but adds dependency. | Phase 0 |
| 4 | Compose mode: inline editor vs. export-only? | Phase 0 spec says export-based. Prototype shows inline editor. Clarify scope. | Phase 0 |
| 5 | Mobile/responsive behavior | Portfolio needs responsive. BackOffice is desktop-only. Portfolio breakpoints? | Phase 0 |
| 6 | Post page: code block styling | Full code blocks not yet prototyped. Syntax highlighting? Language label? Copy button? | Phase 0 |
| 7 | Post page: image handling | Full-width images, captions, lightbox? Or keep simple (img + caption)? | Phase 0 |
| 8 | Graph manipulation tools | Pan, zoom, select, highlight. D3 built-ins or custom controls? | Phase 1 |
| 9 | Mermaid rendering in Compose mode | Mermaid pearls should render visually in compose editor. Integration approach? | Phase 1 |
| 10 | Training gauge location | Spec says "quiet, in context bar". Does it go in context bar or Options? | Phase 1 |
| 11 | Fog of war timing | When to introduce frontier mapping. Spec says 500+ pearls. Verify threshold. | Phase 2 |
