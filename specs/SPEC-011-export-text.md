# SPEC-011: Export to Plain Text

**Feature ID:** SPEC-011
**Priority:** Low
**Complexity:** Low
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 1-2 days

---

## Overview

Enable export of transcripts as plain text (.txt) files with minimal formatting for maximum compatibility.

## Acceptance Criteria

- [ ] "Download as Text" button in transcript modal
- [ ] Plain text format with metadata header
- [ ] Speaker attribution preserved (if available)
- [ ] UTF-8 encoding
- [ ] Safe filename generation
- [ ] Works across all browsers

## Output Format

```
================================================================================
PODCAST TRANSCRIPT
================================================================================

Podcast: The Example Podcast
Episode: Episode 42: The Future of AI
Host: John Doe
Date: January 15, 2025
Duration: 1 HR 23 MIN

Description:
This episode explores the cutting edge of artificial intelligence...

================================================================================
TRANSCRIPT
================================================================================

This is the transcript content. Speaker names are preserved when available.

[Speaker Name]: Quote or dialogue here.

Regular content continues...
```

## Technical Implementation

```javascript
function formatAsPlainText(episode) {
  const header = `
================================================================================
PODCAST TRANSCRIPT
================================================================================

Podcast: ${episode.podcastName || 'Unknown'}
Episode: ${episode.title}
Host: ${episode.author || 'Unknown'}
Date: ${formatDate(episode.date)}
Duration: ${episode.duration || 'Unknown'}

Description:
${episode.description || 'No description available'}

================================================================================
TRANSCRIPT
================================================================================

${episode.transcript}
`.trim();

  return header;
}

function downloadAsText(content, filename) {
  const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.download = `${sanitizeFilename(filename)}.txt`;
  link.click();
  URL.revokeObjectURL(url);
}
```

## Related Specs

- SPEC-001: Markdown Export
- SPEC-012: Export to PDF
- SPEC-013: Batch Export

---

**Last Updated:** 2025-01-26
