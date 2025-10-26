# SPEC-017: Performance - Virtual Scrolling

**Feature ID:** SPEC-017
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Implement virtual scrolling for episode lists to handle hundreds of episodes without performance degradation.

## Problem Statement

Rendering 100+ episode cards simultaneously causes:
- High initial render time
- Memory consumption issues
- Laggy scrolling
- Poor mobile performance

## Solution

Use virtual scrolling to render only visible items plus a buffer.

## Technical Implementation

```javascript
class VirtualScroller {
  constructor(container, items, itemHeight, renderItem) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.renderItem = renderItem;

    this.visibleStart = 0;
    this.visibleEnd = 0;

    this.render();
    this.container.addEventListener('scroll', () => this.handleScroll());
  }

  handleScroll() {
    const scrollTop = this.container.scrollTop;
    const viewportHeight = this.container.clientHeight;

    // Calculate visible range with buffer
    this.visibleStart = Math.max(0, Math.floor(scrollTop / this.itemHeight) - 5);
    this.visibleEnd = Math.min(
      this.items.length,
      Math.ceil((scrollTop + viewportHeight) / this.itemHeight) + 5
    );

    this.render();
  }

  render() {
    const visibleItems = this.items.slice(this.visibleStart, this.visibleEnd);

    // Set total height to maintain scrollbar
    this.container.style.height = `${this.items.length * this.itemHeight}px`;

    // Position and render visible items
    this.container.innerHTML = visibleItems
      .map((item, index) => {
        const actualIndex = this.visibleStart + index;
        const top = actualIndex * this.itemHeight;
        return `
          <div style="position: absolute; top: ${top}px; height: ${this.itemHeight}px; width: 100%;">
            ${this.renderItem(item)}
          </div>
        `;
      })
      .join('');
  }
}

// Usage
const scroller = new VirtualScroller(
  document.getElementById('episode-list'),
  episodes,
  200, // itemHeight
  (episode) => `<div class="episode-card">...</div>`
);
```

## Performance Targets

- **Initial render:** <100ms for 1000 episodes
- **Scroll FPS:** 60fps consistently
- **Memory:** <50MB for 1000 episodes

## Related Specs

- SPEC-018: Web Workers
- SPEC-019: Service Worker

---

**Last Updated:** 2025-01-26
