# 📘 claude.md — Apple Podcast Transcript Viewer

**Project Intelligence Overview for AI Assistants**

---

## 🧠 Project Context

### Purpose
**What problem are we solving?**

Apple's Podcasts app on macOS stores transcripts locally but provides no way to export, search, or analyze them. Users who want to reference, quote, or study podcast content are forced to manually copy small sections or re-listen to find information.

**Our solution:** A privacy-first, local-first web/desktop application that reads Apple's local transcript files and provides:
- Export to multiple formats (Markdown, TXT, PDF)
- AI-powered analysis using local LLMs (Ollama)
- Full-text search and filtering
- Organization tools (favorites, bookmarks)
- Analytics and visualization

### Audience
**Who benefits and how?**

**Primary Users:**
1. **Knowledge Workers** (developers, researchers, writers)
   - Extract quotes and insights for articles/presentations
   - Reference technical discussions
   - Build personal knowledge base

2. **Content Creators** (bloggers, podcasters, YouTubers)
   - Find references and resources mentioned in episodes
   - Research topics for content creation
   - Extract lists of books/tools discussed

3. **Academics** (students, professors)
   - Quote podcasts in research papers
   - Analyze discourse trends
   - Export citations with proper metadata

### Success Criteria

**User Adoption:**
- 10,000+ users in first 6 months
- 60% weekly active user rate
- Top 5 on Product Hunt at launch

**Technical Quality:**
- <5% error rate in transcript extraction
- <2s load time for 100 episodes
- >70% test coverage
- Zero data breaches (100% local processing)

**Feature Delivery:**
- Complete SPEC-000 refactoring: 2 weeks
- Ship Phase 1 features: 4 weeks from start
- Launch v2.0 production: 12-16 weeks total

---

## 🧩 System Overview

### Current Architecture (v1.0)

**Tech Stack:**
- **Frontend:** Single 562-line HTML file (vanilla JavaScript, inline CSS)
- **Database:** SQL.js (SQLite compiled to WebAssembly) with custom WAL support
- **Deployment:** Static site (GitHub Pages)
- **Browser APIs:** FileReader, DOMParser, Drag & Drop

**Data Flow:**
```
User drags folder
    ↓
Recursively traverse files
    ↓
Parse .ttml (XML) transcripts → Extract text + metadata
Parse MTLibrary.sqlite + WAL → Query episode metadata
    ↓
Merge data → Render episode cards
    ↓
Click episode → Display full transcript in modal
```

**Key Innovation:** Custom-modified SQL.js to support SQLite WAL files (Write-Ahead Logging)

---

### Planned Architecture (v2.0)

**Tech Stack (Path B - Recommended):**
- **Language:** TypeScript
- **Build:** Vite (fast HMR, optimized builds)
- **State:** Zustand (lightweight reactive store)
- **Testing:** Vitest (Jest-compatible, Vite-native)
- **Styling:** CSS Modules or Tailwind CSS (TBD)
- **Desktop:** PWA (Progressive Web App)
  - Service Worker for offline support
  - File System Access API for persistent folder access
  - IndexedDB for caching AI results

**Architecture:**
```
Frontend (TypeScript)
  ├── Components
  │   ├── EpisodeCard
  │   ├── Modal
  │   ├── DragDropZone
  │   └── SearchBar
  ├── Services
  │   ├── TTMLParser (transcript parsing)
  │   ├── SQLiteService (database reading)
  │   ├── OllamaClient (AI integration)
  │   └── FileSystemService (folder access)
  ├── State (Zustand)
  │   ├── EpisodeStore
  │   ├── UIStore
  │   └── SettingsStore
  └── Utils
      ├── Formatters (date, time, duration)
      └── Validators

External Dependencies
  ├── SQL.js (custom WAL build) - SQLite in browser
  ├── Ollama (localhost:11434) - Local LLM for AI features
  └── Browser APIs (File System Access, Service Worker, IndexedDB)
```

---

### Data Sources

**Apple Podcasts Local Storage:**
```
~/Library/Group Containers/243LU875E5.groups.com.apple.podcasts/
├── Library/Cache/Assets/TTML/
│   └── *.ttml (XML transcript files)
└── Documents/
    ├── MTLibrary.sqlite (podcast metadata)
    └── MTLibrary.sqlite-wal (write-ahead log)
```

