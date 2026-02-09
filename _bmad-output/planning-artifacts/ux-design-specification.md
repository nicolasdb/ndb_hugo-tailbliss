---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7]
inputDocuments:
  - /var/home/nicolas/github/ndb_hugo-tailbliss/_bmad-output/01-prd-skill-discovery-system.md
  - /var/home/nicolas/github/ndb_hugo-tailbliss/README.md
  - /home/nicolas/github/MakerSkillTree/README_fr-geek.md
date: "2026-02-08"
---

# UX Design Specification: ndb_hugo-tailbliss

**Author:** Nicolas
**Date:** 2026-02-08

---

## Executive Summary

### Project Vision

A knowledge-first portfolio system that inverts the traditional portfolio workflow. Users capture knowledge pearls first (private BackOffice app), organize them into narratives, and let skill patterns emerge naturally from documented work over time. The public portfolio (Hugo static site) showcases curated posts, articles, and a "patterns gallery" of extracted skills — each backed by real evidence.

**Two distinct applications, one workflow:**
- **BackOffice App** (dark, functional, private) — capture, explore, compose, curate
- **Public Portfolio** (light, spacious, public) — published posts, skill patterns gallery

**Inspiration References:**
- **MakerSkillTree**: Progressive hex-tile skill tracking with visual motivation — but paper-only, evidence-less, preset domains, no emergence. Provides self-esteem boost but limited real impact.
- **Path of Exile Skill Tree**: Full constellation graph where individual skills are visible as highlighted subgraph patterns extracted from the whole mesh. A skill is NOT a node — it's a PATTERN (subgraph) visible within the full knowledge graph.

### Target Users

**Primary: Nicolas (BackOffice User)**
- Captures knowledge pearls in any format (private, unrestricted)
- Manages data ingestion from existing sources (Obsidian, Logseq, GitHub repos)
- Queries and explores the knowledge graph via natural language
- Composes, refines, and publishes posts from pearl clusters
- Reviews emergent skill patterns for accuracy
- Can export pearl clusters for external curation (no vendor lock-in)

**Secondary: Portfolio Visitors**
- Browse published posts and articles
- Explore curated skill patterns gallery
- Understand growth trajectory through evidence-backed visualizations
- No access to private BackOffice or raw pearls

### Key Design Challenges

1. **Phase 0 Foundation**: Hugo TailBliss site is still generic — needs its own visual charter, identity, and foundational page structure before knowledge system layers on
2. **Graph Visualization as Unknown Territory**: Path of Exile-style constellation + pattern extraction is novel; requires progressive iteration (walking skeleton → simple graph → pattern highlighting → full interaction)
3. **Skill = Subgraph Pattern**: Novel concept requiring custom visualization — highlighting connected evidence clusters within the full pearl mesh
4. **Two-Application Architecture**: Private BackOffice app (functional, data-focused, dark) vs. Public Portfolio website (curated, narrative-focused, light) — distinct UX needs, clear separation of concerns
5. **Pearl Capture Friction & Cognitive Load**: Context switching between creation mode and reporting mode causes procrastination. Must design for zero-friction capture including future edge devices (ESP32-S3)
6. **AI Pipeline Integration**: LLM processing (chunking, labeling, transcription, vision, NL queries) must feel seamless, not like a separate tool

### Design Opportunities

1. **Walking Skeleton Strategy**: Each phase delivers a thin vertical slice of real value
2. **Progressive Graph Exploration**: Start with force-directed layout → add pattern extraction → Path of Exile-style highlighting — each iteration testable independently
3. **Privacy-to-Publishing Flow**: Natural workflow from private pearl → cluster → refined post → published pattern; the UX IS the publishing pipeline
4. **Evidence Visibility**: Every public skill pattern links to real documented work — trust through transparency
5. **Dark Office / Light Gallery**: Instant context awareness through visual identity — no confusion about which space you're in
6. **Export/Curation Freedom**: Pearl clusters exportable as .md bundles for curation in any tool; reimport polished articles — anti-lock-in at the workflow level

---

## System Architecture (UX-Relevant)

### Three-Layer Architecture

```
[CAPTURE]              [ORGANIZE]              [PUBLISH]
Edge devices (later)   BackOffice App          Hugo Portfolio
+ Existing sources     (Svelte, dark theme)    (light theme, static)
├─ Obsidian vaults     ├─ Pearl management     ├─ Posts & articles
├─ Logseq graphs       ├─ Graph explorer       ├─ Patterns gallery
├─ GitHub repos        ├─ NL query interface   ├─ Timeline narrative
├─ ESP32-S3 (future)   ├─ Post composer        └─ Public, curated
└─ TOML/JSON webhook   ├─ Export 7z bundles
                       └─ Private, functional
        │                      │                      │
        └──── API/webhook ─────┘──── git commit .md ──┘
```

### Technology Decisions

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Public Portfolio | Hugo + TailBliss (customized) | Static, fast, markdown-native, cheap hosting |
| BackOffice App | Svelte + FastAPI | Reactive UI, fast iteration, Python ML ecosystem |
| Database | PostgreSQL + Neo4j | Pearl storage + temporal knowledge graph |
| Graph Ingestion | Graphiti (Python) | Temporal tracking, convergence detection |
| AI Processing | Claude API | Chunking, labeling, relationships, NL queries, drafts |
| Edge Devices | Rust + ESP32-S3 (parallel track) | Zero-friction ambient capture, memory-safe embedded |
| Pearl Wire Format | TOML/JSON | Language-agnostic, human-readable |
| Publishing Bridge | Git API (commit .md) | BackOffice → Hugo pipeline, no vendor lock-in |

### AI Processing Pipeline

```
RAW INPUT                    AI PROCESSING                HUMAN CURATION
─────────                    ─────────────                ──────────────
Obsidian notes    ─┐
Logseq pages      ─┤         ┌─ Chunking & parsing
GitHub repos      ─┤────────►├─ Pattern recognition
Audio files       ─┤         ├─ Transcription (Whisper)
Photos/sketches   ─┤         ├─ Vision analysis
ESP32 captures    ─┘         ├─ Labeling & categorization
                             ├─ Relationship inference
                             └─ Pre-structured pearl
                                        │
                                        ▼
                              GRAPHITI INGESTION
                              (temporal graph)
                                        │
                                        ▼
                             ┌─ Natural language queries
                             ├─ Cluster extraction
                             ├─ Article draft generation
                             └─ Export as .md bundle (7z)
                                        │
                                        ▼
                              CURATION (user choice)
                             ┌─ BackOffice editor
                             ├─ External chatbot (Claude, etc.)
                             └─ Any markdown editor
                                        │
                                        ▼
                              RE-IMPORT polished article
                                        │
                                        ▼
                              PUBLISH → git commit → Hugo
```

