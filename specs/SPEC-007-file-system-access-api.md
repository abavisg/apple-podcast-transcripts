# SPEC-007: File System Access API Integration

**Feature ID:** SPEC-007
**Priority:** Medium
**Complexity:** Medium-High
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 4-5 days

---

## Overview

Implement persistent folder access using the File System Access API, allowing users to select the Apple Podcasts folder once and have the app automatically detect new transcripts without repeated drag-and-drop.

## User Story

**As a** regular user of the transcript viewer
**I want to** select the podcast folder once and have it remember my choice
**So that** I don't need to drag-and-drop files every time I visit the app

## Acceptance Criteria

- [ ] "Select Folder" button appears on main interface
- [ ] Folder selection opens native file picker
- [ ] Selected folder handle persisted across sessions (IndexedDB)
- [ ] Auto-load transcripts on app start if folder previously selected
- [ ] Manual "Refresh" button to re-scan folder
- [ ] Optional auto-refresh (configurable interval)
- [ ] Works only in supported browsers (Chrome, Edge, Brave)
- [ ] Graceful fallback UI for unsupported browsers
- [ ] "Change Folder" option to select different directory
- [ ] Permission re-prompt if access revoked

## Technical Requirements

### 1. Feature Detection

```javascript
function supportsFileSystemAccess() {
  return 'showDirectoryPicker' in window;
}

// Show appropriate UI based on support
if (supportsFileSystemAccess()) {
  showSelectFolderButton();
} else {
  showDragDropOnly();
}
```

### 2. Folder Selection

```javascript
async function selectPodcastFolder() {
  try {
    const dirHandle = await window.showDirectoryPicker({
      mode: 'read',
      startIn: 'downloads'  // Hint for native picker
    });

    // Store handle in IndexedDB for persistence
    await storeFolderHandle(dirHandle);

    // Initial scan
    await scanFolder(dirHandle);

    return dirHandle;
  } catch (error) {
    if (error.name === 'AbortError') {
      // User cancelled
      return null;
    }
    throw error;
  }
}
```

### 3. Handle Persistence

```javascript
// Store in IndexedDB (special serializable format)
async function storeFolderHandle(dirHandle) {
  const db = await openDB('AppStorage');
  await db.put('settings', dirHandle, 'podcastFolderHandle');
}

// Retrieve and verify permissions
async function loadFolderHandle() {
  const db = await openDB('AppStorage');
  const handle = await db.get('settings', 'podcastFolderHandle');

  if (!handle) return null;

  // Check if we still have permission
  const permission = await handle.queryPermission({ mode: 'read' });

  if (permission === 'granted') {
    return handle;
  } else if (permission === 'prompt') {
    // Re-request permission
    const newPermission = await handle.requestPermission({ mode: 'read' });
    return newPermission === 'granted' ? handle : null;
  }

  return null;
}
```

### 4. Folder Scanning

```javascript
async function scanFolder(dirHandle) {
  const files = {
    ttml: [],
    sqliteMain: null,
    sqliteWal: null
  };

  // Recursively traverse directory
  async function traverse(handle, path = '') {
    for await (const entry of handle.values()) {
      const fullPath = path ? `${path}/${entry.name}` : entry.name;

      if (entry.kind === 'file') {
        if (entry.name.endsWith('.ttml')) {
          const file = await entry.getFile();
          files.ttml.push({ file, path: fullPath });
        } else if (entry.name === 'MTLibrary.sqlite') {
          files.sqliteMain = await entry.getFile();
        } else if (entry.name === 'MTLibrary.sqlite-wal') {
          files.sqliteWal = await entry.getFile();
        }
      } else if (entry.kind === 'directory') {
        await traverse(entry, fullPath);
      }
    }
  }

  await traverse(dirHandle);
  return files;
}
```

## Implementation Tasks

- [ ] Implement feature detection
- [ ] Build folder selection UI
- [ ] Implement `selectPodcastFolder()` function
- [ ] Build IndexedDB handle persistence
- [ ] Implement `loadFolderHandle()` with permission checks
- [ ] Build folder scanning logic
- [ ] Add refresh mechanism (SPEC-008)
- [ ] Handle permission errors gracefully
- [ ] Add "Change Folder" functionality
- [ ] Test across Chrome, Edge, Brave
- [ ] Document unsupported browsers (Firefox, Safari)

## UI Design

```
┌──────────────────────────────────────┐
│  Apple Podcast Transcript Viewer    │
├──────────────────────────────────────┤
│                                      │
│  Choose how to load transcripts:    │
│                                      │
│  ┌─────────────────┐                │
│  │  Select Folder  │  Recommended   │
│  │  📁             │                │
│  │  One-time setup │                │
│  │  Auto-refresh   │                │
│  │                 │                │
│  │  [Select...]    │                │
│  └─────────────────┘                │
│                                      │
│  Or drag & drop folder here ↓       │
│  ┌─────────────────────────────┐    │
│  │         Drop Zone           │    │
│  └─────────────────────────────┘    │
└──────────────────────────────────────┘
```

## Browser Compatibility

| Browser | Support | Version |
|---------|---------|---------|
| Chrome  | ✅ Yes  | 86+     |
| Edge    | ✅ Yes  | 86+     |
| Brave   | ✅ Yes  | 1.30+   |
| Firefox | ❌ No   | -       |
| Safari  | ❌ No   | -       |

## Related Specs

- SPEC-008: Folder Refresh Mechanism
- SPEC-009: Search & Filter

## References

- [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API)
- [Browser Compatibility](https://caniuse.com/native-filesystem-api)

---

**Last Updated:** 2025-01-26
