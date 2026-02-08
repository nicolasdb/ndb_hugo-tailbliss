# Product Requirements Document: Knowledge-First Portfolio System

**Project**: Nicolas's Portfolio & Skills Repository
**Version**: 2.0 (Portfolio-First)
**Date**: 2026-02-07
**Author**: Nicolas (with multi-agent collaborative input)
**Status**: Ready for Design & Implementation

---

## Executive Summary

This PRD defines a **living knowledge system** that inverts the traditional portfolio workflow. Instead of selecting skills first then writing content, the system captures knowledge pearls continuously (multi-format: notes, images, audio, sketches), organizes them into coherent narratives, and builds an evolving temporal knowledge graph that reveals:

- How you actually learn and work (honest chronicle, not curation)
- Patterns in your documented practice (skills emerge from shape of work)
- When old ideas became relevant (time-to-traction visibility)
- Where safe exploration opportunities exist (mapping untouched areas)
- How capabilities connect and compound over time

The system is designed for **emergence with temporal awareness**—discovering patterns through documented work over time—using temporal-aware graph databases (Graphiti) and serendipitous memory surfacing rather than pre-defined skill taxonomies.

---

## Problem Statement

### Current State

1. **Reversed Portfolio Workflow**: Traditional approach: "What skills do I have? → What content should I write to showcase them?" → Write content after the fact
2. **Knowledge Pearl Scatter**: Ideas and learning moments captured across tools (notes, photos, recordings, sketches) and scattered; they get lost or forgotten
3. **Documentation Timing Loss**: Documentation happens *after* work is done, losing the raw learning moments and emotional context
4. **Pattern Invisibility**: Real skill emergence patterns remain hidden; you can't see what you've unconsciously been building toward
5. **Temporal Blindness**: No system tracks *when* ideas emerged, *when* they became relevant, *how long* convergence takes
6. **Ideation Decay**: Old ideas disconnected from new work; no mechanism to surface convergence moments

### The Gap

There's a fundamental mismatch between:
- How knowledge actually forms (continuous, multi-format, emergent from practice)
- How portfolios are traditionally curated (skills-first, writing-second, looking backward)
- When learning moments matter most (captured in real-time, contextualized with others over time)
- When ideas become valuable (not immediately, but when new work creates convergence)

---

## Goals & Objectives

### Primary Goal
**Build a system that inverts traditional portfolio workflow: capture knowledge first, organize into narratives, let skill patterns emerge naturally from the shape of documented work over time.**

### Key Objectives

| Objective | Description |
|-----------|-------------|
| **O1: Multi-Format Pearl Capture** | Capture knowledge pearls continuously in any format (notes, images, audio, sketches, code) without requiring completion or organization |
| **O2: Temporal Tracking** | Track precise timestamps for convergence detection, time-to-traction visibility, and evolution patterns |
| **O3: Project Organization** | Organize related pearls into coherent projects and narratives automatically or manually |
| **O4: Skill Pattern Recognition** | Use LLM analysis to reveal skill clusters emerging from documented work (not pre-defined) |
| **O5: Convergence Detection** | Surface when old ideas/pearls become relevant to current work (temporal + semantic matching) |
| **O6: Safe Exploration Mapping** | Visualize what's been heavily practiced vs. untouched areas; identify learning frontiers |
| **O7: Honest Narrative Visibility** | Make growth visible as topology—showing real learning trajectory, iteration, failures, and breakthroughs |

---

## User Personas

### Primary User: Nicolas (Self)
- **Goal**: Understand and communicate my evolving capabilities through real work evidence
- **Need**: A system that captures growth continuously, reveals patterns I don't see, and identifies safe exploration areas
- **Pain Point**: Too many scattered sources (old repos, issues, blog versions) + difficulty connecting old ideas to new work
- **Value**: A personal knowledge artifact that's both humble (evidence-based) and ambitious (reveals emergence)

### Secondary Users: Collaborators, Recruiters, Stakeholders
- **Goal**: Understand Nicolas's capabilities, growth trajectory, and expertise areas
- **Need**: Visual, navigable representation of skills and how they developed
- **Pain Point**: Static portfolio doesn't show growth, connections, or learning patterns
- **Value**: Clear evidence of capabilities, learning trajectory, and safe collaboration areas