**Phase 0 AI stages (text-only MVP):**

| Stage | Tool | Purpose |
|-------|------|---------|
| Chunking & parsing | Python markdown parsers | Split Obsidian/Logseq into pearl-sized units |
| Labeling & categorization | Claude API | Tag pearls with topics, extract metadata |
| Relationship inference | Claude API + embeddings | Connect related pearls |
| Graphiti ingestion | Graphiti (Python) | Temporal graph construction |
| Natural language query | Claude API → Cypher queries | Conversational graph exploration |
| Article draft generation | Claude API | Pre-structure clusters into draft .md |

---

## UI Design Principles

### Core Philosophy: Progressive Disclosure

Show only what's needed, when it's needed. Everything else fades until relevant. Like a workshop: pull out the tool for the current task, the drawer is there when you need it, invisible when you don't.

### Cognitive Ergonomics Rules

1. **Mode-aware UI** — Interface adapts to what you're DOING
   - Capturing → Minimal UI (input field, mic button, done)
   - Exploring → Graph fills the screen (sidebar appears on hover)
   - Composing → Writing-focused (pearl references as floating cards)
   - Publishing → Preview mode (checklist, one big "Publish" button)

2. **Spatial memory** — Consistent zones so the brain maps the space
3. **No tabs, no menus** — Modes flow into each other naturally
4. **Dynamic UI** — Right tool for the right job, not everything everywhere at the same time

### Visual Identity: Dark Office / Light Gallery

Two completely different visual languages connected by the content flowing between them.

**BackOffice App (Dark Workshop):**
- Deep slate/charcoal backgrounds (not pure black)
- Luminous pearl nodes (warm whites, gentle glow)
- Thin connection lines (low opacity until highlighted)
- Functional, dense, tool-oriented
- Data IS the visual — the graph IS the art

**Public Portfolio (Light Gallery):**
- Light mode default (respect visitor OS preference)
- Generous whitespace — let the content breathe
- Typography-forward — the writing IS the showcase
- Skill patterns as visual cards (subgraph thumbnail + title + evidence count)
- Timeline as scrollable narrative (vertical, magazine-style)

### Color Semantics (BackOffice)

| Color | Meaning |
|-------|---------|
| Warm white / pearl | Knowledge pearl (neutral, raw) |
| Amber / gold | Convergence moment (old idea meets new work) |
| Vivid red / coral | Extracted skill pattern (highlighted subgraph) |
| Cool blue | Temporal markers (timeline, dates) |
| Dim gray | Fog of war / unexplored frontier |
| Green pulse | New capture / fresh ingestion |

No gradients. No shadows. No decoration. Clean, functional, data-driven aesthetics.

---

## BackOffice App: Mode-Based UI

### Layout Structure

```
┌─────────────────────────────────────┐
│  TOP: Context bar (where am I?)     │
├────────┬────────────────────────────┤
│ LEFT:  │  CENTER:                   │
│ Nav    │  Primary workspace         │
│ (slim, │  (adapts to current mode)  │
│ icons  │                            │
│ only,  │                            │
│ expand │                            │
│ on     │                            │
│ hover) │                            │
├────────┴────────────────────────────┤
│  BOTTOM: Quick capture (always)     │
└─────────────────────────────────────┘
```

- LEFT nav: always present, minimal icons, expands on hover
- CENTER: transforms based on active mode
- BOTTOM: quick-capture bar ALWAYS accessible — one tap to drop a pearl from anywhere

### Four Modes

#### 1. CAPTURE Mode
```
◉ CAPTURE    ◎ EXPLORE    ◎ COMPOSE    ◎ PUBLISH
─────────────────────────────────────────────────

  What's on your mind?
  ┌───────────────────────────────┐
  │ Type, paste, or speak...      │
  └───────────────────────────────┘
       🎤  📷  📎

  Recent pearls:
  ● Docker networking issue ···· 2m ago
  ● Rust memory model idea ····· 1h ago
  ● Photo: PCB prototype ······· 3h ago
```

#### 2. EXPLORE Mode
```
◎ CAPTURE    ◉ EXPLORE    ◎ COMPOSE    ◎ PUBLISH
─────────────────────────────────────────────────

  ┌─ "What connects Docker to my teaching?" ─┐
  └───────────────────────────────────────────┘

       ╭──╮
   ╭──╮│  ╰──╮    ╭──╮
   │  ╰╯  ╭──╯╭───╯  │
   ╰──╮   │   │  ╭───╯
      ╰───╯   ╰──╯
   [full graph, interactive, NL query]

  Cluster: "DevOps Teaching" (7 pearls)
```

#### 3. COMPOSE Mode
```
◎ CAPTURE    ◎ EXPLORE    ◉ COMPOSE    ◎ PUBLISH
─────────────────────────────────────────────────

┌──────────┬──────────────────────────┐
│ Pearls   │                          │
│          │  # My Docker Journey     │
│ ● note1  │                          │
│ ● note2  │  When I first started... │
│ ● photo  │                          │
│ ● audio  │  [writing area]          │
│          │                          │
│ [drag →] │  ┌─────────────────────┐ │
│ ──────── │  │ Export 7z │ Preview │ │
│ + Add    │  └─────────────────────┘ │
└──────────┴──────────────────────────┘
```

#### 4. PUBLISH Mode
```
◎ CAPTURE    ◎ EXPLORE    ◎ COMPOSE    ◉ PUBLISH
─────────────────────────────────────────────────

  Preview: "My Docker Journey"
  ┌───────────────────────────────────┐
  │  [rendered post as it will appear │
  │   on the public portfolio]        │
  └───────────────────────────────────┘

  ☑ Frontmatter complete
  ☑ Images optimized
  ☐ Skill patterns linked

              [ Publish to Portfolio ]
```

### Graph Visualization Progressive Path

| Version | Capability | Phase |
|---------|-----------|-------|
| v0 | Static force-directed graph (D3.js), nodes = pearls, edges = relationships | Phase 0 |
| v1 | Click node → highlight immediate neighbors | Phase 1 |
| v2 | Skill extraction — select skill label → highlight ALL connected pearls as colored subgraph (Path of Exile moment) | Phase 1-2 |
| v3 | Animation, fog of war dimming, temporal playback, frontier mapping | Phase 2+ |

---

## Public Portfolio: Gallery UI

### Design Language

- Typography-forward, generous whitespace
- Curated content only (user-published)
- Skill patterns displayed as visual cards:
  - Thumbnail of extracted subgraph
  - Skill/pattern title
  - Evidence count (e.g., "backed by 23 pearls")
  - Link to full narrative

### Foundational Pages