**Data We Extract:**
- **From TTML:** Transcript text, speaker attribution, episode ID
- **From SQLite:** Episode title, podcast name, author, date, duration, description

---

### External Integrations

**Ollama (Local LLM):**
- **Endpoint:** `http://localhost:11434/api/generate`
- **Purpose:** AI summaries, resource extraction, actionable items
- **Models:** llama3, mixtral, mistral (user-selectable)
- **Privacy:** 100% local, no cloud API calls

**No other external APIs** - Everything runs locally in browser.

---

## ⚙️ Build Discipline

### Development Workflow

**1. Ship in Tiny Slices**
- One feature per PR (ideally one spec at a time)
- Each spec has clear acceptance criteria
- Deploy often, get feedback early

**2. Test Before Push**
- Run `npm test` (Vitest) before committing
- Manual testing in Chrome, Firefox, Safari
- Verify no console errors
- Check File System API in Chrome/Edge

**3. Update Documentation**
- Mark spec as complete in `specs/README.md`
- Update `IMPROVEMENTS.md` progress tracker
- Add feature to main `README.md`
- Update PRD.md status if major milestone

**4. Follow Spec-Driven Development**
- ALWAYS read the full spec before coding
- Use acceptance criteria as implementation checklist
- Reference spec ID in commit messages (e.g., `feat(SPEC-001): Add markdown export`)
- Update spec status when complete

---

### Git Workflow

**Branch Naming:**
- `refactor/path-b` - For SPEC-000 refactoring
- `feat/SPEC-XXX-short-name` - For feature specs
- `fix/issue-description` - For bug fixes
- `docs/update-name` - For documentation

**Commit Messages:**
```
feat(SPEC-001): Add markdown export with frontmatter
fix(SPEC-007): Handle permission denied error
docs(PRD): Update Phase 1 status
refactor: Split TTMLParser into separate module
```

**PR Requirements:**
- Reference spec in description
- Include before/after screenshots (if UI change)
- All tests passing
- No TypeScript errors (`npm run type-check`)

---

### Code Quality Standards

**TypeScript:**
- Strict mode enabled
- No `any` types (use `unknown` if necessary)
- Proper interfaces for all data structures
- JSDoc comments for public APIs

> See [claude-typescript.md](claude-typescript.md) for detailed TypeScript standards, patterns, and code style guidelines.

**Testing:**
- Unit tests for parsers and utilities (>80% coverage)
- Integration tests for services
- Component tests for UI (if using framework)
- E2E tests for critical paths (optional)

**Performance:**
- Virtual scrolling for >100 episodes (SPEC-017)
- Web Workers for heavy parsing (SPEC-018)
- Code splitting for faster initial load
- Lighthouse score >90 (PWA)

---

## 📋 Feature Roadmap (Reference)

**IMPORTANT: All development must start with SPEC-000 (Refactoring)**

### Phase 0: Foundation ⚠️ **IN PROGRESS**
- **SPEC-000:** Refactoring Foundation (1-3 weeks, Path B recommended)

### Phase 1: Quick Wins (2-3 weeks)
- **SPEC-001:** Markdown Export
- **SPEC-009:** Search & Filter
- **SPEC-014-016:** Accessibility (Keyboard, Screen Reader, Visual)

### Phase 2: AI Features (4-5 weeks)
- **SPEC-006:** Ollama Core Integration (foundation)
- **SPEC-002:** Quick Summaries (2-3 sentences)
- **SPEC-003:** Detailed Summaries (300-500 words)
- **SPEC-004:** Resource Extraction (books, tools, people)
- **SPEC-005:** Actionable Items

### Phase 3: Enhanced UX (3-4 weeks)
- **SPEC-007:** File System Access API
- **SPEC-008:** Folder Refresh
- **SPEC-010:** Bookmarks & Favorites
- **SPEC-011-013:** Export Options (TXT, PDF, Batch)

### Phase 4: Performance & Analytics (3-4 weeks)
- **SPEC-017-019:** Performance (Virtual Scroll, Web Workers, Service Worker)
- **SPEC-020-021:** Data Viz (Timeline, Word Clouds)

**Full Details:** See [specs/README.md](specs/README.md)

---

## 🧪 Quality & Testing

### Testing Approach