---

## Product Vision & Core Concept

### The Inverted Portfolio Workflow

**Traditional**: Select skills → Write content to showcase → Build portfolio
**New**: Capture pearls → Organize into narratives → Recognize skills as emergent patterns

1. **Capture** knowledge pearls continuously (multi-format: notes, images, audio, sketches, code)
2. **Organize** into projects by clustering related pearls
3. **Track time** (when captured, when became relevant, when convergence happened)
4. **Recognize patterns** through LLM analysis of accumulated documented work
5. **Revive convergence** by surfacing when old ideas connect to new work
6. **Build honest chronicle** that shows real learning trajectory, not curated highlights

### The Temporal Knowledge Graph Model

Rather than a static skill tree, build a **temporal mesh of nodes, relationships, and events** where:

- **Nodes** = Knowledge pearls, projects, ideas, outcomes
- **Edges** = Relationships (depends on, compounds with, related to, etc.)
- **Timestamps** = When each event occurred (critical for convergence detection)
- **Temporal Patterns** = How skills emerged over time, when ideas became relevant
- **Negative Space** = Visible gaps revealing untouched learning areas
- **Convergence Moments** = When old ideas + new work intersect (marked by temporal proximity)

**Graphiti Integration**: Using Graphiti for temporal graph ingestion ensures:
- Precise tracking of when knowledge was captured
- Detection of convergence moments (old idea X + new project Y at time T)
- Time-to-traction visibility (how long from first mention to relevance?)
- Evolution tracking (how did this idea change over time?)

### Multi-Dimensional Skill Emergence

Skills don't need pre-definition. They emerge from documented patterns:

1. **Practice Accumulation** 📚
   - Each outcome/pearl adds to the bucket
   - Pattern recognition happens through repetition
   - Signal: Clustering of related work across time

2. **Convergence Recognition** 🔗
   - Old ideas surface when relevant to new work
   - Temporal proximity reveals connections
   - Signal: Multiple related outcomes within a time window

3. **Mastery Evidence** 📈
   - Success patterns across multiple outcomes
   - Consistency over time
   - Signal: High-confidence clustering + temporal distribution

### Evidence-First Model

Each element is **documented evidence**:
- Knowledge pearls (captured in real-time, with timestamps)
- Projects (organized from related pearls)
- Outcomes (completed work with dates)
- Convergence moments (when old connects to new, tracked temporally)

No self-assessment. No skills selected upfront. Only documented learning and work.

---

## Key Features

### Feature 1: Multi-Format Knowledge Pearl Capture

**Description**: Capture learning moments, ideas, observations, and reflections in any format without requiring completion or organization

**User Stories**:
- As Nicolas, I want to capture quick ideas in various formats (text, audio, photos, sketches) so that learning moments don't get lost
- As Nicolas, I want to capture without organization/categorization so that I'm not blocked by decision-making
- As a system, I want to timestamp every pearl so that temporal patterns become visible later
- As Nicolas, I want to optionally link pearls together so that connections can be recorded as they're noticed

**Acceptance Criteria**:
- [ ] Multi-format input (markdown, audio transcription, image upload, code snippet, voice note)
- [ ] Simple, low-friction capture UI (append to a note, quick form, voice command)
- [ ] Automatic timestamping (captured_at, discovered_relevant_at for later)
- [ ] Optional tagging/linking without forcing organization
- [ ] Pearl storage with full metadata (source, format, context tags)
- [ ] Search and browse functionality

**Effort**: M (Medium - multi-format ingestion, transcription APIs)

---

### Feature 2: Project Organization from Pearl Clustering

**Description**: Organize related knowledge pearls into coherent projects and narratives; turn scattered ideas into structured documentation

**User Stories**:
- As Nicolas, I want to group related pearls into a project so that scattered learning moments become coherent narratives
- As Nicolas, I want to create a post/article from clustered pearls so that I can share organized thinking
- As a system, I want to suggest related pearls so that organization is easier
- As Nicolas, I want to track which pearls contributed to which project so that I can trace narrative origins

