# SPEC-015: Accessibility - Screen Reader Support

**Feature ID:** SPEC-015
**Priority:** Medium
**Complexity:** Low-Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 2-3 days

---

## Overview

Ensure full screen reader compatibility with proper ARIA labels, roles, live regions, and semantic HTML.

## Acceptance Criteria

- [ ] All images have alt text
- [ ] All buttons have accessible names
- [ ] Proper ARIA roles on custom components
- [ ] ARIA live regions for dynamic content
- [ ] Semantic HTML (headings, landmarks, lists)
- [ ] Form labels properly associated
- [ ] Status messages announced
- [ ] Loading states announced

## ARIA Implementation

```html
<!-- Episode Card -->
<article
  role="article"
  aria-labelledby="episode-title-123"
  aria-describedby="episode-desc-123"
>
  <h3 id="episode-title-123">Episode Title</h3>
  <p id="episode-desc-123">Episode description...</p>

  <button
    aria-label="View transcript for Episode Title"
    aria-haspopup="dialog"
  >
    View Transcript
  </button>
</article>

<!-- Modal -->
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-desc"
>
  <h2 id="modal-title">Episode Title</h2>
  <div id="modal-desc">Full transcript content...</div>
  <button aria-label="Close dialog">×</button>
</div>

<!-- Live Region for Status Updates -->
<div
  role="status"
  aria-live="polite"
  aria-atomic="true"
  class="sr-only"
>
  <!-- Dynamically updated messages -->
</div>

<!-- Loading State -->
<div
  role="status"
  aria-live="assertive"
  aria-busy="true"
>
  Loading transcript...
</div>
```

## Screen Reader Only Text

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

## Related Specs

- SPEC-014: Keyboard Navigation
- SPEC-016: Visual Accessibility

---

**Last Updated:** 2025-01-26
