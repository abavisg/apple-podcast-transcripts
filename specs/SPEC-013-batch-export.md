# SPEC-013: Batch Export Functionality

**Feature ID:** SPEC-013
**Priority:** Low
**Complexity:** Low-Medium
**Status:** Planned
**Dependencies:** SPEC-001, SPEC-011, SPEC-012
**Estimated Effort:** 2-3 days

---

## Overview

Allow users to select multiple episodes and export them all at once in their chosen format (markdown, text, or PDF).

## Acceptance Criteria

- [ ] Checkbox selection UI on episode cards
- [ ] "Select All" / "Deselect All" buttons
- [ ] Export format dropdown (MD, TXT, PDF)
- [ ] "Export Selected" button
- [ ] Progress indicator during batch export
- [ ] Download as ZIP file containing all exports
- [ ] Option to export with/without metadata

## Technical Implementation

```javascript
import JSZip from 'jszip';

async function batchExport(episodes, format) {
  const zip = new JSZip();

  for (const episode of episodes) {
    let content, filename;

    switch(format) {
      case 'markdown':
        content = formatAsMarkdown(episode);
        filename = `${sanitizeFilename(episode.title)}.md`;
        break;
      case 'text':
        content = formatAsPlainText(episode);
        filename = `${sanitizeFilename(episode.title)}.txt`;
        break;
      case 'pdf':
        // Note: PDF requires different handling
        content = await generatePDF(episode);
        filename = `${sanitizeFilename(episode.title)}.pdf`;
        break;
    }

    zip.file(filename, content);
  }

  const blob = await zip.generateAsync({ type: 'blob' });
  const url = URL.createObjectURL(blob);

  const link = document.createElement('a');
  link.href = url;
  link.download = `podcast-transcripts-${Date.now()}.zip`;
  link.click();

  URL.revokeObjectURL(url);
}
```

## UI Design

```
┌──────────────────────────────────────────┐
│  ☑ Select All  ☐ Episode 1              │
│                ☑ Episode 2              │
│                ☑ Episode 3              │
│                                          │
│  3 selected                              │
│  Export as: [▼ Markdown  ]  [Export]    │
└──────────────────────────────────────────┘
```

## Related Specs

- SPEC-001: Markdown Export (dependency)
- SPEC-011: Export to Plain Text (dependency)
- SPEC-012: Export to PDF (dependency)

## References

- [JSZip Library](https://stuk.github.io/jszip/)

---

**Last Updated:** 2025-01-26