**Acceptance Criteria**:
- [ ] Manual pearl clustering into projects (drag-and-drop UI)
- [ ] Semi-automatic suggestion based on semantic similarity
- [ ] Project creation from cluster of pearls (turn into blog post, documentation, etc.)
- [ ] Temporal grouping view (show pearls by date within project)
- [ ] Pearl-to-project lineage tracking (what pearls became this project?)
- [ ] Project metadata (title, date range, tags, public/private)

**Effort**: M (Medium - clustering UI + semantic similarity matching)

---

### Feature 3: Temporal Tracking & Convergence Detection

**Description**: Track when knowledge pearls and projects were created; detect when old ideas become relevant to new work; reveal time-to-traction patterns

**User Stories**:
- As a system, I want to timestamp every pearl and project so that temporal patterns become visible
- As Nicolas, I want to see when this old pearl became relevant to new work so that convergence moments are captured
- As the system, I want to detect when multiple pearls/ideas connect across time so that serendipity is visible
- As Nicolas, I want to understand the timeline from "first idea" to "relevant outcome" so that patience becomes strategic

**Acceptance Criteria**:
- [ ] Graphiti integration for temporal graph management
- [ ] Automatic timestamps on all pearls, projects, outcomes
- [ ] Temporal queries: "What ideas from 2-3 years ago are relevant now?"
- [ ] Convergence detection: semantic similarity + temporal proximity matching
- [ ] Timeline visualization showing when ideas appeared and when they became relevant
- [ ] "Time to traction" metrics (months/years from first mention to relevance)

**Effort**: L (Large - Graphiti integration, temporal queries, convergence algorithms)

---

### Feature 4: Skill Pattern Recognition from Documented Work

**Description**: Use LLM analysis to recognize skill clusters and patterns emerging from accumulated knowledge pearls and projects (not predefined skills)

**User Stories**:
- As a system, I want to analyze the corpus of documented pearls/projects to identify emerging skill patterns
- As Nicolas, I want to see what skill clusters the system recognized from my work so that I understand my actual capabilities
- As the system, I want to track confidence in skill recognition so that users know what's proven vs. emerging

**Acceptance Criteria**:
- [ ] LLM pipeline: ingest pearls + projects → extract skill signals → identify clusters
- [ ] Confidence scoring (how many pearls/projects support this skill?)
- [ ] Clustering algorithm: group related patterns
- [ ] Relationship mapping: show how skills compound (knowing X makes Y more likely)
- [ ] Manual validation workflow (accept/reject/refine discovered skills)
- [ ] Recency weighting: recent work weighted more heavily than old

**Effort**: L (Large - requires robust LLM integration + pattern analysis)

---

### Feature 5: Exploration Frontier Mapping

**Description**: Visualize the fog of war (unknown unknowns) and safe exploration vectors

**User Stories**:
- As Nicolas, I want to see what I haven't explored yet so that I can plan safe learning
- As the system, I want to identify skill gaps adjacent to current expertise so that learning paths are suggested
- As Nicolas, I want to understand the cost of exploring unknown areas so that I can make informed choices

**Acceptance Criteria**:
- [ ] Negative space visualization (show gaps in the knowledge graph)
- [ ] Adjacent skills analysis: what's one step away from current expertise?
- [ ] Safe exploration paths: multi-step routes from known to unknown
- [ ] Exploration difficulty scoring: estimate the learning curve
- [ ] Risk/reward analysis: potential value vs. complexity of exploring each frontier

**Effort**: L (Large - requires graph analysis algorithms)

---

### Feature 6: Idea Convergence & Revival

**Description**: Connect old ideas to current work; show where past thinking finds new traction

**User Stories**:
- As Nicolas, I want to see how old ideas relate to current projects so that visionary thinking compounds
- As the system, I want to surface old pearls when new work touches on similar domains so that convergence becomes visible
- As Nicolas, I want to understand the "time to traction" for ideas so that patience becomes strategic

**Acceptance Criteria**:
- [ ] Historical idea indexing (with optional deep-dive into past repos)
- [ ] Relevance matching: when does old idea X connect to new project Y?
- [ ] Timeline visualization: show idea lifecycle from first mention to traction
- [ ] Convergence scoring: confidence that this idea should be revived
- [ ] Actionable recommendations: "You mentioned X 10 years ago; your new project Y touches on it"