**Test Pyramid:**
```
        /\
       /E2E\          ← Few (critical user paths)
      /──────\
     /Integration\    ← Some (services working together)
    /────────────\
   /  Unit Tests  \   ← Many (parsers, utils, formatters)
  /────────────────\
```

**Unit Tests (>80% coverage goal):**
- `TTMLParser` - Parsing TTML XML correctly
- `SQLiteService` - Querying database correctly
- `Formatters` - Date, time, duration formatting
- `Validators` - Input validation logic

**Integration Tests:**
- File System Access API flow
- Ollama API communication
- IndexedDB caching behavior
- Service Worker offline functionality

**E2E Tests (Optional, for critical paths):**
- Drag folder → Display episodes
- Click episode → View transcript
- Generate AI summary → Cache result
- Export to markdown → Download file

**Testing Tools:**
- **Unit/Integration:** Vitest
- **E2E:** Playwright (optional)
- **Coverage:** Vitest coverage (c8)

**Testing Standards:**
> See [claude-testing.md](claude-testing.md) for detailed testing philosophy, test architecture, and anti-patterns to avoid.

---

### Quality Checks

**Before Every PR:**
- [ ] `npm test` passes (all tests green)
- [ ] `npm run type-check` passes (no TypeScript errors)
- [ ] `npm run lint` passes (ESLint clean)
- [ ] Manual testing in Chrome
- [ ] No console errors or warnings

**Before Release:**
- [ ] Test on macOS, Windows (if applicable)
- [ ] Test in Chrome, Firefox, Safari, Edge
- [ ] File System API works (Chrome/Edge)
- [ ] Ollama integration works
- [ ] Performance: 100 episodes load <2s
- [ ] Lighthouse PWA score >90
- [ ] Accessibility audit passes (WAVE, axe)

---

## 🛠 Technology Stack

### Current (v1.0)
- Vanilla JavaScript (ES6)
- HTML5 + CSS3
- SQL.js (custom WAL build)
- GitHub Pages hosting

### Planned (v2.0 - Path B)
- **TypeScript** 5.x
- **Vite** 5.x (build tool)
- **Zustand** 4.x (state management)
- **Vitest** 1.x (testing)
- **PWA** (Progressive Web App)
  - Service Worker API
  - File System Access API
  - IndexedDB
  - Web App Manifest

### Optional (Future)
- **React** or **Svelte** (if Path C chosen)
- **Tailwind CSS** (utility-first styling)
- **Tauri** (if PWA proves insufficient)

---

## 🔑 Key Technical Decisions

### 1. Desktop Technology: PWA (Not Tauri)
**Decision:** Start with Progressive Web App
**Rationale:**
- File System Access API sufficient for SPEC-007
- Ollama works over HTTP (no native process needed)
- 2x faster development (TypeScript vs Rust)
- Can upgrade to Tauri later if needed

**Full Analysis:** [docs/TECH-DECISION-tauri-vs-pwa.md](docs/TECH-DECISION-tauri-vs-pwa.md)

---

### 2. Refactoring Path: Path B (Modern Web)
**Decision:** TypeScript + Vite + Modular Architecture
**Rationale:**
- Type safety for 21 complex features
- Scalable architecture
- Testable codebase
- Good balance of effort vs benefit

**Full Details:** [specs/SPEC-000-refactoring-foundation.md](specs/SPEC-000-refactoring-foundation.md)

---

### 3. No Cloud Services
**Decision:** 100% local processing
**Rationale:**
- Privacy-first promise to users
- No server costs
- Works offline
- No GDPR/privacy compliance complexity

**Exceptions:** None. Even AI features use local Ollama.

---

## 📚 Key Documents

### Core Documentation
- **[README.md](README.md)** - User-facing documentation, how to use
- **[PRD.md](PRD.md)** - Product Requirements Document (v2.0 vision)
- **[IMPROVEMENTS.md](IMPROVEMENTS.md)** - High-level roadmap

### Technical Specifications
- **[specs/README.md](specs/README.md)** - Index of all 22 specs
- **[specs/SPEC-000-refactoring-foundation.md](specs/SPEC-000-refactoring-foundation.md)** - Architecture refactoring (START HERE)
- **[specs/SPEC-001 through SPEC-021](specs/)** - Individual feature specs

