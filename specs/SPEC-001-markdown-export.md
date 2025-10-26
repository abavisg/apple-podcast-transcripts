# SPEC-001: Markdown Export

**Feature ID:** SPEC-001
**Priority:** High
**Complexity:** Low
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 2-3 days

---

## Overview

Enable users to download podcast transcripts as well-formatted markdown files with complete episode metadata, making transcripts portable and easy to use in other tools.

## User Story

**As a** podcast listener who takes notes
**I want to** export transcripts as markdown files
**So that** I can import them into my note-taking system (Obsidian, Notion, etc.) with proper metadata and formatting

## Problem Statement

Currently, users can only view and copy transcripts within the browser interface. There's no way to save them as files for offline use, archiving, or importing into other tools. Markdown is a widely-supported format that preserves structure and metadata while remaining readable.

## Acceptance Criteria

- [ ] A "Download as Markdown" button appears on each transcript modal
- [ ] Downloaded files include YAML frontmatter with complete metadata
- [ ] Transcript content is cleanly formatted in markdown
- [ ] Speaker attribution is preserved (if available in source)
- [ ] Filenames are safe, descriptive, and follow the pattern: `[PodcastName] - [EpisodeTitle] - [Date].md`
- [ ] Special characters in filenames are sanitized properly
- [ ] Download works across all modern browsers (Chrome, Firefox, Safari, Edge)
- [ ] File encoding is UTF-8 to support international characters

## Functional Requirements

### 1. Markdown File Structure

```markdown
---
podcast: The Example Podcast
author: John Doe
episode: Episode 42: The Future of AI
date: January 15, 2025
duration: 1 HR 23 MIN
description: This episode explores the cutting edge...
source: Apple Podcasts
exported: 2025-01-26T10:30:00Z
---

# Episode 42: The Future of AI

**Podcast:** The Example Podcast
**Host:** John Doe
**Date:** January 15, 2025
**Duration:** 1 HR 23 MIN

## Description

This episode explores the cutting edge of artificial intelligence...

## Transcript

[Speaker attribution if available]
[Full transcript content with proper paragraph breaks]
```

### 2. UI Components

**Location:** Transcript modal (popup that displays when clicking an episode)

**Button Design:**
- Icon: Download icon (⬇) or document icon
- Text: "Download as Markdown" or "Export to .md"
- Position: Top-right of modal, near close button
- Style: Secondary button (not primary CTA)

### 3. Filename Generation

**Pattern:** `[Podcast] - [Episode] - [Date].md`

**Example:** `Lex Fridman Podcast - E42 The Future of AI - 2025-01-15.md`

**Sanitization Rules:**
- Replace `/` with `-`
- Replace `:` with `` (remove)
- Replace `?` `*` `<` `>` `|` with `` (remove)
- Limit filename length to 200 characters
- Trim whitespace
- Remove consecutive spaces

## Technical Requirements

### 1. Markdown Formatter Function

```javascript
/**
 * Formats episode data as a markdown document
 * @param {Object} episode - Episode metadata and transcript
 * @returns {string} Formatted markdown content
 */
function formatAsMarkdown(episode) {
  const frontmatter = generateFrontmatter(episode);
  const header = generateHeader(episode);
  const description = episode.description ? `## Description\n\n${episode.description}\n\n` : '';
  const transcript = formatTranscript(episode.transcript);

  return `${frontmatter}\n\n${header}\n\n${description}## Transcript\n\n${transcript}`;
}
```

### 2. File Download Implementation

**Use Blob API:**
```javascript
function downloadMarkdown(content, filename) {
  const blob = new Blob([content], { type: 'text/markdown;charset=utf-8' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.download = filename;
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
  URL.revokeObjectURL(url);
}
```

### 3. Date Formatting

- Use ISO 8601 format for `exported` timestamp
- Use human-readable format for display dates (e.g., "January 15, 2025")
- Include timezone information if available

### 4. Speaker Attribution Preservation

If TTML data includes speaker information:
- Format as `**[Speaker Name]:** Text content`
- Or use blockquote format: `> Speaker Name: Text content`
- Default to no attribution if not available

## Implementation Tasks

- [ ] Create `formatAsMarkdown(episode)` function
  - [ ] Implement YAML frontmatter generator
  - [ ] Implement header section generator
  - [ ] Implement transcript formatter with speaker attribution
  - [ ] Add proper line breaks and paragraph spacing
- [ ] Create `sanitizeFilename(name)` function
  - [ ] Handle special characters
  - [ ] Enforce length limits
  - [ ] Test with edge cases (emoji, unicode, etc.)
- [ ] Create `downloadMarkdown(content, filename)` function
  - [ ] Implement Blob creation with UTF-8 encoding
  - [ ] Handle browser-specific download triggers
  - [ ] Clean up URLs after download
- [ ] Add download button to transcript modal UI
  - [ ] Design button placement and styling
  - [ ] Add appropriate icon
  - [ ] Wire up click handler
- [ ] Testing
  - [ ] Test across Chrome, Firefox, Safari, Edge
  - [ ] Test with various podcast names (special chars, long names)
  - [ ] Test with missing metadata fields (graceful fallbacks)
  - [ ] Test with very long transcripts
  - [ ] Test UTF-8 encoding with international characters
  - [ ] Test filename sanitization edge cases

## Testing Strategy

### Unit Tests
- Filename sanitization with various inputs
- Markdown formatting with complete/incomplete metadata
- Date formatting consistency
- Speaker attribution formatting

### Integration Tests
- End-to-end download flow
- File content verification
- Cross-browser compatibility

### Edge Cases
- Episodes with no description
- Episodes with no speaker data
- Very long episode titles (>200 chars)
- Special characters in metadata (quotes, apostrophes, unicode)
- Empty transcripts
- Transcripts with unusual formatting

## UI/UX Considerations

1. **Visual Feedback**
   - Show brief success message/toast after download
   - Button should indicate loading state if processing takes time

2. **Accessibility**
   - Button must be keyboard accessible
   - Include proper ARIA labels
   - Screen reader should announce "Download transcript as markdown file"

3. **Mobile Behavior**
   - On mobile browsers, trigger native file download/share sheet
   - Consider alternative on iOS (copy to clipboard as fallback)

## Success Metrics

- Download completion rate (% of users who click download)
- No reported issues with file encoding
- Files open correctly in popular markdown editors
- Zero sanitization-related filename errors

## Future Enhancements

- [ ] Allow users to customize markdown template
- [ ] Add option to include/exclude frontmatter
- [ ] Support multiple markdown flavors (CommonMark, GFM, etc.)
- [ ] Batch download multiple episodes
- [ ] Include embedded timestamps in transcript
- [ ] Add table of contents for long transcripts

## Related Specs

- SPEC-011: Export to Plain Text
- SPEC-012: Export to PDF
- SPEC-013: Batch Export Functionality

## References

- [YAML Frontmatter Spec](https://jekyllrb.com/docs/front-matter/)
- [CommonMark Specification](https://commonmark.org/)
- [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API)
- [Blob API Documentation](https://developer.mozilla.org/en-US/docs/Web/API/Blob)

---

**Last Updated:** 2025-01-26
**Author:** Engineering Team
**Reviewers:** TBD