**Effort**: L (Large - requires historical data management + matching algorithms)

---

### Feature 7: Knowledge Graph Visualization

**Description**: Interactive visualization of skills, relationships, XP levels, and exploration frontiers

**User Stories**:
- As a visitor, I want to see Nicolas's skill landscape visually so that I understand his capabilities at a glance
- As Nicolas, I want to interact with my knowledge graph so that I can explore different perspectives of my growth
- As the system, I want to show relationships between skills so that compound effects become visible

**Acceptance Criteria**:
- [ ] Interactive force-directed graph (nodes = skills, edges = relationships)
- [ ] Node coloring: by domain, by proficiency, by XP level
- [ ] Edge styling: by relationship type (compounds, depends on, related to, etc.)
- [ ] Hover/click interactions: drill into skill details, related outcomes, evidence
- [ ] Negative space highlighting: show gaps in the graph
- [ ] Animation: show growth over time (skill appearing, XP increasing)

**Effort**: L (Large - requires advanced visualization library + interaction design)

---

### Feature 8: Timeline & Narrative View

**Description**: Chronological view of how skills developed, projects completed, and ideas converged

**User Stories**:
- As a visitor, I want to see how Nicolas grew over time so that I understand his learning trajectory
- As Nicolas, I want to see the narrative of my growth so that the story becomes visible
- As the system, I want to show when ideas became relevant so that timing/patience becomes clear

**Acceptance Criteria**:
- [ ] Timeline visualization: chronological progression of discoveries, completions, convergences
- [ ] Different event types: discovery (low-XP signal), completion, convergence, pivot
- [ ] Narrative filtering: view by domain, by type, by impact
- [ ] Annotation capability: add context to timeline events
- [ ] Comparison view: show parallel skill development

**Effort**: M (Medium - requires timeline UX + event aggregation)

---

## Functional Requirements

### FR-1: Data Capture & Storage

```
Requirement: The system must capture and store work outcomes with minimum friction

Details:
- Support multiple input formats (CLI, markdown, API)
- Validate outcome data (required: date, skills, success metrics)
- Store with proof-of-work linking (git commit, URL, etc.)
- Automatic timestamping
- Versioning: track changes to outcomes
```

### FR-2: Memory Pearl Management

```
Requirement: The system must allow creation and retrieval of memory pearls

Details:
- Create pearls: timestamp + content + optional tags + source
- Persist without requiring completion
- Search & browse pearls
- Surface relevant pearls based on current work context
- Track activation: when was pearl written vs. when did it become relevant?
```

### FR-3: LLM-Driven Skill Discovery

```
Requirement: The system must discover skill patterns from outcomes

Details:
- Batch process outcomes to extract skill signals
- Identify skill clusters through pattern analysis
- Generate skill confidence scores
- Suggest relationships between skills
- Handle manual validation/refinement
```

### FR-4: Graph Storage & Querying

```
Requirement: The system must store and query a knowledge graph efficiently

Details:
- Support nodes: skills, projects, outcomes, ideas
- Support edges: depends on, compounds with, related to, etc.
- Query: skill relationships, adjacent skills, exploration paths
- Efficient negative space identification (missing relationships)
- Timeline queries (skill development over time)
```

### FR-5: XP Calculation

```
Requirement: The system must calculate and track skill proficiency levels

Details:
- XP algorithm: frequency × recency × success_metrics
- Proficiency levels: Novice (0-25) → Intermediate (25-50) → Advanced (50-75) → Expert (75-100)
- Compound XP: related skills boost each other
- Decay: older evidence has lower weight
- Transparency: users see how XP is calculated
```

### FR-6: Visualization APIs

```
Requirement: The system must provide data for visualization

Details:
- Graph export: node/edge lists with attributes
- Timeline export: chronological events with metadata
- Frontier export: gap analysis + exploration vectors
- Real-time updates: as new outcomes are added
- Multiple formats: JSON, CSV, GraphML
```

### FR-7: Idea Indexing & Matching