### Decision Documents
- **[docs/TECH-DECISION-tauri-vs-pwa.md](docs/TECH-DECISION-tauri-vs-pwa.md)** - PWA vs Tauri analysis

### Generic Guides (Reference)
- **[claude-generic.md](claude-generic.md)** - General engineering discipline
- **[claude-testing.md](claude-testing.md)** - Testing philosophy, test pyramid, anti-patterns
- **[claude-typescript.md](claude-typescript.md)** - TypeScript standards, patterns, code style

---

## 🚀 Getting Started (For AI Assistants)

### When Helping with This Project

**1. Always Start with Context**
- Read this file first (claude.md)
- Check current phase in [specs/README.md](specs/README.md)
- Review relevant spec before coding

**2. Follow the Roadmap**
- SPEC-000 must complete before any feature work
- Respect dependencies (SPEC-006 blocks SPEC-002-005)
- Don't skip phases

**3. Reference Specifications**
- Each feature has a detailed spec in [specs/](specs/)
- Use acceptance criteria as checklist
- Follow technical implementation guidance
- Update spec status when complete

**4. Maintain Quality**
- Write TypeScript (no `any` types)
- Include unit tests
- Follow file naming conventions
- Keep PRD.md and README.md updated

**5. Privacy First**
- No cloud API calls (except Ollama localhost)
- No telemetry without explicit user opt-in
- All data stays on user's device

---

## 🎯 Current Status (As of 2025-01-26)

### Completed ✅
- [x] Initial web app (v1.0) - Live at https://alexbeals.com/projects/podcasts/
- [x] Custom SQL.js WAL support
- [x] All 22 spec documents created
- [x] PRD.md complete
- [x] Technology decisions made (PWA, Path B)

### In Progress 🚧
- [ ] SPEC-000: Refactoring Foundation (Phase 0)
  - [ ] Choose path (A, B, or C)
  - [ ] Set up TypeScript + Vite
  - [ ] Migrate codebase
  - [ ] Write tests

### Next Up 📋
- [ ] Phase 1: Quick Wins (SPEC-001, 009, 014-016)
- [ ] Phase 2: AI Features (SPEC-002-006)
- [ ] Phase 3: Enhanced UX (SPEC-007-013)
- [ ] Phase 4: Performance (SPEC-017-021)

---

## ⚠️ Important Notes for AI Assistants

### DO:
✅ Read the relevant spec completely before suggesting code
✅ Reference spec IDs in all suggestions (e.g., "Per SPEC-001...")
✅ Respect the dependency graph (no SPEC-002 before SPEC-006)
✅ Maintain privacy-first approach (no cloud APIs)
✅ Write TypeScript with proper types
✅ Include tests with code changes
✅ Update documentation when features complete

### DON'T:
❌ Skip SPEC-000 refactoring (required foundation)
❌ Add external API calls or telemetry
❌ Use `any` types in TypeScript
❌ Implement features not in specs (ask first)
❌ Break privacy promise (no data uploads)
❌ Forget to update specs/README.md status

---

## 🆘 Common Questions

**Q: Where do I start?**
A: SPEC-000 refactoring foundation. Choose Path B (recommended).

**Q: Can I work on AI features first?**
A: No. Must complete SPEC-000, then SPEC-006 (Ollama Core) before SPEC-002-005.

**Q: Should we use Tauri or PWA?**
A: PWA (see docs/TECH-DECISION-tauri-vs-pwa.md). Can upgrade to Tauri later if needed.

**Q: What testing is required?**
A: Unit tests for parsers/utils (>70% coverage), integration tests for services, manual E2E testing.

**Q: Can we add [feature X] not in specs?**
A: Create a spec first (SPEC-022+), discuss with maintainer, then implement.

**Q: How do we handle Apple changing file formats?**
A: Version detection, backwards compatibility, community monitoring. See PRD.md Risk Assessment.

---

## 📞 Project Contacts

- **Repository:** https://github.com/dado3212/apple-podcast-transcripts
- **Issues:** https://github.com/dado3212/apple-podcast-transcripts/issues
- **Live Demo:** https://alexbeals.com/projects/podcasts/

---

**Last Updated:** 2025-01-26
**Version:** 2.0 (Planning Phase)
**Status:** Foundation phase (SPEC-000 in progress)
