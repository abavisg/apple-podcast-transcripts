# SPEC-000: Refactoring & Architecture Foundation

**Feature ID:** SPEC-000
**Priority:** Critical (Foundation for all other specs)
**Complexity:** High
**Status:** Planned
**Dependencies:** None (but blocks all other specs)
**Estimated Effort:** 1-3 weeks (varies by path)

---

## Overview

Refactor the current monolithic 562-line `index.html` file into a maintainable, modular architecture that supports the 21 planned features and future desktop app development. This spec presents **three refactoring paths** with different complexity and time investment levels.

## Current State Analysis

### Existing Architecture (v1.0)

**File:** `index.html` (562 lines)
- **Lines 1-280:** Inline CSS styling
- **Lines 281-562:** Inline JavaScript (vanilla JS)

**Key Components (all in one file):**
1. **UI Rendering** - Episode cards, modal popup, drag-and-drop zone
2. **File Processing** - Recursive directory traversal, TTML XML parsing
3. **Database** - SQL.js integration, SQLite + WAL file handling
4. **Utilities** - Date formatting, time formatting, speaker extraction
5. **Event Handling** - Drag/drop, modal open/close, episode clicks

**Dependencies:**
- SQL.js (custom WAL-enabled build): `sql-wasm.js` + `sql-wasm.wasm`
- No build system, no package management
- No TypeScript, no testing framework
- Privacy-first: all client-side, no external APIs

### Technical Debt

✅ **What Works Well:**
- Simple deployment (single HTML file)
- No build complexity
- Fast initial load
- Privacy-preserving (100% client-side)
- Custom SQL.js WAL support

⚠️ **Pain Points:**
- Difficult to maintain (all code in one file)
- No code reusability
- Hard to test (no modularization)
- No type safety
- Inline CSS makes styling changes difficult
- Adding features (like Ollama integration) will make file unmanageable
- No state management (all in DOM)

---

## Three Refactoring Paths

Choose based on your goals, timeline, and comfort level:

| Path | Complexity | Effort | Best For |
|------|------------|--------|----------|
| **Path A: Minimal** | Low | 1 week | Quick cleanup, preserve simplicity |
| **Path B: Modern Web** | Medium | 2 weeks | Serious app development, prepare for features |
| **Path C: Desktop-Ready** | High | 3 weeks | Full rewrite, framework-based, Tauri/PWA prep |

---

## Path A: Minimal Refactoring

**Philosophy:** Keep it simple, split files, ES6 modules, stay vanilla.

### Proposed Structure

```
apple-podcast-transcripts/
├── index.html (minimal, loads modules)
├── styles/
│   ├── main.css
│   ├── podcast-card.css
│   └── modal.css
├── js/
│   ├── main.js (entry point)
│   ├── ui/
│   │   ├── episodeCard.js
│   │   ├── modal.js
│   │   └── dragDrop.js
│   ├── parsers/
│   │   ├── ttmlParser.js
│   │   └── sqliteReader.js
│   ├── utils/
│   │   ├── formatters.js
│   │   └── fileTraversal.js
│   └── state.js (simple state object)
├── sql-wasm.js
├── sql-wasm.wasm
└── favicons/
```

### Changes

**index.html** (becomes ~50 lines):
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Apple Podcast Transcript Viewer</title>
  <link rel="stylesheet" href="styles/main.css">
  <link rel="stylesheet" href="styles/podcast-card.css">
  <link rel="stylesheet" href="styles/modal.css">
</head>
<body>
  <div id="app"></div>
  <script type="module" src="js/main.js"></script>
</body>
</html>
```

**js/main.js:**
```javascript
import { initDragDrop } from './ui/dragDrop.js';
import { renderEpisodeCard } from './ui/episodeCard.js';
import { openModal, closeModal } from './ui/modal.js';
import { parseTTML } from './parsers/ttmlParser.js';
import { readSQLite } from './parsers/sqliteReader.js';
import { formatTime, formatDate } from './utils/formatters.js';
import { traverseFileTree } from './utils/fileTraversal.js';

// Initialize app
document.addEventListener('DOMContentLoaded', () => {
  initDragDrop();
});
```

**js/state.js** (simple global state):
```javascript
export const state = {
  episodes: [],
  currentTranscript: null,
  db: null
};

export function addEpisode(episode) {
  state.episodes.push(episode);
}