```
Requirement: The system must connect old ideas to current work

Details:
- Index historical ideas (from memory pearls, old repos, etc.)
- Relevance matching: when is idea X relevant to project Y?
- Timeline tracking: first mention → current relevance
- Actionable notifications: surface relevant old ideas
- Manual linking: user can connect ideas explicitly
```

---

## Non-Functional Requirements

### NFR-1: Emergence Design

The system must be designed to discover patterns that weren't pre-categorized.

**Implications**:
- Use graph DB (not relational) for flexible relationship discovery
- LLM-driven categorization, not fixed taxonomies
- Allow new skill categories to emerge without schema changes
- Support serendipitous surfacing (memory pearls)

### NFR-2: Continuous Operation

The system must support ongoing data capture without interruption.

**Implications**:
- Minimal friction in outcome logging
- Background processing (don't block on LLM analysis)
- Real-time updates to visualization
- Data consistency guarantees

### NFR-3: Evidence-Based

All claims about skills must be supported by documented evidence.

**Implications**:
- Every skill claim links to outcomes
- Proof-of-work linking (commits, URLs, dates)
- Transparency: users see the evidence
- No self-assessment without supporting work

### NFR-4: Scalability

The system must handle growth as projects, skills, and ideas accumulate.

**Implications**:
- Graph queries must remain fast (sub-second)
- Support 1000+ skills, 10000+ outcomes
- Memory efficient pearl storage
- Efficient negative space identification

### NFR-5: Privacy & Control

Nicolas maintains full control over what's visible and shareable.

**Implications**:
- Fine-grained visibility controls (public/private/shared)
- Ability to exclude sensitive outcomes
- Control over what appears in public visualization
- Export/backup of all data

---

## Technical Architecture

### Technology Stack

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| **Temporal Graph Ingestion** | **Graphiti** | Specifically designed for temporal knowledge graphs; tracks when events happened, how relationships evolved over time; critical for convergence detection and time-to-traction visibility |
| Knowledge Graph Storage | Neo4j (with temporal extensions) | Flexible relationship modeling; negative space visibility; compatible with Graphiti output |
| LLM Integration | Claude API | Pattern extraction; skill emergence recognition; relationship inference |
| Knowledge Pearl Storage | PostgreSQL or Vector DB | Structured pearl metadata + full-text search + embeddings for serendipity matching |
| Visualization | D3.js / Cytoscape.js | Interactive graph visualization with temporal animation |
| Timeline Engine | Custom timeline with temporal queries | Chronological event visualization; show convergence moments |
| Backend | Node.js / Python (FastAPI) | API for pearl capture, graph queries, LLM orchestration, temporal analysis |
| Frontend | React/Vue | Interactive visualizations, pearl capture, exploration interface |
| Hosting | TBD (Vercel, Netlify, self-hosted) | Integrated with existing portfolio |

### Data Model

#### Nodes

```
Skill
  - id: UUID
  - name: string
  - category: string (inferred or manual)
  - xp: integer (0-100)
  - proficiency: enum (Novice, Intermediate, Advanced, Expert)
  - evidence_count: integer
  - created_at: timestamp
  - last_updated: timestamp
  - tags: string[]

Project/Outcome
  - id: UUID
  - name: string
  - date: timestamp
  - description: string
  - skills_involved: Skill[]
  - success_metrics: object
  - proof_of_work: string (URL, git commit, etc.)
  - status: enum (Completed, In Progress, On Hold)
  - created_at: timestamp

MemoryPearl
  - id: UUID
  - content: string
  - created_at: timestamp
  - tags: string[]
  - source: string (optional)
  - activated_date: timestamp (when did this become relevant?)
  - status: enum (Dormant, Active, Archived)

Idea
  - id: UUID
  - title: string
  - description: string
  - first_mentioned: timestamp
  - current_relevance_score: float (0-1)
  - related_projects: Project[]
  - status: enum (Seed, Developing, Converged, Abandoned)
```

#### Edges

```
Skill -[COMPOUNDS_WITH]-> Skill
  Related to compound learning (knowing A makes B easier)

Skill -[DEPENDS_ON]-> Skill
  Prerequisite relationship

Skill -[RELATED_TO]-> Skill
  General relatedness

Project -[DEMONSTRATES]-> Skill
  Outcome provides evidence for skill

MemoryPearl -[RELATES_TO]-> Skill
  Pearl touches on this skill domain

Idea -[MANIFESTS_IN]-> Project
  Idea realized in a project

Idea -[RELATED_TO]-> Idea
  Ideas connected to each other
```

### Workflow Architecture

#### Phase 1: Continuous Intake

```
Input → Outcome Logging
       ↓
     Validation
       ↓
   Graph Storage (outcomes, skills tagged)
       ↓
  XP Calculation (automated)
       ↓
  Pattern Detection (background LLM batch)
```

#### Phase 2: Batch Analysis

```
LLM Analysis (nightly/weekly)
     ↓
Skill Discovery
     ↓
Relationship Inference
     ↓
Cluster Identification
     ↓
Human Validation
     ↓
Graph Update
     ↓
Visualization Refresh
```

#### Phase 3: Serendipity Engine

```
New Outcome Added
     ↓
Find Related Outcomes/Skills
     ↓
Query Memory Pearls for Relevance
     ↓
Surface Adjacent Ideas
     ↓
User Notification (optional)
```

---

## Phasing & Milestones

### Phase 1: Foundation (Weeks 1-4)

**Goal**: Build the core data capture and graph structure

**Deliverables**:
- [ ] Outcome logging mechanism (CLI or markdown-based)
- [ ] Graph DB schema + initial data model
- [ ] XP calculation engine
- [ ] Basic graph query API
- [ ] Manual skill tagging workflow

**Success Criteria**:
- Can log 3-5 outcomes/week
- Outcomes persist in graph with skill relationships
- XP calculations are accurate and transparent
- Graph queries respond in <500ms

**Effort**: 3-4 weeks

---

### Phase 2: Emergence (Weeks 5-8)

**Goal**: Add LLM-driven pattern discovery and memory pearls

**Deliverables**:
- [ ] Memory pearl capture system
- [ ] LLM skill discovery pipeline
- [ ] Relationship inference engine
- [ ] Pearl serendipity matching
- [ ] Manual validation UI

**Success Criteria**:
- LLM discovers 3-5 new skill clusters per batch
- Memory pearls surface naturally when relevant
- Validation workflow is smooth (< 2 min per discovered skill)
- No user friction in pearl creation

**Effort**: 3-4 weeks

---

### Phase 3: Visualization (Weeks 9-12)

**Goal**: Make the knowledge graph visible and navigable

**Deliverables**:
- [ ] Interactive graph visualization
- [ ] Timeline view (narrative arc)
- [ ] Exploration frontier mapping
- [ ] Skill detail pages
- [ ] Public/private visibility controls

**Success Criteria**:
- Graph renders smoothly (60 FPS)
- Hover/click interactions are responsive
- Timeline shows meaningful events
- Frontier mapping identifies real exploration vectors

**Effort**: 3-4 weeks

---

### Phase 4: Integration & Polish (Weeks 13+)

**Goal**: Integrate into portfolio site and refine UX

**Deliverables**:
- [ ] Portfolio site integration
- [ ] Foundational pages (Graph, Timeline, Frontier, Memory)
- [ ] SEO & shareability
- [ ] Performance optimization
- [ ] User testing & refinement

**Success Criteria**:
- System feels integrated with portfolio
- Load times < 2 seconds
- Visitors can explore without guidance
- Sharing skill insights is natural

**Effort**: 2-3 weeks (ongoing refinement)

---

## Success Metrics

### Quantitative Metrics

| Metric | Target | Why It Matters |
|--------|--------|----------------|
| Outcomes Captured / Month | 10-15 | Proof of continuous input |
| Skill Clusters Discovered | 15-25 | Evidence of pattern emergence |
| XP Spread Distribution | 20-80 range | Shows diverse capability levels |
| Memory Pearls Activated | 10-20% per month | Shows serendipity engine effectiveness |
| Graph Query Response Time | < 500ms | System usability |
| Visitor Engagement (Time on Page) | 3-5 min | Indicates compelling visualization |

### Qualitative Metrics

- **Does the system reveal patterns I didn't see?** (Emergence success)
- **Do I trust the skill levels?** (Evidence quality)
- **Are exploration frontiers genuinely valuable?** (Safety + learning value)
- **Do old ideas resurface naturally?** (Serendipity quality)
- **Does the narrative feel true?** (Authenticity of growth story)

---

## Risks & Mitigation

### Risk 1: Data Quality
**Issue**: Outcomes logged inconsistently; skill tagging is subjective

**Mitigation**:
- Clear outcome schema with validation
- LLM double-checks skill assignments
- Manual review workflow before graph update
- Historical refinement process

### Risk 2: LLM Hallucination
**Issue**: LLM invents skills or relationships that aren't real

**Mitigation**:
- Human validation required for new skills
- Confidence scoring to highlight uncertain extractions
- Audit trail showing LLM reasoning
- Fallback to manual categorization

### Risk 3: Historical Data Messy
**Issue**: Old repos/issues are scattered and unstructured

**Mitigation**:
- Make historical data optional (Phase 1 uses current work only)
- Iterative ingestion (don't require completeness upfront)
- LLM preprocessing to normalize old data
- Accept "good enough" extraction initially

### Risk 4: Performance Degradation
**Issue**: Graph queries slow down as data accumulates

**Mitigation**:
- Regular graph optimization/reindexing
- Query caching for common patterns
- Archival of old outcomes (keep recent hot)
- Monitoring + alerting on slow queries

### Risk 5: Motivation Decay
**Issue**: Stops logging outcomes after initial enthusiasm

**Mitigation**:
- Minimal friction in logging (< 1 minute per outcome)
- Integration with existing workflows (git hooks, project templates)
- Visualization motivation (seeing growth compounds engagement)
- Community/sharing features (show others your journey)

---

## Open Questions & Decisions

### Q1: Input Mechanism
**Question**: How should outcomes be logged? (CLI, markdown, web form, git hooks, etc.)

**Options**:
- A) Simple markdown in project directory
- B) CLI tool for rapid logging
- C) Web form (requires login)
- D) Git commit hook integration
- E) Combination approach