1. **Home** — Latest posts + featured skill patterns
2. **Posts/Articles** — Blog-style, markdown-rendered, evidence-linked
3. **Patterns Gallery** — Curated skill subgraph snapshots ("framed paintings from the constellation")
4. **Timeline** — Scrollable narrative of growth, convergence moments, milestones
5. **About** — Profile + link to patterns gallery as capability evidence

---

## Privacy & Publishing Model

### Core Principle: Private First, Publish by Choice

All data lives in BackOffice (private) by default. Nothing is public until the user explicitly publishes.

### Publishing Flow

1. Capture pearls (private, BackOffice)
2. AI processes: chunk, label, infer relationships (private)
3. Explore graph, discover clusters (private)
4. Compose post from cluster (private)
5. Optional: export 7z bundle → curate externally → reimport (private)
6. Preview in publish mode (private)
7. **Publish** = git commit .md to Hugo repo (becomes public)
8. Hugo rebuilds → static portfolio updated

### Export/Curation Freedom

- Pearl clusters exportable as 7z bundles of .md files
- User curates with any tool (Claude, ChatGPT, VS Code, pen & paper)
- Reimport polished article for re-ingestion and publication
- No vendor lock-in at any level of the workflow

---

## Phase 0: Walking Skeleton Scope

### Goal
Prove the full pipeline end-to-end with one vertical slice.

### Deliverables
1. **Hugo site with real visual charter** (not generic TailBliss)
2. **Minimal BackOffice** (Svelte): import Obsidian notes → pearl list → basic graph view
3. **FastAPI backend**: ingestion endpoint + Graphiti + basic graph queries
4. **One compose/publish flow**: select pearls → draft post → git commit → Hugo rebuilds
5. **One published post** derived from real pearl data on the public portfolio

### Existing Data as Test Corpus
- Obsidian vaults
- Logseq graphs
- GitHub repositories (commits, issues, READMEs)

### What Phase 0 Does NOT Include
- Edge devices (ESP32-S3) — parallel track
- Audio/video transcription — text-only MVP
- Vision analysis — text-only MVP
- Full Path of Exile graph interaction — v0 static graph only
- Fog of war / frontier mapping — later phases

---

## Core User Experience

### Defining Experience

**Two applications, two core loops:**

**BackOffice App — Primary loop: Capture → Explore → Compose → Publish**
- The atomic action is **pearl capture**: if it takes more than a few seconds or requires thinking about organization, the system fails. Everything else is downstream.
- The magic moment is **natural language graph exploration**: ask a question, get a visual answer with highlighted patterns.

**Public Portfolio — Primary loop: Read → Discover → Trust**
- Visitor reads an article, discovers it's backed by a constellation of evidence.
- The "aha": "This isn't just a blog post — there's a whole documented pattern behind it."
- Trust through transparency: every claim is traceable to real work.

### Platform Strategy

| Platform | Technology | Interaction | Notes |
|----------|-----------|-------------|-------|
| BackOffice | Svelte web app (Docker container) | Desktop: keyboard + mouse | Local-first, containerized |
| Portfolio | Hugo static site | Responsive, all devices | Deploy online when ready |
| Backend | FastAPI + Graphiti + Neo4j (Docker) | API only | Same container stack |
| Edge devices | Rust + ESP32-S3 | Physical (button, voice, camera) | Separate track, WiFi → backend API |

**Deployment model: Container-first (Docker)**
- Everything runs locally on local network by default (offline-capable)
- Can be deployed online when needed (VPS, cloud)
- No cloud dependency for core functionality
- Portfolio can be deployed separately (Netlify/Cloudflare) or served from container

### Effortless Interactions

| Interaction | Must feel like... | Never feel like... |
|-------------|-------------------|---------------------|
| Capturing a pearl | Dropping a coin in a jar | Filing a tax form |
| Asking the graph a question | Talking to a knowledgeable friend | Writing SQL queries |
| Composing a post from pearls | Arranging photos in an album | Building a PowerPoint deck |
| Publishing to portfolio | Pressing "send" on a message | Deploying a server |
| Importing Obsidian/Logseq notes | Pouring water into a bucket | Migrating a database |
| Browsing skill patterns (visitor) | Exploring an art gallery | Reading a spreadsheet |

**What should happen automatically (zero user effort):**
- Timestamping every pearl
- AI labeling and categorization
- Relationship inference between pearls
- Graph construction and updates
- Skill pattern detection
- Hugo rebuild after publish

### Critical Success Moments

1. **First Import ("The Constellation")**: User imports Obsidian vault → sees their knowledge rendered as a graph for the first time. Hundreds of notes become a living constellation. If this doesn't create a visceral "wow", motivation dies early.

2. **First Pattern Recognition ("I didn't know that")**: System highlights a skill cluster the user didn't consciously recognize. The system sees what the user can't.

3. **First Publish ("It works")**: Pearl cluster → composed post → live on portfolio. The full pipeline proven. Evidence-backed content exists.

4. **First Visitor Trust ("This is real")**: Someone browses the patterns gallery, clicks through to evidence trail, sees real documented work behind every claim.

### Experience Principles

1. **Capture is sacred** — Never interrupt creative flow. Pearl capture must be instantaneous, format-agnostic, and organization-free. The system organizes later.

2. **The graph speaks** — Natural language is the primary interface for exploration. Users ask questions, the graph answers visually.

3. **Trust through evidence** — Every public claim links to documented work. No self-assessment. No preset skill categories. Only patterns from real, timestamped evidence.

4. **Container-first, cloud-optional** — Full functionality offline on local network. Deploy online when ready. User owns their data completely.

5. **Progressive revelation** — Show only what's needed for the current mode. The interface adapts to the user's intent, not the other way around.

6. **Two worlds, one flow** — Dark workshop (BackOffice) and light gallery (Portfolio) are distinct applications with distinct identities. Content flows between them through deliberate publishing.

---

## Narrative Architecture: Pearls, Necklaces, Patterns

### Three Scales of Story

| Scale | Metaphor | Time range | Content | AI role |
|-------|----------|-----------|---------|---------|
| **Micro** | Pearl | Moment | Single capture (note, photo, idea) | Timestamp, label, categorize |
| **Meso** | Necklace | Days/weeks | Composed post, milestone, savepoint | Suggest composition readiness, generate draft |
| **Macro** | Pattern | Months/years | Skill arc, capability narrative | Detect cross-necklace patterns, suggest skill description |

- **Pearl** = single captured moment (raw, small, precious)
- **Necklace** = composed post or milestone (pearls strung together in meaningful sequence)
- **Pattern** = skill recognition (threads crossing multiple necklaces over time)

### AI Cadence (Automated, Not Automatic)

