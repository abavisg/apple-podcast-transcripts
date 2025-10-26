# Product Requirements Document (PRD)
## Apple Podcast Transcript Viewer

**Version:** 2.0 (Next Generation)
**Date:** January 26, 2025
**Status:** Planning Phase
**Owner:** Engineering Team
**Stakeholders:** End Users, Contributors, Open Source Community

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Product Vision & Goals](#product-vision--goals)
3. [Target Users & Personas](#target-users--personas)
4. [Current State (v1.0)](#current-state-v10)
5. [Proposed Solution (v2.0)](#proposed-solution-v20)
6. [Feature Roadmap](#feature-roadmap)
7. [Technical Architecture](#technical-architecture)
8. [Desktop App Strategy](#desktop-app-strategy)
9. [Success Metrics & KPIs](#success-metrics--kpis)
10. [Go-to-Market Strategy](#go-to-market-strategy)
11. [Competitive Analysis](#competitive-analysis)
12. [Risk Assessment](#risk-assessment)
13. [Implementation Timeline](#implementation-timeline)
14. [Open Questions](#open-questions)

---

## Executive Summary

### The Problem

Apple Podcasts app on macOS stores podcast transcripts locally but provides no way to:
- Export or copy large portions of text
- Search across transcripts
- Save transcripts for offline reference
- Analyze content with AI
- Organize favorite episodes

Users who want to reference, quote, or analyze podcast content are forced to manually copy small sections or re-listen to find information.

### The Solution

A **privacy-first, local-first web and desktop application** that:
1. Reads Apple's local podcast transcript files
2. Displays them in a clean, searchable interface
3. Enables export to multiple formats (Markdown, TXT, PDF)
4. Provides AI-powered analysis using local LLMs (Ollama)
5. Offers advanced organization features (search, favorites, stats)

### Why Now?

- **Podcasts are booming:** 464M podcast listeners globally (2024)
- **AI is accessible:** Local LLMs (Ollama) make privacy-preserving AI feasible
- **Privacy concerns:** Users want alternatives to cloud-based transcript services
- **No alternatives:** Existing solutions require uploads or subscriptions

### Success Criteria

- **Adoption:** 10K+ users in first 6 months
- **Engagement:** 60% weekly active user rate
- **Quality:** <5% error rate in transcript extraction
- **Performance:** <2s load time for 100 episodes
- **Privacy:** 100% local processing (no data leaves device)

---

## Product Vision & Goals

### Vision Statement

> **"Make podcast knowledge accessible, searchable, and actionable—while keeping user data private and secure."**

### Long-term Vision (12-24 months)

Transform from a simple transcript viewer into a comprehensive **podcast knowledge management system** that:
- Integrates with note-taking apps (Obsidian, Notion)
- Provides cross-episode semantic search
- Generates knowledge graphs from content
- Supports collaborative annotation
- Offers publisher/creator tools

### Core Principles

1. **Privacy First** - No data leaves user's device unless explicitly chosen
2. **Offline First** - Works without internet after initial load
3. **User Control** - User is always the gatekeeper for AI features
4. **Simplicity** - Easy to use for non-technical users
5. **Open Source** - Community-driven development

### Primary Goals (v2.0)

**Goal 1: Enhance Usability**
- Reduce friction in accessing transcripts
- Add search and filtering capabilities
- Improve export options

**Goal 2: Add Intelligence**
- Local AI summaries (Ollama integration)
- Resource extraction (books, tools, people mentioned)
- Actionable insights extraction

**Goal 3: Scale Experience**
- Support hundreds of episodes without performance degradation
- Persistent folder access (no repeated drag-and-drop)
- Better organization (favorites, bookmarks)

**Goal 4: Expand Reach**
- Desktop app (macOS initially, Windows/Linux later)
- Better accessibility (keyboard nav, screen readers)
- Mobile-friendly web version

---

## Target Users & Personas

### Primary Persona: Knowledge Worker Kevin

**Demographics:**
- Age: 28-45
- Occupation: Software developer, researcher, writer
- Tech-savvy, privacy-conscious
- Listens to 3-5 hours of podcasts weekly

**Goals:**
- Extract quotes and insights for articles/presentations
- Reference technical discussions from podcasts
- Build personal knowledge base

**Pain Points:**
- Can't easily copy transcript sections
- Needs to search across multiple episodes
- Wants to integrate with existing note-taking workflow

**How our product helps:**
- One-click Markdown export to Obsidian
- Full-text search across all episodes
- AI summaries highlight key points

### Secondary Persona: Content Creator Clara

**Demographics:**
- Age: 25-40
- Occupation: Blogger, YouTuber, podcaster
- Creative professional
- Researches topics extensively

**Goals:**
- Find references and resources mentioned in podcasts
- Gather inspiration for content
- Fact-check claims

**Pain Points:**
- Hard to find specific moments in 2-hour episodes
- Can't extract list of books/tools mentioned
- Loses track of interesting episodes

**How our product helps:**
- AI resource extraction (books, tools, links)
- Favorites and bookmarks
- Search by topic or keyword

### Tertiary Persona: Academic Alice

**Demographics:**
- Age: 22-35
- Occupation: PhD student, professor
- Research-focused
- Analyzes podcast content for studies

**Goals:**
- Quote podcasts in papers
- Analyze trends in discourse
- Export citations

**Pain Points:**
- No proper citation format for podcasts
- Can't batch export transcripts
- Needs metadata for references

**How our product helps:**
- Structured Markdown with frontmatter
- Batch export functionality
- Metadata preservation (date, host, episode)

### User Needs Summary

| Need | Priority | Current Solution | Planned Solution |
|------|----------|------------------|------------------|
| **Export transcripts** | Critical | ❌ None | ✅ MD/TXT/PDF (SPEC-001, 011, 012) |
| **Search content** | High | ❌ None | ✅ Full-text search (SPEC-009) |
| **AI summaries** | High | ❌ None | ✅ Ollama integration (SPEC-002-005) |
| **Organize favorites** | Medium | ❌ None | ✅ Bookmarks (SPEC-010) |
| **Persistent access** | Medium | ⚠️ Drag/drop each time | ✅ Folder selection (SPEC-007) |
| **Accessibility** | Medium | ⚠️ Limited | ✅ Full support (SPEC-014-016) |
| **Performance** | Medium | ⚠️ Slows with 100+ episodes | ✅ Virtual scroll (SPEC-017) |

---

## Current State (v1.0)

### What Exists Today

**Live Demo:** https://alexbeals.com/projects/podcasts/

**Core Features:**
- ✅ Drag-and-drop folder interface
- ✅ Reads local Apple Podcasts transcripts (.ttml files)
- ✅ Queries SQLite database for metadata
- ✅ Custom SQL.js with WAL support
- ✅ Displays episode cards with metadata
- ✅ Modal popup for full transcript viewing
- ✅ Copy-to-clipboard functionality
- ✅ 100% client-side (privacy-preserving)
- ✅ Works offline

**Technical Details:**
- **Architecture:** Single 562-line HTML file
- **Language:** Vanilla JavaScript
- **Dependencies:** SQL.js (custom WAL build)
- **Deployment:** Static site (GitHub Pages)
- **Browser Support:** Modern browsers with FileReader API

### User Feedback & Analytics

**GitHub Stats (as of Jan 2025):**
- ⭐ Stars: TBD (check repo)
- 🍴 Forks: TBD
- 📊 Issues: Active community engagement

**Common User Requests:**
1. "Can you add export to Markdown?" (most requested)
2. "Search would be amazing"
3. "I have 300+ episodes, gets slow"
4. "Native Mac app version?"
5. "AI summaries would save time"

**Known Issues:**
- Performance degrades with 100+ episodes
- No search functionality
- No export options beyond copy-paste
- No way to save folder selection (must drag each time)
- Mobile experience suboptimal

---

## Proposed Solution (v2.0)

### Vision for v2.0

Transform from **simple transcript viewer** → **intelligent podcast knowledge assistant**

### Key Enhancements

#### 1. **Smarter Interface**
- Persistent folder access (File System Access API)
- Auto-refresh for new episodes
- Search and filter across all content
- Bookmark/favorite episodes
- Virtual scrolling for performance

#### 2. **AI-Powered Insights** (Privacy-Preserving)
- Local LLM integration (Ollama)
- Quick summaries (2-3 sentences)
- Detailed summaries (300-500 words)
- Resource extraction (books, tools, people)
- Actionable items extraction

#### 3. **Enhanced Export**
- Markdown export with frontmatter
- Plain text export
- PDF export with formatting
- Batch export multiple episodes

#### 4. **Better Accessibility**
- Complete keyboard navigation
- Screen reader support (ARIA)
- High contrast mode
- Font size controls

#### 5. **Desktop App**
- Native macOS app (Tauri or PWA)
- System tray integration (optional)
- Native file dialogs
- Better file system access

#### 6. **Analytics & Visualization**
- Listening statistics
- Timeline view
- Word clouds from transcripts
- Topic clustering

---

## Feature Roadmap

### Phase 1: Quick Wins (2-3 weeks)
**Goal:** Improve core usability immediately

| Spec | Feature | Impact | Effort |
|------|---------|--------|--------|
| SPEC-000 | Refactoring | Foundation | 2 weeks |
| SPEC-001 | Markdown Export | High | 2-3 days |
| SPEC-009 | Search & Filter | High | 3-4 days |
| SPEC-014 | Keyboard Navigation | Medium | 2-3 days |
| SPEC-015 | Screen Reader Support | Medium | 2-3 days |
| SPEC-016 | Visual Accessibility | Medium | 2-3 days |

**Deliverables:**
- Refactored codebase (TypeScript, modular)
- Markdown export functionality
- Full-text search
- Full accessibility compliance

---

### Phase 2: AI Features (4-5 weeks)
**Goal:** Add local AI-powered intelligence

| Spec | Feature | Impact | Effort |
|------|---------|--------|--------|
| SPEC-006 | Ollama Core Integration | Critical (blocker) | 5-7 days |
| SPEC-002 | Quick Summaries | High | 3-4 days |
| SPEC-003 | Detailed Summaries | High | 3-4 days |
| SPEC-004 | Resource Extraction | High | 4-5 days |
| SPEC-005 | Actionable Items | Medium | 3-4 days |

**Deliverables:**
- Ollama integration infrastructure
- 4 AI-powered features
- IndexedDB caching for AI results
- Model selection UI

**Dependencies:**
- SPEC-006 must complete before SPEC-002-005
- User must have Ollama installed locally

---

### Phase 3: Enhanced UX (3-4 weeks)
**Goal:** Reduce friction, improve organization

| Spec | Feature | Impact | Effort |
|------|---------|--------|--------|
| SPEC-007 | File System Access API | High | 4-5 days |
| SPEC-008 | Folder Refresh | Medium | 2-3 days |
| SPEC-010 | Bookmarks & Favorites | Medium | 2-3 days |
| SPEC-011 | Plain Text Export | Low | 1-2 days |
| SPEC-012 | PDF Export | Medium | 3-4 days |
| SPEC-013 | Batch Export | Medium | 2-3 days |

**Deliverables:**
- Persistent folder selection
- Favorites system
- Multiple export formats
- Batch operations

**Browser Compatibility:**
- File System Access API: Chrome, Edge, Brave only
- Fallback: Drag-and-drop still available

---

### Phase 4: Performance & Analytics (3-4 weeks)
**Goal:** Scale to hundreds of episodes, add insights

| Spec | Feature | Impact | Effort |
|------|---------|--------|--------|
| SPEC-017 | Virtual Scrolling | Medium | 3-4 days |
| SPEC-018 | Web Workers | Medium | 3-4 days |
| SPEC-019 | Service Worker | Low | 3-4 days |
| SPEC-020 | Timeline & Stats | Low | 3-4 days |
| SPEC-021 | Word Clouds | Low | 3-4 days |

**Deliverables:**
- Support for 1000+ episodes
- Offline-first architecture
- Analytics dashboard
- Visual insights

---

### Total Roadmap Summary

**Timeline:** 12-16 weeks
**Total Specs:** 21 features + 1 refactoring
**Estimated Effort:** 65-85 developer days

**Phased Approach Benefits:**
- Users get value incrementally
- Can pivot based on feedback
- Lower risk (test each phase before next)

---

## Technical Architecture

### Current Architecture (v1.0)

```
┌─────────────────────────────────────┐
│         index.html (562 lines)      │
│  ┌─────────────────────────────┐   │
│  │  Inline CSS (lines 1-280)   │   │
│  └─────────────────────────────┘   │
│  ┌─────────────────────────────┐   │
│  │  Inline JS (lines 281-562)  │   │
│  │  - Drag/drop handling        │   │
│  │  - TTML parsing             │   │
│  │  - SQLite reading           │   │
│  │  - UI rendering             │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
         ↓
    ┌────────┐
    │ SQL.js │ (Custom WAL build)
    └────────┘
```

**Pros:**
- Simple deployment
- No build step
- Privacy-preserving

**Cons:**
- Hard to maintain
- Not scalable
- No testing
- No modularity

---

### Proposed Architecture (v2.0)

**Path B: Modern Web Architecture (Recommended)**

```
┌──────────────────────────────────────────────────┐
│                   Frontend                        │
│  ┌────────────────────────────────────────────┐  │
│  │  React/Vanilla Components                 │  │
│  │  - EpisodeCard, Modal, DragDropZone       │  │
│  └────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────┐  │
│  │  Services Layer                            │  │
│  │  - TTMLParser                              │  │
│  │  - SQLiteService                           │  │
│  │  - OllamaClient (AI)                       │  │
│  │  - FileSystemService                       │  │
│  └────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────┐  │
│  │  State Management (Zustand)                │  │
│  │  - EpisodeStore                            │  │
│  │  - UIStore                                 │  │
│  │  - SettingsStore                           │  │
│  └────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────┐  │
│  │  Caching Layer (IndexedDB)                 │  │
│  │  - AI responses cache                      │  │
│  │  - Folder handle persistence               │  │
│  │  - User settings                           │  │
│  └────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────┘
         ↓                           ↓
    ┌────────┐                 ┌──────────┐
    │ SQL.js │                 │  Ollama  │
    │  (WAL) │                 │(localhost│
    └────────┘                 │  :11434) │
                               └──────────┘
```

**Tech Stack:**
- **Language:** TypeScript
- **Build:** Vite
- **State:** Zustand
- **Testing:** Vitest
- **Styling:** CSS Modules or Tailwind
- **Storage:** IndexedDB (caching)
- **Desktop:** PWA or Tauri (TBD)

**Benefits:**
- Type safety (TypeScript)
- Modular architecture
- Testable code
- Scalable to all 21 specs
- Desktop-ready

---

## Desktop App Strategy

### Problem Statement

**Web version limitations:**
- Browser security restrictions
- Less "native" feel
- Harder to discover (not in App Store)
- Limited OS integration

### Desktop App Goals

1. **Better File Access** - Unrestricted or persistent
2. **Native Feel** - Feels like a Mac/Windows app
3. **Discoverability** - Searchable in app stores
4. **Offline-First** - No dependency on web server
5. **Auto-Updates** - Seamless version updates

### Technology Options

See [TECH-DECISION-tauri-vs-pwa.md](docs/TECH-DECISION-tauri-vs-pwa.md) for full analysis.

**Option 1: Progressive Web App (PWA)**
- Build: Web app + Service Worker
- Install: "Add to Dock" via browser
- Distribution: Web + direct install
- Pros: Fast dev, instant updates, smaller bundle
- Cons: Not in App Store, limited native features

**Option 2: Tauri (Rust + Web)**
- Build: Rust backend + web frontend
- Install: Native app installer (.dmg, .exe)
- Distribution: App stores + direct download
- Pros: Full native access, smaller than Electron
- Cons: Rust learning curve, slower dev

**Preliminary Recommendation: PWA**
- File System Access API works in Chrome/Edge
- Ollama integration works over HTTP
- Faster to market
- Can upgrade to Tauri later if needed

### Platform Support

**Priority 1 (v2.0):**
- macOS (primary target - where Apple Podcasts lives)

**Priority 2 (v2.1):**
- Windows (if Apple Podcasts for Windows exists/syncs)
- Linux (community demand)

### Desktop-Specific Features

| Feature | PWA | Tauri |
|---------|-----|-------|
| File System Access | ✅ (Limited API) | ✅ (Full) |
| Auto-launch on startup | ❌ | ✅ |
| System tray integration | ❌ | ✅ |
| Native notifications | ✅ (Web API) | ✅ (Native) |
| Deep linking | ⚠️ (Limited) | ✅ |
| Offline mode | ✅ (Service Worker) | ✅ (Built-in) |
| Auto-updates | ✅ (Instant) | ✅ (With framework) |

---

## Success Metrics & KPIs

### Acquisition Metrics

**Goal:** 10,000 users in 6 months

| Metric | Target | Measurement |
|--------|--------|-------------|
| GitHub Stars | 1,000+ | GitHub API |
| Weekly Active Users | 3,000+ | Analytics (privacy-preserving) |
| Installs (Desktop) | 5,000+ | Download counter |
| Product Hunt upvotes | 500+ | Product Hunt |

### Engagement Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Weekly Active User % | 60% | Usage analytics |
| Avg. episodes viewed/user | 10+ | Local analytics |
| AI features usage % | 40% | Feature flags |
| Export usage % | 50% | Feature tracking |

### Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Transcript extraction error rate | <5% | Error logging |
| App crash rate | <1% | Error tracking |
| Load time (100 episodes) | <2s | Performance monitoring |
| GitHub issue response time | <48hrs | Issue tracker |

### Satisfaction Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| NPS (Net Promoter Score) | 50+ | User surveys |
| GitHub issue close rate | >80% | Issue tracker |
| Feature requests implemented | 30% | Roadmap tracking |

---

## Go-to-Market Strategy

### Launch Phases

**Phase 1: Soft Launch (Weeks 1-4)**
- Target: Existing users + early adopters
- Channels: GitHub, personal blog, Twitter/X
- Goal: Get feedback, fix critical bugs
- Metrics: 100 active users, <10 critical bugs

**Phase 2: Community Launch (Weeks 5-8)**
- Target: Tech-savvy podcast listeners
- Channels: Product Hunt, Hacker News, Reddit (r/podcasts, r/privacy)
- Goal: Reach 1,000 users, generate buzz
- Metrics: 1K users, top 5 Product Hunt, >50 upvotes HN

**Phase 3: Mainstream Push (Weeks 9-16)**
- Target: General podcast audience
- Channels: Podcast communities, YouTube demos, blog posts
- Goal: 5K users, establish brand
- Metrics: 5K users, 10+ blog mentions, YouTube demo views

**Phase 4: Desktop App Launch (Weeks 17-24)**
- Target: Power users, content creators
- Channels: App directories, Mac blogs, review sites
- Goal: 10K total users, App Store presence
- Metrics: 10K users, App Store ranking, reviews

### Marketing Channels

**Owned:**
- GitHub README (optimized for SEO)
- Project website/landing page
- Documentation site
- Blog (announcement posts)

**Earned:**
- Product Hunt launch
- Hacker News posts
- Reddit discussions
- Tech blog coverage (9to5Mac, MacStories)

**Community:**
- Open source contributions welcome
- Discord/Slack community (optional)
- Twitter/X engagement
- YouTube tutorial videos

### Key Messaging

**Tagline:** "Your podcast transcripts, searchable and AI-enhanced—privately."

**Value Props:**
1. **Privacy-First** - "Your data never leaves your device"
2. **AI-Powered** - "Local LLM summaries with zero cloud dependency"
3. **Free & Open** - "Open source, no subscriptions"
4. **Easy Export** - "Markdown, PDF, text—your choice"
5. **Power Features** - "Search, favorites, analytics"

---

## Competitive Analysis

### Direct Competitors

**1. Manual Copy-Paste (Current "Solution")**
- Pros: Free, built-in
- Cons: Tedious, limited, no export
- **Our advantage:** 100x faster, AI insights

**2. Descript ($12-24/month)**
- Pros: Professional transcription, editing
- Cons: Paid, cloud-based, privacy concerns, overkill
- **Our advantage:** Free, local, privacy-first

**3. Otter.ai (Free-$20/month)**
- Pros: Good transcription, cloud sync
- Cons: Paid tiers, cloud-only, not for Apple Podcasts
- **Our advantage:** Works with Apple Podcasts, free, private

**4. Podcast transcript websites (Listen Notes, etc.)**
- Pros: Web-based, searchable
- Cons: Require upload, privacy issues, limited features
- **Our advantage:** No upload, full control

### Indirect Competitors

**5. Note-taking apps (Notion, Obsidian)**
- Use case: Users manually paste transcripts
- **Our advantage:** Direct integration, auto-export

**6. Browser extensions**
- None exist specifically for Apple Podcasts
- **Opportunity:** We're first to market

### Competitive Positioning

**Our unique position:**
> "The only privacy-preserving, AI-enhanced, free & open-source tool for Apple Podcast transcripts."

**Moat:**
1. **Custom SQL.js WAL support** - Technical barrier
2. **Local AI integration** - Novel approach
3. **Open source** - Community trust
4. **Privacy focus** - Growing concern

---

## Risk Assessment

### Technical Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| **Apple changes file format** | Low | High | Monitor updates, version support |
| **Browser API deprecation** | Low | Medium | Feature detection, fallbacks |
| **Ollama integration fails** | Medium | Medium | Graceful degradation, clear docs |
| **Performance issues** | Medium | Medium | Virtual scrolling, Web Workers |
| **SQLite WAL changes** | Low | High | Test with new versions, community watch |

### Product Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| **Low user adoption** | Medium | High | Strong marketing, Product Hunt launch |
| **Users don't install Ollama** | High | Medium | Make AI features optional, good UX |
| **Feature creep** | Medium | Medium | Stick to roadmap, prioritize ruthlessly |
| **Support burden** | Medium | Low | Good docs, FAQ, community forum |

### Market Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| **Apple adds export feature** | Low | High | Pivot to advanced features (AI, etc.) |
| **Competitor launches similar tool** | Medium | Medium | Faster iteration, better UX |
| **Privacy regulations restrict local AI** | Very Low | Low | All processing local, compliant |

### Mitigation Strategies

1. **Apple risk:** Stay nimble, monitor changes, maintain fallbacks
2. **Adoption risk:** Focus on great UX, leverage Product Hunt
3. **Technical debt:** Refactor first (SPEC-000), maintain quality
4. **Support:** Comprehensive docs, video tutorials, active GitHub

---

## Implementation Timeline

### Overview

**Total Duration:** 12-16 weeks (3-4 months)
**Team Size:** 1-2 developers (can scale with contributions)
**Start Date:** TBD (after approval)
**Target Launch:** Q2 2025

### Detailed Timeline

**Weeks 1-2: Foundation (SPEC-000)**
- Refactor to TypeScript + Vite
- Set up testing infrastructure
- Establish CI/CD
- **Milestone:** Refactored v1.0 deployed

**Weeks 3-4: Phase 1 - Quick Wins**
- Markdown export (SPEC-001)
- Search & filter (SPEC-009)
- Accessibility (SPEC-014-016)
- **Milestone:** v2.0 Alpha release

**Weeks 5-9: Phase 2 - AI Features**
- Ollama core integration (SPEC-006)
- Quick summaries (SPEC-002)
- Detailed summaries (SPEC-003)
- Resource extraction (SPEC-004)
- Actionable items (SPEC-005)
- **Milestone:** v2.0 Beta release

**Weeks 10-13: Phase 3 - Enhanced UX**
- File System Access API (SPEC-007)
- Folder refresh (SPEC-008)
- Bookmarks (SPEC-010)
- Advanced exports (SPEC-011-013)
- **Milestone:** v2.0 RC (Release Candidate)

**Weeks 14-16: Phase 4 - Performance**
- Virtual scrolling (SPEC-017)
- Web Workers (SPEC-018)
- Service Worker (SPEC-019)
- Analytics & viz (SPEC-020-021)
- **Milestone:** v2.0 Production Launch

### Launch Readiness Checklist

- [ ] All Phase 1-2 specs implemented
- [ ] Test coverage >70%
- [ ] Performance benchmarks met
- [ ] Documentation complete
- [ ] Tutorial video created
- [ ] Product Hunt page ready
- [ ] Landing page live
- [ ] Analytics configured
- [ ] Support channels set up
- [ ] Press kit prepared

---

## Open Questions

### Technical Questions

1. **Desktop Technology:** Tauri or PWA? (See TECH-DECISION.md)
2. **Framework:** React, Vue, Svelte, or vanilla?
3. **CSS:** Tailwind, CSS Modules, or styled-components?
4. **Testing:** How much coverage is enough?
5. **Deployment:** Continue GitHub Pages or self-host?

### Product Questions

1. **Pricing:** Forever free or freemium later?
2. **Ollama UX:** How to make installation seamless?
3. **Mobile:** Should we build mobile version?
4. **Collaboration:** Allow teams/sharing features?
5. **Publishing:** Help podcast creators extract their own transcripts?

### Business Questions

1. **Sustainability:** How to fund development long-term?
2. **Support:** Community-only or dedicated support?
3. **Roadmap:** What comes after v2.0?
4. **Partnerships:** Integrate with Obsidian, Notion, etc.?

---

## Appendix

### Referenced Documents

- [SPEC-000: Refactoring Foundation](specs/SPEC-000-refactoring-foundation.md)
- [SPEC-001 through SPEC-021: Feature Specifications](specs/README.md)
- [TECH-DECISION: Tauri vs PWA](docs/TECH-DECISION-tauri-vs-pwa.md)
- [Current README](README.md)

### Glossary

- **TTML:** Timed Text Markup Language (XML format for transcripts)
- **WAL:** Write-Ahead Logging (SQLite feature)
- **Ollama:** Local LLM runtime (like Docker for AI models)
- **IndexedDB:** Browser database for client-side storage
- **PWA:** Progressive Web App
- **Tauri:** Desktop app framework (Rust + Web)

### Change Log

| Date | Version | Changes | Author |
|------|---------|---------|--------|
| 2025-01-26 | 1.0 | Initial PRD created | Engineering Team |

---

**End of PRD**

**Next Steps:**
1. Review and approve this PRD
2. Decide on desktop technology (Tauri vs PWA)
3. Begin SPEC-000 refactoring
4. Kick off Phase 1 development

**Questions or Feedback:** [Create a GitHub issue](https://github.com/dado3212/apple-podcast-transcripts/issues)
