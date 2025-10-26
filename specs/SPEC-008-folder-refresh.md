# SPEC-008: Folder Refresh Mechanism

**Feature ID:** SPEC-008
**Priority:** Medium
**Complexity:** Low-Medium
**Status:** Planned
**Dependencies:** SPEC-007 (File System Access API)
**Estimated Effort:** 2-3 days

---

## Overview

Add manual and automatic refresh capabilities to re-scan the podcast folder for new transcripts without re-selecting the directory.

## Acceptance Criteria

- [ ] "Refresh" button visible when folder selected
- [ ] Manual refresh rescans folder and displays new episodes
- [ ] Optional auto-refresh with configurable interval (5, 15, 30, 60 min)
- [ ] Visual indicator for new episodes since last check
- [ ] "Last updated" timestamp displayed
- [ ] Notification badge showing count of new episodes

## Technical Implementation

```javascript
async function refreshTranscripts(dirHandle) {
  const newFiles = await scanFolder(dirHandle);
  const currentEpisodeIds = getCurrentEpisodeIds();
  const newEpisodes = filterNewEpisodes(newFiles, currentEpisodeIds);

  if (newEpisodes.length > 0) {
    displayNewBadge(newEpisodes.length);
    updateEpisodeList(newEpisodes);
  }

  updateLastRefreshTimestamp();
}

// Auto-refresh
let autoRefreshInterval;
function enableAutoRefresh(intervalMinutes) {
  clearInterval(autoRefreshInterval);
  autoRefreshInterval = setInterval(() => {
    refreshTranscripts(currentFolderHandle);
  }, intervalMinutes * 60 * 1000);
}
```

## Related Specs

- SPEC-007: File System Access API (dependency)

---

**Last Updated:** 2025-01-26