| Frequency | AI action | User sees |
|-----------|----------|-----------|
| On capture | Label, categorize, infer relationships | Nothing — silent processing |
| Daily | Generate summary of today's captures | Summary ready when user opens app |
| Weekly | Score composition readiness per project | Subtle warm indicator on project cards |
| Ongoing | Detect cross-project skill patterns | Highlighted patterns in graph when exploring |

### Milestone as Savepoint

The milestone/savepoint concept addresses **context-switching fear**: users postpone documentation because they unconsciously know resuming a project costs 2x effort. Milestones eliminate this:

- A milestone is a **deliberate pause point** that anchors project state
- It captures: what was done, where things stand, what to do next
- It creates **resumability** — like a video game save point
- The daily summary is the **autosave**; the composed milestone is the **manual save**
- This builds confidence in half-baked ideas: "I can stop here safely and come back"

**Open question**: Is "REFLECT" a 5th mode between Explore and Compose, or a sub-flow within Compose? To be determined through iteration.

---

## Interaction Philosophy: Nothing Automatic, Everything Automated

### Core Principle

The system prepares silently. The UI reflects state. The user initiates action. No popups. No notifications. No interruptions.

### How It Works

**Background workers (automated, silent):**
- Daily summary generation (runs on schedule, result stored)
- Composition readiness scoring (continuous, updates a field)
- Pattern detection (batch, updates graph silently)
- Relationship inference (on ingestion, no user interaction)

**User-triggered workflows (pull, not push):**
- "Show me today's summary" → reads pre-generated summary
- "What's ready to compose?" → reads readiness scores
- "Generate a draft from this cluster" → triggers draft pipeline
- "Describe this skill pattern" → triggers long-form generation

### Anti-patterns (Never Do This)

```
❌ AUTOMATIC (invasive):
  *popup* "You have 12 uncategorized pearls!"
  *notification* "New skill pattern detected!"
  *banner* "You haven't documented in 3 days"

✅ AUTOMATED (ready when you are):
  Daily summary EXISTS — visible when user opens app
  "Ready to compose" indicator GLOWS subtly on project card
  Skill pattern HIGHLIGHTED in graph when user explores
  Nothing pushes. Everything pulls.
```

### The Wise Librarian Principle

The system is a wise librarian, not a pushy assistant. The librarian has already pulled the right books and placed them on your reserved desk. But they will never tap your shoulder while you're reading.

### Trust & Consent Hierarchy

1. **System prepares** (automated, silent, respectful)
2. **UI reflects** (state indicators, not alerts)
3. **User initiates** (pulls when ready)
4. **AI assists** (generates when asked)
5. **User publishes** (deliberate, never accidental)

At every step: user consent, user timing, user control.

### Compose Mode: Narrative-First

The AI doesn't generate a generic article. It generates a **timeline narrative** from the pearl sequence:

```
◉ COMPOSE
─────────

  AI detected narrative: "Your Docker Journey"
  ┌─────────────────────────────────────────┐
  │ Mar 2024: "networking is confusing"     │
  │ Apr 2024: [photo of whiteboard diagram] │
  │ Jun 2024: "finally got bridge working"  │
  │ Sep 2024: "teaching Docker to interns"  │
  │ Jan 2025: "wrote custom network plugin" │
  └─────────────────────────────────────────┘

  Generated draft: "From Confusion to Contribution..."
  [Edit narrative]  [Reorder]  [Export 7z]
```

The pearls ARE the story. The AI finds the arc. The user polishes.

### Two Types of Composition

| Type | Source | Output | Time range |
|------|--------|--------|-----------|
| **Post/Milestone** | Pearl cluster from one project | ~500 word progress report or savepoint | Days/weeks |
| **Skill Description** | Pattern crossing multiple projects | ~1500 word capability narrative | Months/years |

Like BMAD methodology: task → story → epic. Pearl → necklace → pattern.

### Capture Psychology: Overcoming the Monkey Brain

The capture interface must communicate: **"Half-formed is perfect. Raw is valuable. Incomplete is the point."**

Rotating gentle nudges instead of blank input:
- *"What just happened?"*
- *"What did you notice?"*
- *"Quick snapshot — no editing needed"*
- *"Drop it here, sort it later"*

These communicate permission to be incomplete. Pearls start as irritants in oysters — they're not beautiful at first.

### Reinforcing Progression

The system must make accumulated progress VISIBLE:
- Today's capture count (quiet, in context bar)
- Diff from yesterday ("5 pearls today vs. 3 yesterday")
- Project readiness indicators (enough material to compose?)
- Skill pattern growth over time (in graph, when exploring)

Basic navigation skills: know where you are, where you've been, where you could go next.

---

## Desired Emotional Response

### Primary Emotional Goals

| Context | Primary Emotion | Supporting Emotion |
|---------|----------------|-------------------|
| Capturing a pearl | **Permission** — "incomplete is valuable" | Lightness, safety |
| Exploring the graph | **Clarity** — "now I see the connections" | Quiet awe, curiosity |
| Pattern recognition | **Anagnorisis** — earned self-recognition | Wonder, "I didn't know that" |
| Composing a post | **Ownership** — "I chose to tell this story" | Pride, authorship |
| Publishing | **Confidence** — "this is honest and evidence-backed" | Accomplishment |
| Resuming a project | **Trust** — "everything is where I left it" | Calm, readiness |
| Returning after absence | **Clean calm** — "my workshop is ready" | No guilt, no pressure |
| Visitor browsing | **Respect** — "I understand how they think" | Trust, intrigue |

### The Peak Emotion: Anagnorisis

**Anagnorisis** (Greek: "recognition") — the storytelling moment when the protagonist suddenly understands something that was true all along. In this system, the user IS the protagonist.

The anagnorisis moment: *"I've been building this skill across 47 pearls over 3 years and didn't know it until I saw the pattern light up in the graph."*

**Why this works:**
- It must feel EARNED, not given. The user traces the red threads themselves.
- A skill score ("DevOps: 85%") is a report card — no emotion.
- A highlighted subgraph of 47 connected pearls across 3 years is a DISCOVERY.
- Temperature = 0 is essential: a hallucinated connection breaks the anagnorisis instantly. Manufactured flattery destroys earned recognition.

**Design implication:** The graph doesn't TELL you your skills. It SHOWS you the pattern and lets you recognize it yourself. The system illuminates the path. The user walks it.

### Emotional Journey Mapping

**First-time experience (onboarding):**
1. Import data → **Clarity** (see connections, maybe quiet awe)
2. First pattern detected → **Anagnorisis** ("I've been building this all along")
3. First composition → **Ownership** ("this story is mine")
4. First publish → **Confidence** ("the pipeline works, evidence speaks")

