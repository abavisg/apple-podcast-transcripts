# SPEC-009: Search & Filter Functionality

**Feature ID:** SPEC-009
**Priority:** Medium
**Complexity:** Low-Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Enable full-text search across transcripts and filtering by podcast name, author, and date range to help users find specific episodes or content quickly.

## Acceptance Criteria

- [ ] Search bar in header/toolbar
- [ ] Full-text search across all transcript content
- [ ] Filter by podcast name (dropdown)
- [ ] Filter by author (dropdown)
- [ ] Filter by date range (date picker)
- [ ] Search result highlighting in transcripts
- [ ] Results sorted by relevance
- [ ] "Clear filters" button
- [ ] Search history (recent searches)
- [ ] Keyboard shortcut (Cmd/Ctrl+F) to focus search

## Technical Implementation

### Search Index

```javascript
// Use Lunr.js or native String.prototype.includes
class TranscriptSearch {
  constructor(episodes) {
    this.episodes = episodes;
    this.buildIndex();
  }

  buildIndex() {
    // Option 1: Simple (no library)
    this.searchableText = this.episodes.map(ep => ({
      id: ep.id,
      text: `${ep.title} ${ep.author} ${ep.transcript}`.toLowerCase()
    }));

    // Option 2: Advanced (with Lunr.js)
    this.index = lunr(function() {
      this.ref('id');
      this.field('title', { boost: 10 });
      this.field('author', { boost: 5 });
      this.field('transcript');

      episodes.forEach(ep => this.add(ep));
    });
  }

  search(query) {
    const lowerQuery = query.toLowerCase();

    return this.searchableText
      .filter(item => item.text.includes(lowerQuery))
      .map(item => this.episodes.find(ep => ep.id === item.id));
  }
}
```

### Filters

```javascript
function applyFilters(episodes, filters) {
  let filtered = episodes;

  if (filters.podcast) {
    filtered = filtered.filter(ep => ep.podcastName === filters.podcast);
  }

  if (filters.author) {
    filtered = filtered.filter(ep => ep.author === filters.author);
  }

  if (filters.dateRange) {
    filtered = filtered.filter(ep => {
      return ep.date >= filters.dateRange.start &&
             ep.date <= filters.dateRange.end;
    });
  }

  return filtered;
}
```

## UI Design

```
┌──────────────────────────────────────────────┐
│  [🔍 Search transcripts...]    [Filters ▼]  │
├──────────────────────────────────────────────┤
│  Active Filters: Podcast: "Lex Fridman" ✕   │
│                  Date: Last 30 days ✕        │
│                  [Clear All]                 │
├──────────────────────────────────────────────┤
│  Found 12 episodes                           │
│  ...                                         │
└──────────────────────────────────────────────┘
```

## Related Specs

- SPEC-010: Bookmark & Favorites

---

**Last Updated:** 2025-01-26
