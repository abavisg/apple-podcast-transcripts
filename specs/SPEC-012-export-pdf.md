# SPEC-012: Export to PDF

**Feature ID:** SPEC-012
**Priority:** Low
**Complexity:** Medium
**Status:** Planned
**Dependencies:** None
**Estimated Effort:** 3-4 days

---

## Overview

Enable export of transcripts as formatted PDF files with proper typography, pagination, and metadata.

## Acceptance Criteria

- [ ] "Download as PDF" button in transcript modal
- [ ] Professional formatting (headers, spacing, page breaks)
- [ ] Metadata on first page
- [ ] Page numbers
- [ ] Table of contents (for long transcripts)
- [ ] Responsive font sizing
- [ ] Works across browsers

## Technical Implementation

Use **jsPDF** or browser's **print-to-PDF** via window.print():

### Option 1: jsPDF Library

```javascript
import jsPDF from 'jspdf';

function exportToPDF(episode) {
  const doc = new jsPDF();

  // Add title page
  doc.setFontSize(20);
  doc.text(episode.title, 20, 20);

  doc.setFontSize(12);
  doc.text(`Podcast: ${episode.podcastName}`, 20, 35);
  doc.text(`Host: ${episode.author}`, 20, 42);
  doc.text(`Date: ${formatDate(episode.date)}`, 20, 49);

  // Add transcript (with pagination)
  doc.addPage();
  doc.setFontSize(11);
  const lines = doc.splitTextToSize(episode.transcript, 170);
  doc.text(lines, 20, 20);

  // Save
  doc.save(`${sanitizeFilename(episode.title)}.pdf`);
}
```

### Option 2: Print to PDF (Native)

```javascript
function printToPDF(episode) {
  // Create styled HTML view
  const printWindow = window.open('', '_blank');
  printWindow.document.write(`
    <html>
      <head>
        <title>${episode.title}</title>
        <style>
          @page { size: A4; margin: 2cm; }
          body { font-family: serif; line-height: 1.6; }
          h1 { page-break-before: always; }
          .metadata { margin-bottom: 2cm; }
        </style>
      </head>
      <body>
        <div class="metadata">
          <h1>${episode.title}</h1>
          <p><strong>Podcast:</strong> ${episode.podcastName}</p>
          <p><strong>Host:</strong> ${episode.author}</p>
        </div>
        <div class="transcript">${episode.transcript}</div>
      </body>
    </html>
  `);
  printWindow.document.close();
  printWindow.print();
}
```

## Related Specs

- SPEC-001: Markdown Export
- SPEC-011: Export to Plain Text
- SPEC-013: Batch Export

## References

- [jsPDF](https://github.com/parallax/jsPDF)
- [Print API](https://developer.mozilla.org/en-US/docs/Web/API/Window/print)

---

**Last Updated:** 2025-01-26