export function setCurrentTranscript(transcript) {
  state.currentTranscript = transcript;
}
```

### Migration Steps

1. **Split CSS** (Day 1)
   - Extract inline styles to `styles/main.css`
   - Extract component-specific styles to separate files
   - Test: Verify visual appearance unchanged

2. **Extract Utilities** (Day 1-2)
   - Create `formatters.js` for `formatTime()`, `formatDate()`
   - Create `fileTraversal.js` for `traverseFileTree()`
   - Add ES6 exports

3. **Modularize Parsers** (Day 2-3)
   - Extract `extractPodcastTranscripts()` → `ttmlParser.js`
   - Extract SQL.js logic → `sqliteReader.js`
   - Test: Verify parsing works

4. **Componentize UI** (Day 3-4)
   - Extract episode card rendering → `episodeCard.js`
   - Extract modal logic → `modal.js`
   - Extract drag/drop → `dragDrop.js`
   - Test: Verify UI interactions

5. **Create Entry Point** (Day 4-5)
   - Build `main.js` that imports and orchestrates
   - Create simple `state.js`
   - Update `index.html` to load `main.js`
   - Test: Full integration

### Pros & Cons

**Pros:**
✅ Quick (1 week)
✅ Low risk (minimal changes)
✅ Still simple (no build tools)
✅ No learning curve
✅ Easy to revert

**Cons:**
❌ Still vanilla JS (no type safety)
❌ No framework benefits
❌ Manual state management
❌ No testing framework
❌ Won't scale well to 21 specs

**Recommendation:** Choose Path A if you want quick wins and plan to keep features minimal.

---

## Path B: Modern Web Architecture

**Philosophy:** Professional web app with TypeScript, build tools, components.

### Proposed Structure

```
apple-podcast-transcripts/
├── src/
│   ├── main.ts (entry point)
│   ├── app.ts (app initialization)
│   ├── components/
│   │   ├── EpisodeCard.ts
│   │   ├── Modal.ts
│   │   ├── DragDropZone.ts
│   │   └── LoadingSpinner.ts
│   ├── services/
│   │   ├── TTMLParser.ts
│   │   ├── SQLiteService.ts
│   │   ├── FileSystemService.ts
│   │   └── OllamaClient.ts (future)
│   ├── state/
│   │   ├── AppState.ts
│   │   ├── EpisodeStore.ts
│   │   └── UIStore.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── constants.ts
│   ├── types/
│   │   ├── Episode.ts
│   │   ├── Transcript.ts
│   │   └── index.ts
│   └── styles/
│       ├── main.css
│       ├── components.css
│       └── variables.css
├── public/
│   ├── index.html
│   ├── sql-wasm.js
│   ├── sql-wasm.wasm
│   └── favicons/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── setup.ts
├── package.json
├── tsconfig.json
├── vite.config.ts
└── README.md
```

### Technology Stack

**Build Tool:** Vite
- Fast HMR (Hot Module Replacement)
- TypeScript support out of box
- Optimized production builds
- Dev server with HTTPS

**Language:** TypeScript
- Type safety for Episode, Transcript, etc.
- Better IDE support
- Catch errors at compile time

**State Management:** Simple reactive store (custom or Zustand)
```typescript
// src/state/AppState.ts
import { create } from 'zustand';

interface AppState {
  episodes: Episode[];
  currentTranscript: Transcript | null;
  isLoading: boolean;
  addEpisode: (episode: Episode) => void;
  setCurrentTranscript: (transcript: Transcript | null) => void;
}

export const useAppStore = create<AppState>((set) => ({
  episodes: [],
  currentTranscript: null,
  isLoading: false,
  addEpisode: (episode) => set((state) => ({
    episodes: [...state.episodes, episode]
  })),
  setCurrentTranscript: (transcript) => set({ currentTranscript: transcript }),
}));
```

**Testing:** Vitest
- Fast, Vite-native testing
- Jest-compatible API
- Component testing

### Key Files

**src/types/Episode.ts:**
```typescript
export interface Episode {
  id: string;
  title: string;
  podcastName: string;
  author: string | null;
  date: number; // Unix timestamp
  duration: number; // seconds
  description: string | null;
  transcript: TranscriptChunk[];
  lastModified: number;
}

export interface TranscriptChunk {
  speaker: string | null;
  sentences: string;
}
```

**src/services/TTMLParser.ts:**
```typescript
import { TranscriptChunk } from '../types';

