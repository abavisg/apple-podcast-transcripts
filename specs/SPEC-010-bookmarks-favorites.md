# SPEC-010: Bookmarks & Favorites

**Feature ID:** SPEC-010
**Priority:** Low-Medium
**Complexity:** Low
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 2-3 days

---

## Overview

Allow users to bookmark/favorite specific episodes for quick access, with a dedicated view to see all favorited content.

## Acceptance Criteria

- [ ] Star/heart icon on each episode card
- [ ] Click to toggle favorite status
- [ ] "Favorites" filter/view in main list
- [ ] Favorites persisted in localStorage
- [ ] Visual indicator (gold star, filled heart)
- [ ] "Remove from favorites" option
- [ ] Favorites count displayed
- [ ] Export favorites list

## Technical Implementation

```javascript
class FavoritesManager {
  constructor() {
    this.favorites = this.load();
  }

  load() {
    const stored = localStorage.getItem('favorites');
    return stored ? JSON.parse(stored) : [];
  }

  save() {
    localStorage.setItem('favorites', JSON.stringify(this.favorites));
  }

  toggle(episodeId) {
    const index = this.favorites.indexOf(episodeId);
    if (index > -1) {
      this.favorites.splice(index, 1);
    } else {
      this.favorites.push(episodeId);
    }
    this.save();
  }

  isFavorite(episodeId) {
    return this.favorites.includes(episodeId);
  }

  getAll() {
    return this.favorites;
  }
}
```

## Related Specs

- SPEC-009: Search & Filter

---

**Last Updated:** 2025-01-26