**Recommendation**: Start with markdown (lowest friction), add CLI as Phase 2 enhancement

### Q2: Visibility Model
**Question**: What's the default visibility for outcomes? (public, private, shared)

**Options**:
- A) Everything public by default
- B) Everything private; manual sharing
- C) Sensible defaults (outcomes public, vulnerabilities private)
- D) Outcome-by-outcome decision

**Recommendation**: Sensible defaults (outcomes public, internals private)

### Q3: LLM Frequency
**Question**: How often should LLM analysis run? (real-time, nightly, weekly, manual)

**Options**:
- A) Real-time (expensive, slow)
- B) Nightly (good balance)
- C) Weekly (lower cost, might miss patterns)
- D) Manual trigger (most control)

**Recommendation**: Nightly batch with manual trigger option

### Q4: Historical Data Scope
**Question**: Should Phase 1 try to ingest past work, or focus only on forward?

**Options**:
- A) Forward-only (simpler, faster to MVP)
- B) Light historical (cherry-pick important repos)
- C) Deep historical (comprehensive archaeology)

**Recommendation**: Forward-only for Phase 1; historical becomes Phase 3+ option

---

## Conclusion

This product represents a fundamental shift in how personal capabilities are documented and understood. Rather than static portfolios, it's a **living knowledge system** that:

- Proves what you can do (evidence-based)
- Shows how you grow (narrative arc visible)
- Maps safe learning (fog of war navigation)
- Revives old ideas (convergence discovery)
- Reveals what you don't know (emergence design)

The phased approach allows shipping value quickly (Phase 1: capture + basic graph) while building toward the full vision (Phase 4: visualization + integration). Each phase compounds on the previous one, and the system becomes more valuable as evidence accumulates.

**Next Steps**:
1. ✅ Validate PRD with stakeholders (this discussion)
2. ⏳ Design UX/UI for foundational pages (Phase 3)
3. ⏳ Build Phase 1 prototype (Weeks 1-4)
4. ⏳ Iterate based on real usage