export class TTMLParser {
  static async parse(file: File): Promise<{
    id: string;
    chunks: TranscriptChunk[];
    lastModified: number;
  }> {
    const text = await file.text();
    const parser = new DOMParser();
    const xmlDoc = parser.parseFromString(text, 'application/xml');

    const match = file.name.match(/(\d+)/);
    const id = match ? match[1] : null;

    if (!id) throw new Error('Invalid TTML filename');

    const chunks: TranscriptChunk[] = [];
    const speakingChunks = xmlDoc.querySelectorAll('p');

    for (const chunk of speakingChunks) {
      const speaker = chunk.getAttribute('ttm:agent');
      const sentences = [...chunk.querySelectorAll('span[podcasts:unit="sentence"]')]
        .map(span => [...span.querySelectorAll('span')].map(s => s.textContent).join(' '))
        .join(' ');

      chunks.push({ speaker, sentences });
    }

    return { id, chunks, lastModified: file.lastModified };
  }
}
```

**src/components/EpisodeCard.ts:**
```typescript
import { Episode } from '../types';
import { formatTime, formatDate } from '../utils/formatters';

export class EpisodeCard {
  constructor(private episode: Episode, private onClick: () => void) {}

  render(): HTMLElement {
    const card = document.createElement('div');
    card.className = 'podcast';
    card.onclick = this.onClick;

    card.innerHTML = `
      <div class="title">${this.episode.title}</div>
      <div class="author">${this.episode.author || 'Unknown'}</div>
      <div class="info">
        <span class="date">${formatDate(this.episode.date)}</span>
        <span class="duration">${formatTime(this.episode.duration)}</span>
      </div>
      ${this.episode.description ? `<div class="description">${this.episode.description}</div>` : ''}
    `;

    return card;
  }
}
```

### Migration Steps

1. **Setup Build Environment** (Day 1)
   ```bash
   npm init -y
   npm install -D vite typescript vitest
   npm install zustand
   ```
   - Create `vite.config.ts`
   - Create `tsconfig.json`
   - Create folder structure

2. **Convert to TypeScript** (Day 2-4)
   - Define types (`Episode`, `Transcript`, etc.)
   - Convert parsers to TS
   - Convert utilities to TS
   - Fix type errors

3. **Build Component System** (Day 5-7)
   - Create `EpisodeCard` component
   - Create `Modal` component
   - Create `DragDropZone` component
   - Add event system

4. **Implement State Management** (Day 8-9)
   - Set up Zustand store
   - Migrate global state
   - Connect components to store

5. **Add Testing** (Day 10-11)
   - Write unit tests for parsers
   - Write tests for formatters
   - Write component tests

6. **Build & Deploy** (Day 12-14)
   - Configure production build
   - Test deployment
   - Migration complete

### Pros & Cons

**Pros:**
✅ Type safety (catch bugs early)
✅ Modern tooling (Vite, TypeScript)
✅ Testable architecture
✅ Scales well to all 21 specs
✅ Component reusability
✅ Better IDE support
✅ Easier collaboration

**Cons:**
❌ 2 weeks investment
❌ Build step required
❌ Learning curve (TypeScript if new)
❌ More complex deployment

**Recommendation:** Choose Path B if you're serious about implementing most of the 21 specs and want a professional foundation.

---

## Path C: Desktop-Ready Full Rewrite

**Philosophy:** Framework-based, production-ready, optimized for desktop (Tauri/PWA).

### Proposed Structure

```
apple-podcast-transcripts/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── components/
│   │   ├── episodes/
│   │   │   ├── EpisodeCard.tsx
│   │   │   ├── EpisodeList.tsx
│   │   │   └── EpisodeDetail.tsx
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Modal.tsx
│   │   ├── file-import/
│   │   │   ├── DragDropZone.tsx
│   │   │   └── FolderSelector.tsx
│   │   └── shared/
│   │       ├── Button.tsx
│   │       ├── LoadingSpinner.tsx
│   │       └── ErrorBoundary.tsx
│   ├── features/
│   │   ├── episodes/
│   │   │   ├── episodesSlice.ts (Redux/Zustand)
│   │   │   ├── useEpisodes.tsx (hook)
│   │   │   └── episodeService.ts
│   │   ├── ai/
│   │   │   ├── ollamaService.ts
│   │   │   ├── summaryCache.ts
│   │   │   └── useAISummary.tsx
│   │   └── file-system/
│   │       ├── fileSystemService.ts
│   │       └── useFileSystem.tsx
│   ├── lib/
│   │   ├── parsers/
│   │   ├── db/
│   │   └── cache/
│   ├── types/
│   ├── hooks/
│   ├── utils/
│   └── styles/
│       └── tailwind.css
├── src-tauri/ (if Tauri chosen)
│   ├── src/
│   │   └── main.rs
│   └── Cargo.toml
├── public/
├── tests/
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.js
└── postcss.config.js
```

### Technology Stack

**Framework:** React (or Svelte - lighter alternative)
- Component-based architecture
- Rich ecosystem
- Great TypeScript support
- Easy to find developers

**Styling:** Tailwind CSS
- Utility-first
- Fast development
- Responsive by default
- Small production bundle

**State:** Zustand or Redux Toolkit
- Global state management
- Middleware support (persistence, devtools)

**Build:** Vite + TypeScript

**Testing:** Vitest + React Testing Library

**Desktop (if Tauri):**
- Rust backend for native features
- IPC communication with frontend
- Native file system access

### Example Components

**src/components/episodes/EpisodeCard.tsx:**
```typescript
import { FC } from 'react';
import { Episode } from '@/types';
import { formatTime, formatDate } from '@/utils/formatters';