**Daily use loop:**
1. Open BackOffice → **Clean calm** (workshop ready, tools in place)
2. Capture pearl → **Permission + lightness** (no judgment, no organization)
3. Explore graph → **Curiosity** (pull threads, ask questions)
4. Review daily summary → **Narrative momentum** (story accumulating)

**Returning after absence:**
1. Open BackOffice → **Clean workspace** (no notifications, no guilt)
2. Find snapshot → **Trust** ("I can resume from exactly here")
3. See accumulated progress → **Gentle discovery** (reflection, not pressure)

### Emotional Valley Map

Critical trust-test moments where users risk abandoning the system:

| Valley | When | User feels | How to cross |
|--------|------|-----------|--------------|
| **The Empty Graph** | First open, no data | "Just an empty tool" | Fast onboarding: import Obsidian in 2 clicks |
| **The Noise Phase** | After import, before patterns | "Mess of dots" | Show something meaningful fast (even simple stats) |
| **The Doubt Moment** | First pattern shown | "Is this real or AI guessing?" | Evidence trail visible immediately, confidence scores |
| **The Documentation Debt** | Realizing uncaptured history | "Should have started years ago" | Celebrate what EXISTS, not what's missing |
| **The Publish Hesitation** | Ready but scared | "Is this good enough?" | Exact preview, low-stakes first publish |
| **The Return Anxiety** | Back after weeks away | "Lost my momentum" | Clean workspace + snapshot = instant re-entry |

Each valley is a trust test. Cross it well → trust deepens. Fail → user abandons.

### Narrative Momentum (Not Gamification)

**Game design, not gamification.** The system is a game board (graph), pearls are moves, patterns are emergent strategies, the player always has agency.

No streaks. No badges. No "you logged 7 days in a row!" No dopamine manipulation.

Instead: **narrative momentum through reflection.**

The daily summary reflects your story back to you:
- "You captured 4 pearls today. Docker project now has 23 pearls."
- "Your graph contains 847 pearls across 12 projects."
- On quiet days: just the current state, no judgment.

This creates the feeling of accumulation, movement, progress — without pushing. The mirror shows you your own story. You naturally want to continue it.

**Narrative silence kills motivation** faster than friction. A static dashboard that looks the same every day offers nothing to reflect on. The daily summary is an emotional engine, not a feature.

### Fog of War Timing

Don't show exploration frontiers until enough territory is mapped:
- **Early phase** (< 100 pearls): empty space = "room to grow" (adventure framing)
- **Growth phase** (100-500 pearls): clusters visible, gaps emerging naturally
- **Mature phase** (500+ pearls): frontier mapping meaningful, fog of war compelling

An empty map with 90% fog is depressing. A map with 40% explored is exciting.

### Micro-Emotions

**Cultivate:**

| Micro-emotion | Where | How |
|---------------|-------|-----|
| Permission | Pearl capture | Nudges: "drop it here, sort it later" |
| Clarity | Graph exploration | Temperature 0, evidence-linked only |
| Safety | Context switching | Milestones as savepoints |
| Ownership | Composition | User initiates everything |
| Clean calm | Returning to app | Organized workshop, no notifications |
| Narrative momentum | Daily use | Reflective summaries, progress visibility |

**Eliminate:**

| Anti-emotion | Where | Prevention |
|-------------|-------|------------|
| Guilt | Inactivity | Zero notifications, zero nagging |
| Overwhelm | Many pearls | Progressive disclosure, mode-aware UI |
| Performance anxiety | Capture | "Incomplete is the point" |
| Surveillance | AI processing | User controls all, nothing automatic |
| Distrust | Pattern recognition | Temp 0, evidence trail, confidence scores |
| Flattery | Skill detection | Earned recognition only, no manufactured praise |

### Design Implications

**Clarity demands zero hallucination:**
- LLM temperature = 0 for all pattern recognition and graph queries
- Every connection traceable to real evidence
- Uncertain patterns marked with confidence scores
- No invented relationships, no suggested skills without evidence

**Failure quality = trust quality:**
- Error states determine long-term trust MORE than success states
- Import fails: "3 of 47 files couldn't parse — here's why" (honest, specific, calm)
- Empty query: "no connections found between X and Y yet" (informative, not discouraging)
- Uncertain pattern: "emerging, needs more evidence" (transparent)
- Never hide failures. Never blame user. Never use vague messages.

**Clean workshop on return:**
- Opens to organized, calm state (configurable preference)
- No accumulated notifications or guilt
- Snapshots available for instant project resumption
- Tools in place, ready when user reaches for them

### Emotional Design Principles

1. **Clarity over awe** — Real connections, zero hallucinations. Wonder is a byproduct of genuine clarity, not a design goal.

2. **Anagnorisis as peak experience** — Earned self-recognition through the graph. The system illuminates. The user discovers. Never reverse this.

3. **Failure quality = trust quality** — Invest more design effort in error states than success states. Honest failure builds deeper trust than polished success.

4. **Nothing performative, everything authentic** — No fake encouragement, no manufactured urgency, no gamification theater. Just honest reflection.

5. **Permission to be incomplete** — "Everything you capture is valuable, even fragments. Especially fragments."

6. **Narrative momentum through reflection** — Game design, not gamification. The daily summary is a mirror, not a scoreboard.

7. **Clean workshop, not eager assistant** — The system is ready when you are. It never greets you with tasks, reminders, or suggestions.

8. **Celebrate what exists** — Frame early empty space as adventure, not deficit. Show frontiers only when there's enough mapped territory to make them meaningful.

---

## UX Pattern Analysis & Inspiration

### Inspiring Products Analysis

**Daily-use tools and why they work:**

| Product | What works | Transferable lesson |
|---------|-----------|-------------------|
| **Brave** | Privacy-first, safe defaults, no ads | Trust through safe defaults; user doesn't configure security |
| **VS Code** | One workspace for everything (code, docs, diagrams via extensions) | Consolidation reduces context switching; extensible but not overwhelming |
| **Obsidian** | Local-first markdown, vault is just files, plugin ecosystem | "World of txt" — user owns files, no vendor lock-in, AI-agent friendly |
| **Logseq** | Quick note capture, graph view, daily journals | Low-friction capture, temporal organization, visual graph |
| **Inkscape** | Precision vector tool, full control | Specialized tool that does its job completely |
| **Excalidraw** | Sketch-first diagramming, VS Code extension | Draft-quality welcome; integration into existing workflow beats separate app |
| **Path of Exile** | Constellation skill tree, emergent builds, pattern visibility | Skill = highlighted subgraph; visual density creates awe |

**The "World of Txt" philosophy:**
- Local-first but sync-able
- User in control of data (crash-safe, exportable)
- No vendor lock-in (files are standard formats)
- AI-agent friendly (text is the universal interface)
- Cognitively friendly (WYSIWYG at the terminal level)
- Tools become invisible with mastery — the question isn't "is it effortless?" but "does it reward daily practice with increasing fluency?"

