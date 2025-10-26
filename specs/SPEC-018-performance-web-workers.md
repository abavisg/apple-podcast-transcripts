# SPEC-018: Performance - Web Workers

**Feature ID:** SPEC-018
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Offload heavy processing (transcript parsing, search indexing) to Web Workers to keep UI thread responsive.

## Problem Statement

Heavy operations block the main thread:
- Parsing hundreds of TTML files
- Building search indexes
- Processing large transcripts

## Solution

Use Web Workers for CPU-intensive tasks.

## Technical Implementation

### Worker: transcript-parser.worker.js

```javascript
// transcript-parser.worker.js
self.addEventListener('message', (e) => {
  const { type, data } = e.data;

  if (type === 'parse-ttml') {
    const result = parseTTML(data.ttmlContent);
    self.postMessage({ type: 'parse-complete', result });
  }
});

function parseTTML(xmlString) {
  const parser = new DOMParser();
  const doc = parser.parseFromString(xmlString, 'text/xml');

  const sentences = Array.from(doc.querySelectorAll('p')).map(p => ({
    speaker: p.getAttribute('ttm:agent') || null,
    text: p.textContent.trim()
  }));

  return sentences.map(s => s.text).join(' ');
}
```

### Main Thread Usage

```javascript
const worker = new Worker('transcript-parser.worker.js');

worker.addEventListener('message', (e) => {
  if (e.data.type === 'parse-complete') {
    displayTranscript(e.data.result);
  }
});

// Send work to worker
worker.postMessage({
  type: 'parse-ttml',
  data: { ttmlContent: rawXML }
});
```

## Use Cases for Workers

1. **TTML Parsing** - Parse XML in background
2. **Search Indexing** - Build Lunr.js index off main thread
3. **Large File Processing** - Handle DB/WAL files
4. **Batch Operations** - Process multiple episodes

## Performance Targets

- Main thread stays <50ms blocked during parsing
- UI remains responsive (60fps) during heavy operations

## Related Specs

- SPEC-017: Virtual Scrolling
- SPEC-019: Service Worker

---

**Last Updated:** 2025-01-26