interface EpisodeCardProps {
  episode: Episode;
  onClick: () => void;
}

export const EpisodeCard: FC<EpisodeCardProps> = ({ episode, onClick }) => {
  return (
    <div
      className="bg-white rounded-lg p-5 shadow-lg hover:scale-105 transition-transform cursor-pointer"
      onClick={onClick}
    >
      <h3 className="text-xl font-bold mb-2">{episode.title}</h3>
      <p className="text-gray-600">{episode.author || 'Unknown'}</p>
      <div className="flex gap-4 mt-3 text-sm text-gray-500">
        <span>{formatDate(episode.date)}</span>
        <span>{formatTime(episode.duration)}</span>
      </div>
      {episode.description && (
        <p className="mt-3 text-gray-700 line-clamp-2">{episode.description}</p>
      )}
    </div>
  );
};
```

**src/features/ai/useAISummary.tsx:**
```typescript
import { useState, useEffect } from 'react';
import { Episode } from '@/types';
import { ollamaService } from './ollamaService';
import { summaryCache } from './summaryCache';

export function useAISummary(episode: Episode) {
  const [summary, setSummary] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  const generateSummary = async () => {
    // Check cache first
    const cached = await summaryCache.get(episode.id);
    if (cached) {
      setSummary(cached);
      return;
    }

    setLoading(true);
    setError(null);

    try {
      const result = await ollamaService.generateQuickSummary(
        episode.transcript.map(t => t.sentences).join(' ')
      );
      setSummary(result);
      await summaryCache.set(episode.id, result);
    } catch (err) {
      setError(err as Error);
    } finally {
      setLoading(false);
    }
  };

  return { summary, loading, error, generateSummary };
}
```

### Migration Steps

1. **Setup Project** (Day 1-2)
   - Initialize React + Vite + TypeScript
   - Configure Tailwind CSS
   - Set up project structure
   - Configure linting/formatting

2. **Build Core UI** (Day 3-5)
   - Create layout components
   - Build episode list/card
   - Implement modal
   - Style with Tailwind

3. **Migrate Business Logic** (Day 6-9)
   - Port parsers to TypeScript
   - Implement services
   - Add state management
   - Connect UI to data

4. **Add Advanced Features** (Day 10-14)
   - File System Access API
   - IndexedDB caching
   - Search functionality
   - Error handling

5. **Testing & Polish** (Day 15-18)
   - Write tests
   - Performance optimization
   - Accessibility audit
   - Documentation

6. **Desktop Integration** (Day 19-21, if Tauri)
   - Set up Tauri
   - Implement native features
   - Build installers
   - Test on all platforms

### Pros & Cons

**Pros:**
✅ Production-ready architecture
✅ Framework benefits (React ecosystem)
✅ Scalable to any complexity
✅ Best developer experience
✅ Easy to add all 21 specs
✅ Desktop-ready (Tauri or PWA)
✅ Team-friendly

**Cons:**
❌ 3 weeks investment
❌ Biggest learning curve
❌ Most complex setup
❌ Framework lock-in
❌ Larger bundle size

**Recommendation:** Choose Path C if you want a professional desktop app and plan to implement most/all 21 specs.

---

## Comparison Matrix

| Criterion | Path A | Path B | Path C |
|-----------|--------|--------|--------|
| **Effort** | 1 week | 2 weeks | 3 weeks |
| **Complexity** | Low | Medium | High |
| **Type Safety** | ❌ No | ✅ Yes | ✅ Yes |
| **Testing** | Manual | Unit + Integration | Full coverage |
| **Scalability** | ⚠️ Limited | ✅ Good | ✅ Excellent |
| **Desktop Ready** | ❌ No | ⚠️ PWA only | ✅ Tauri or PWA |
| **Learning Curve** | None | TypeScript | Framework + TypeScript |
| **Bundle Size** | Smallest | Medium | Largest |
| **Maintainability** | ⚠️ Medium | ✅ Good | ✅ Excellent |
| **Deployment** | Simple | Build step | Build + bundle |
| **Team Friendly** | ❌ No | ✅ Yes | ✅ Yes |
| **Future-Proof** | ⚠️ Limited | ✅ Good | ✅ Excellent |

---

## Recommendation

**For this project, I recommend Path B (Modern Web Architecture)**

**Rationale:**
1. **21 specs planned** - Need solid foundation
2. **Ollama integration** - Complex features ahead
3. **File System API** - TypeScript will help
4. **Testing critical** - Parsers need tests
5. **Balance** - Not too simple, not overkill
6. **PWA path** - Path B prepares for PWA better than Tauri

**Migration Path:**
- Start with **Path B** now
- Evaluate **Path C** after Phase 2 (if Tauri needed)
- Path A is too limited for your roadmap

---

## Implementation Checklist

### Pre-Migration
- [ ] Backup current `index.html`
- [ ] Create new branch: `git checkout -b refactor/path-b`
- [ ] Set up version control for rollback

### Path B Migration (Recommended)
- [ ] Day 1: Initialize Vite + TypeScript project
- [ ] Day 2: Define TypeScript types/interfaces
- [ ] Day 3: Convert parsers to TypeScript
- [ ] Day 4: Convert utilities to TypeScript
- [ ] Day 5: Build component system
- [ ] Day 6: Extract UI components
- [ ] Day 7: Implement drag/drop component
- [ ] Day 8: Set up Zustand state management
- [ ] Day 9: Connect components to store
- [ ] Day 10: Add Vitest testing
- [ ] Day 11: Write parser tests
- [ ] Day 12: Write component tests
- [ ] Day 13: Production build configuration
- [ ] Day 14: Deployment testing
- [ ] Final: Merge to main, deploy

### Post-Migration
- [ ] Update README with new dev setup
- [ ] Document build commands
- [ ] Update deployment process
- [ ] Archive old version
- [ ] Announce refactoring complete

---

## Breaking Changes

### For Users
- ✅ No breaking changes (UI/UX identical)
- ✅ Data format unchanged
- ✅ Same browser compatibility

### For Developers
- ⚠️ Build step required (Path B & C)
- ⚠️ New folder structure
- ⚠️ TypeScript knowledge needed (Path B & C)
- ⚠️ Node.js/npm required (Path B & C)

---

## Testing Strategy

### Path A Testing
- Manual testing only
- Browser console verification
- Visual regression testing

### Path B Testing
```typescript
// tests/unit/TTMLParser.test.ts
import { describe, it, expect } from 'vitest';
import { TTMLParser } from '@/services/TTMLParser';