**The lost comment pain:** When a long comment or note is lost due to a crash or UI failure, it "hurts badly." This reinforces: auto-save everything, never lose user input, graceful failure on every interaction.

**Chicken-and-egg insight:** "Am I using them daily because they feel effortless, or do they become effortless because I use them?" — The best tools reward mastery. They start usable and become invisible with practice. Design for both: low entry barrier AND increasing fluency.

### Abandoned Products & Anti-Patterns

| Product | Why abandoned | Anti-pattern extracted |
|---------|--------------|----------------------|
| **Notion** | Online-first, sluggish, requires connection, pricy for teams, pushy notifications | Cloud-dependency kills trust; notifications = anti-emotion; pricing as lock-in |
| **Fusion360** | No Linux support, forced online updates on every launch | Platform exclusion; forced updates break user control; online-required = fragile |

**General abandonment triggers (mapped to anti-emotions):**

| Trigger | Anti-emotion | Our design response |
|---------|-------------|-------------------|
| Unwanted automatic features | Overwhelm, surveillance | Nothing automatic, everything automated |
| Intrusive notifications | Guilt, pressure | Zero notifications, zero popups |
| Data safety concerns | Distrust | Local-first, container-hosted, user owns data |
| Loss of control | Anxiety | User initiates all workflows |
| Unreliable outcomes | Frustration | Graceful failure > graceful success |
| Lack of personalization | Alienation | Configurable preferences, clean workspace |
| Doesn't do the job | Abandonment | Focus: do fewer things, do them completely |
| Online-first requirement | Dependency fear | Container-first, offline-capable |
| Forced updates | Loss of agency | User controls when to update |

**Key insight:** The reasons for abandoning tools ARE the anti-emotions list from our emotional design. The spec is internally consistent.

### Visualization Inspiration: Maps as Purpose-Driven Tools

Each visualization type serves a specific purpose. The system should support MULTIPLE map metaphors, not just one:

| Map type | Purpose in system | When to use |
|----------|------------------|-------------|
| **Star chart / Path of Exile** | Skill pattern visualization | Exploring skill emergence, seeing the constellation |
| **Flowchart (Mermaid)** | Process documentation, pearl sequences | Composing posts, showing workflow |
| **Sequence diagram (Mermaid)** | Temporal ordering, convergence timeline | Timeline view, event ordering |
| **Geo/territory map** | Domains of expertise, boundaries | Frontier mapping, fog of war |
| **Circuit diagram** | Dependencies, compound relationships | How skills connect and build on each other |
| **Subway map** | Simplified learning paths, reduced mental load | Known routes presented without full network complexity |

**Subway map as cognitive relief:** The subway map isn't just a visualization — it's a simplification principle. When routes are known, reduce mental load. Don't show the full constellation when the user just needs to follow a path. Same data, simplified for specific intent.

