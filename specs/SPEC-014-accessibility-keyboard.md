# SPEC-014: Accessibility - Keyboard Navigation

**Feature ID:** SPEC-014
**Priority:** Medium
**Complexity:** Low
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 2-3 days

---

## Overview

Ensure complete keyboard navigation support for all interactive elements, allowing users to navigate and operate the app without a mouse.

## Acceptance Criteria

- [ ] All interactive elements keyboard accessible (Tab, Enter, Space)
- [ ] Logical tab order throughout app
- [ ] Focus indicators visible on all elements
- [ ] Keyboard shortcuts for common actions
- [ ] Skip navigation links
- [ ] Escape key closes modals
- [ ] Arrow keys navigate lists
- [ ] No keyboard traps

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Tab` | Next element |
| `Shift+Tab` | Previous element |
| `Enter` / `Space` | Activate button/link |
| `Escape` | Close modal |
| `Cmd/Ctrl+F` | Focus search |
| `↑` `↓` | Navigate episode list |
| `Cmd/Ctrl+K` | Quick command palette (future) |

## Technical Implementation

```javascript
// Focus management for modals
function openModal(modalId) {
  const modal = document.getElementById(modalId);
  modal.setAttribute('aria-hidden', 'false');

  // Save previous focus
  previousFocus = document.activeElement;

  // Focus first element in modal
  const firstFocusable = modal.querySelector('button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])');
  firstFocusable?.focus();

  // Trap focus within modal
  modal.addEventListener('keydown', trapFocus);
}

function trapFocus(e) {
  if (e.key !== 'Tab') return;

  const focusableElements = modal.querySelectorAll('button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])');
  const firstElement = focusableElements[0];
  const lastElement = focusableElements[focusableElements.length - 1];

  if (e.shiftKey && document.activeElement === firstElement) {
    lastElement.focus();
    e.preventDefault();
  } else if (!e.shiftKey && document.activeElement === lastElement) {
    firstElement.focus();
    e.preventDefault();
  }
}
```

## Related Specs

- SPEC-015: Screen Reader Support
- SPEC-016: Visual Accessibility

---

**Last Updated:** 2025-01-26
