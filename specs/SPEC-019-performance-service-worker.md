# SPEC-019: Performance - Service Worker & Offline

**Feature ID:** SPEC-019
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Implement Service Worker for offline functionality, faster load times, and background sync capabilities.

## Acceptance Criteria

- [ ] App loads offline after first visit
- [ ] Static assets cached (HTML, CSS, JS)
- [ ] SQL.js WASM cached
- [ ] Offline indicator in UI
- [ ] Background sync for Ollama operations (future)

## Technical Implementation

### service-worker.js

```javascript
const CACHE_NAME = 'podcast-transcripts-v1';
const ASSETS = [
  '/',
  '/index.html',
  '/sql-wasm.js',
  '/sql-wasm.wasm',
  '/favicons/favicon-96x96.png',
  // ... other assets
];

// Install: cache assets
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(ASSETS))
  );
});

// Fetch: serve from cache, fallback to network
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});

// Activate: clean old caches
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then(keys => {
      return Promise.all(
        keys.filter(key => key !== CACHE_NAME)
          .map(key => caches.delete(key))
      );
    })
  );
});
```

### Register in index.html

```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js')
    .then(() => console.log('Service Worker registered'))
    .catch(err => console.error('SW registration failed:', err));
}
```

## Related Specs

- SPEC-017: Virtual Scrolling
- SPEC-018: Web Workers

---

**Last Updated:** 2025-01-26