**Advanced visualization potential:**
- Cross-reference with personality traits, values, oppositions and boundaries
- Shadow work and negative space (what you avoid, what you don't know)
- Fog of war as genuine unknown territory representation
- Multiple lenses on same underlying data

**Design principle:** The graph is not one visualization — it's a **LENS system**. Same underlying data, different visual metaphors for different questions.

### Text-Based Visual Pipeline: "World of Txt" Extended

Excalidraw, Mermaid, SVG — visual formats that remain text-based under the hood.

**Mermaid as primary diagram format:**
- AI-readable and AI-generatable out of the box (no fine-tuning needed)
- Renders natively in both Hugo (goldmark) and Svelte
- Covers: flowcharts, sequence diagrams, timelines, graph representations
- Git-trackable, diffable, versionable
- One syntax, many visual forms
- Excalidraw deferred to later phases (too complex for AI without fine-tuning)

**Text-based visual pipeline:**

```
CAPTURE (text-based)          STORAGE           RENDER
────────────────────          ───────           ──────
Mermaid syntax       ─┐
SVG markup           ─┤      .md files    ──►  SVG/HTML in BackOffice
ASCII diagrams       ─┤      (git repo)   ──►  Static render in Hugo
Code blocks          ─┘                   ──►  AI interpretation
                                          ──►  Graph relationship extraction
```

**Visual pearls feed the knowledge graph automatically:**
A Mermaid diagram pearl contains explicit relationships. When captured:
- Human reads the visual rendering
- AI parses the text syntax for relationship extraction
- Graph engine ingests the relationships directly into the constellation
- No manual tagging needed — drawing IS documenting relationships

**Implication:** Drawing a quick diagram is MORE valuable to the system than writing a paragraph, because relationships are explicit rather than implicit. The system naturally rewards visual thinking through better pattern detection.

**Format hierarchy for pearl storage:**
1. Mermaid (best: structured, renderable, AI-parseable, relationship-rich)
2. Markdown with links (good: text searchable, relationships via links)
3. Code blocks (good: searchable, contextual)
4. Plain text (okay: searchable, needs AI for relationship inference)
5. Binary (photos, audio) — stored with AI-generated text description layer

### The Equipped Workbench Model

The BackOffice isn't an empty canvas. It's an **equipped workbench** — a promise of a good and satisfying job.

**What "equipped" means technically:**
- Parsers configured (Obsidian format, Logseq format, GitHub format)
- Renderers active (Mermaid, SVG)
- AI pipeline running (silent, automated, ready)
- Graph live (updated, queryable)
- Daily summary generated (waiting for you to look)

**The end-of-session ritual: "Reset the Space"**

Like a craftsperson tidying the workshop at end of day:
1. Enter workshop → tools are where you left them
2. Pull project from shelf (resume from snapshot)
3. Work
4. End session → **single "End Session" button**:
   - Auto-saves all open work
   - Creates snapshot/savepoint
   - Archives completed items
   - Returns workspace to clean state
5. Workshop is clean for next session

### Design Inspiration Strategy

**Core philosophy: "World of Txt" meets "Equipped Workbench"**

The system inherits the best of Nicolas's daily tools:
- **From Obsidian/Logseq**: local-first, markdown-native, graph-aware
- **From VS Code**: one workspace, extensible, keyboard-friendly
- **From Brave**: safe defaults, privacy-first, no intrusion
- **From Mermaid**: text-based visuals, AI-friendly, renderable everywhere
- **From Path of Exile**: constellation as data visualization, emergence through play

While explicitly rejecting:
- **From Notion**: cloud-dependency, notifications, pricing lock-in
- **From Fusion360**: platform exclusion, forced updates, online requirement
- **From generic SaaS**: feature overwhelm, surveillance, data hostage

**The Workbench Promise:** The equipped workbench communicates competence and readiness. You walk in and feel: "I can do good work here."

---

## Design System Foundation

### Design System Choice

**TailwindCSS 4.1 as unified design system across both applications.**

Single CSS foundation with theme switching for dark (BackOffice) and light (Portfolio). No component library, no external design system dependency. Build only what's needed.

### Rationale for Selection

| Factor | Decision rationale |
|--------|-------------------|
| **Uniformity** | Same utility classes, same design tokens, same mental model |
| **Single source of truth** | One CSS configuration shared across Hugo + Svelte |
| **Already installed** | Hugo portfolio already runs TailwindCSS 4.1 with OKLCH |
| **Dark/light = theme swap** | Same components, different theme — not different systems |
| **No vendor lock-in** | Tailwind is utility CSS, no proprietary components |
| **Solo developer** | One system to learn, maintain, and extend |
| **Walking skeleton speed** | No new library to evaluate, install, learn |
| **"World of txt" aligned** | Utility classes in HTML = readable, AI-friendly |
| **Svelte compatible** | Tailwind integrates natively with Svelte/Vite |

### Monorepo Structure

```
project-root/
├── shared/
│   └── tokens/
│       ├── colors.css        ← semantic color definitions (OKLCH)
│       ├── typography.css    ← font scale
│       └── spacing.css       ← spacing scale
├── portfolio/               ← Hugo site
│   ├── assets/css/main.css  ← @import shared tokens + light defaults
│   ├── tailwind.config.js
│   └── hugo.yaml
├── backoffice/              ← Svelte app
│   ├── src/app.css          ← @import shared tokens + dark defaults
│   ├── tailwind.config.js
│   └── vite.config.js
└── README.md
```

**Why monorepo for Phase 0:**
- One repo, one git history, shared tokens, two apps
- Single `git commit` updates tokens across both apps simultaneously
- "Publish" action commits to same repo where Hugo reads content
- Pipeline: write markdown → commit → Hugo rebuilds from same repo
- Extract to separate packages when/if scale demands it

### Implementation Approach

**Shared design tokens (CSS custom properties, OKLCH color space):**

```css
/* shared/tokens/colors.css — single source of truth */
:root {
  /* Pearl system — universal across both themes */
  --pearl: oklch(0.95 0.02 90);
  --convergence: oklch(0.75 0.15 75);
  --pattern: oklch(0.65 0.25 25);
  --temporal: oklch(0.70 0.12 240);
  --frontier: oklch(0.40 0.01 260);
  --fresh: oklch(0.75 0.18 145);
}

.light {
  --surface: oklch(0.98 0.01 260);
  --surface-elevated: oklch(1.0 0 0);
  --text-primary: oklch(0.15 0.02 260);
  --text-secondary: oklch(0.45 0.02 260);
}

.dark {
  --surface: oklch(0.15 0.02 260);
  --surface-elevated: oklch(0.20 0.02 260);
  --text-primary: oklch(0.93 0.01 260);
  --text-secondary: oklch(0.65 0.02 260);
}
```

**Hugo already has this architecture:** TailwindCSS 4.1 with OKLCH colors and `@theme` directive in `main.css`. Phase 0 portfolio visual charter = swap generic TailBliss color tokens with pearl system colors. One-file change.

**Theme switching mechanism:**
- `<html class="dark">` for BackOffice (dark workshop)
- `<html class="light">` for Portfolio (light gallery, with OS preference respect)
- Same component markup, Tailwind `dark:` variants handle the switch
- Hugo's existing dark mode toggle (localStorage-backed) already works this way

### What Is Shared vs. What Is Not

**Shared (design language):**
- CSS tokens (colors, spacing, typography) via `shared/tokens/`
- Tailwind utility classes (same class names, same behavior)
- Visual language (same color semantics across both apps)
- Mermaid rendering (both Hugo and Svelte support it)
- Pearl system colors (universal meaning in both contexts)

**NOT shared (implementation):**
- Component markup (Hugo Go templates ≠ Svelte components)
- Interactivity (Alpine.js in Hugo, Svelte reactivity in BackOffice)
- Layout systems (Hugo layouts vs Svelte routing)
- Build pipelines (Hugo build vs Vite/Svelte build)

**This is intentional.** Share the design language, not the code. Two apps, one visual vocabulary, zero shared components. Clean separation of concerns.

### Density Strategy

Same tokens, different application:

| Property | Portfolio (light gallery) | BackOffice (dark workshop) |
|----------|-------------------------|---------------------------|
| Text size | Large (`text-lg`, `text-xl`) | Standard/compact (`text-sm`, `text-base`) |
| Line height | Generous (1.7-1.8) | Tight (1.4-1.5) |
| Margins | Wide, breathing room | Compact, efficient |
| Layout | Single column, focused | Multi-panel, dense |
| Max width | Content-constrained (80ch) | Full-width, maximized screen |
| Whitespace | Abundant (gallery feel) | Functional (workshop feel) |

The context switch between apps feels like changing rooms in the same building — familiar vocabulary, different density appropriate to the task.

### Customization Strategy

**Phase 0:**
- Replace generic TailBliss color tokens with pearl system colors (one-file change)
- Establish typography scale for both density modes
- Set spacing scale for both contexts
- Build only components needed for walking skeleton

**Phase 1+:**
- Refine based on real usage
- Extract reusable patterns when repetition appears (not before)
- Add animation tokens for graph transitions
- Extend color palette as visualization needs grow

**Anti-over-engineering rule:** Don't build a component library upfront. Build components as needed. Three similar implementations before abstracting.

---

## Defining Core Experience

### The Defining Experience

**"Drop a pearl, watch patterns emerge."**

Two moments define the entire system:
1. **Capture** — drop a pearl (upload, validate, store)
2. **Recognize** — see the pattern light up with %confidence (anagnorisis)

Everything else is downstream: compose, publish, explore, timeline.

### User Mental Model

**The user is a craftsperson, not a data entry clerk.**

- BackOffice is NOT where content is created — it's where content is **inserted, assessed, and validated**
- Content creation happens elsewhere (workshop, field, edge devices)
- BackOffice receives raw materials (uploads) and processes them into pearls
- Mental model: **import → validate → store → explore → compose → export**

### Pearl Capture Mechanics

**Phase 0: Upload-first model**

```
Upload raw material (file, text, link)
          │
          ▼
   AI pre-processing
   ├─ Chunk into pearl candidates (if multi-topic)
   ├─ Label and categorize each candidate
   └─ Summarize (variable length handling)
          │
          ▼
   Pearl candidates presented for validation
   "I found 3 topics in this document:
    Pearl 1: Docker bridge config (para 2-4)
    Pearl 2: Kubernetes decision (para 5-7)
    Pearl 3: Intern onboarding (para 8-12)
    Accept as 3 pearls? Or keep as 1?"
          │
          ▼
   User validates: accept / edit / reject each
          │
          ▼
   Validated pearls inserted into graph (Graphiti)
```

**AI proposes, user decides.** Chunking boundaries, labels, and categorization are all AI-suggested and user-validated. Same consent principle everywhere.

**Open questions (TBD through iteration):**
- Summarization strategy for variable-length inputs
- Optimal pearl granularity guidelines
- Metadata presented during validation review

### Pattern Recognition Pipeline

**Decomposed into solved problems:**

```
EMBED              CLUSTER            LABEL              VALIDATE
─────              ───────            ─────              ────────
text-embedding-3   HDBSCAN            Claude API         User accepts/
(vectors)          (density-based,    (temp=0,           rejects/edits
                    no preset k)      evidence-linked)
     │                  │                  │                  │
     ▼                  ▼                  ▼                  ▼
  Similarity        Emergent          "Container          Training
  space             clusters           Orchestration"      signal
                    (variable          confidence: 87%     recorded
                     size ok)          evidence: 15p
```

**Why this decomposition works:**
- **Embeddings**: proven, cheap (~$0.02 for 500 notes), fast
- **HDBSCAN**: finds clusters without knowing how many exist (emergence-friendly)
- **LLM labeling**: flexible, explainable, temp=0 for zero hallucination
- **User validation**: consent + training data for free

**No custom ML models needed for Phase 0.** Each step is proven technology. The magic is composing them together.

### Temporal Pattern Analysis

Beyond "these pearls are related" — Graphiti reveals WHEN and HOW:

```
Cluster: "Container Orchestration" (15 pearls)

Temporal narrative:
  2023-Q1: 2 pearls (exploration)
  2023-Q3: 1 pearl (dormancy)
  2024-Q1: 4 pearls (acceleration — triggered by project X)
  2024-Q2: 3 pearls (sustained practice)
  2024-Q4: 5 pearls (convergence with teaching domain)

Story: "Dormant knowledge activated by new project,
        then converged with teaching skills"
```

The temporal pattern IS the anagnorisis material. Not "you have Docker skills" but "your Docker knowledge lay dormant for 6 months then exploded when you started teaching."

### %Confidence as Social Contract

**Confidence display is the core trust and incentive mechanism.**

Like meeting a person: first impression sets initial %confidence, then each interaction refines it up or down. This is how humans build trust — and how reinforcement learning works.

**How it functions:**
- Every pattern/skill shown with explicit %confidence
- Low confidence = "I see something, but need more evidence"
- High confidence = "strong pattern, well-supported"
- User sees EXACTLY what evidence supports the score
- Adding more pearls → confidence changes → curiosity + incentive

**Why it works:**
- **Authenticity**: honest about uncertainty (not performative)
- **Incentive**: low % pulls the user to add more evidence
- **Trust**: transparency about what the system knows vs. guesses
- **Agency**: user understands they can influence the score

### Two-Layer Storage Architecture

| Layer | Database | Stores | Purpose |
|-------|----------|--------|---------|
| **Operational** | Neo4j (graph) | Pearl current state: validated labels, confidence, relationships | Queries, visualization, pattern detection |
| **Training** | PostgreSQL (log) | Historical diffs: AI proposal vs. user action, immutable ledger | Training data, accuracy metrics, system improvement |

**Pearl properties (Neo4j):**
```
Pearl #247:
  content: "Docker bridge networking notes"
  ai_labels: ["docker", "networking", "infrastructure"]
  ai_confidence: 0.82
  user_validated: true
  user_label_edits: ["devops"]
  validation_date: 2026-02-08
  chunk_accepted: true
```

**Validation log (PostgreSQL, append-only):**
```
Signal #247:
  pearl_id: 247
  ai_proposed_labels: ["docker", "networking", "infrastructure"]
  user_final_labels: ["docker", "networking", "infrastructure", "devops"]
  action: "accepted_with_addition"
  ai_proposed_chunks: 3
  user_accepted_chunks: 3
  timestamp: 2026-02-08T14:32:00
```

**Validation IS training.** Every accept/reject/edit is a labeled data point. After 6 months of normal use → enough data for fine-tuning. Zero extra user effort.

### Training Gauge & Model Agency

**User controls when the system learns:**

```
Dashboard (quiet, in context bar):

  Training data: ████████░░ 847 validations
  [Launch training]  or  auto at threshold

  Model versions:
    v3 (current) — accuracy 88%
    v2 (previous) — accuracy 84%
    v1 (baseline) — accuracy 71%
```

- Training data gauge visible (like "context until compact" in Claude Code)
- User can trigger training manually or set auto-threshold
- Model versioning with rollback capability (Phase 1+)
- Meta-learning visibility: "Month 1: 40% edits. Month 6: 12% edits. The system is learning your vocabulary."

**Noted for future (out of Phase 0 scope):**
- Model versioning and rollback architecture
- Degradation detection after fine-tuning
- A/B comparison between model versions

### Publish Mechanics

**Phase 0: Export-based composition**

```
BackOffice:
  Select pearl cluster → Bundle as .md files → Export (7z)

External editor (user's choice):
  Compose / curate / polish → Save .md with frontmatter

BackOffice:
  Reimport polished article → Review → Publish (git commit)

Hugo:
  Rebuild → Live on portfolio
```

### Novel vs. Established Pattern Analysis

| Interaction | Pattern type | Strategy |
|-------------|-------------|----------|
| Pearl upload & validation | **Established** | Standard file upload + review |
| AI chunking proposal | **Adapted** | AI suggests, user confirms |
| Graph constellation | **Novel** | Custom visualization — invest here |
| NL graph query | **Novel** | Conversational graph interface — invest here |
| %Confidence display | **Novel** | Trust mechanism — unique differentiator |
| Export bundle | **Established** | Standard file export |
| Reimport & publish | **Established** | File upload + git commit |
| Training gauge | **Adapted** | Resource meter (like Claude Code context) |

**Strategy:** Established patterns for pipeline. Innovation budget on: graph visualization, pattern recognition display, %confidence mechanism.

### Success Criteria

| Criteria | Metric |
|----------|--------|
| Pearl upload to validation | < 30 seconds (including AI processing) |
| Validation decision | < 10 seconds per pearl |
| Graph query response | < 3 seconds (NL → visual result) |
| Pattern confidence accuracy | User agrees > 80% of time |
| Export bundle generation | < 5 seconds |
| Publish to live site | < 60 seconds (git + Hugo rebuild) |
| First import to first "wow" | < 5 minutes |
| Embedding cost per cycle | < $1 |