describe('TTMLParser', () => {
  it('should parse valid TTML file', async () => {
    const mockFile = new File(['<xml>...</xml>'], '12345.ttml');
    const result = await TTMLParser.parse(mockFile);

    expect(result.id).toBe('12345');
    expect(result.chunks).toHaveLength(10);
  });

  it('should throw on invalid filename', async () => {
    const mockFile = new File(['<xml>...</xml>'], 'invalid.ttml');
    await expect(TTMLParser.parse(mockFile)).rejects.toThrow();
  });
});
```

### Path C Testing
- All Path B tests plus:
- Component tests with React Testing Library
- E2E tests with Playwright
- Accessibility tests

---

## Success Metrics

### Path A Success
- ✅ Files separated
- ✅ ES6 modules working
- ✅ Code maintainable

### Path B Success
- ✅ TypeScript compiles with 0 errors
- ✅ Test coverage >70%
- ✅ Vite build succeeds
- ✅ All features working

### Path C Success
- ✅ All Path B metrics
- ✅ Component library established
- ✅ Desktop app builds (Tauri or PWA)
- ✅ Cross-platform tested

---

## Next Steps After Refactoring

Once refactoring complete (any path):

1. **Update IMPROVEMENTS.md** - Mark SPEC-000 complete
2. **Begin Phase 1** - Start with SPEC-001 (Markdown Export)
3. **Establish workflow** - Use new architecture for all features
4. **Document patterns** - Create contribution guide

---

## Related Specs

**Blocked by SPEC-000 (must refactor first):**
- All 21 feature specs depend on having a proper architecture

**Related Documents:**
- [PRD.md](../PRD.md) - Product requirements
- [TECH-DECISION-tauri-vs-pwa.md](../docs/TECH-DECISION-tauri-vs-pwa.md) - Desktop tech choice

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Recommended Path:** B (Modern Web Architecture)
